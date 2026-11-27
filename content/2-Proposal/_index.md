---
title: "Proposal"
date: 2026-11-27
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Project Proposal: Ask First (Hỏi Trước)

*Author: Võ Hoàng Quốc Minh, AI2 member of the team. This proposal presents our team project from the angle of the parts I was responsible for: the AI pipeline running locally inside Lambda, the measurement and evaluation data, and the shared AWS environment of the team.*

### 1. Problem and solution overview

**The problem in one sentence:** an elderly person receives a message about a "traffic fine", an "insurance refund" or a "police summons" and needs someone to tell them whether to act on it, before they tap the link or read out an OTP code.

**The solution:** a Telegram bot called *Hỏi Trước* ("Ask First"). Users forward a message, paste a link or send a screenshot. The bot replies with a short Vietnamese verdict: a risk level, reasons grounded in the wording of the message itself, what to do next, and related official warnings. If the user has consented to link a relative, that relative is notified when the message is high risk.

Three points shape the design, and they are also the three areas I worked on most:

1. **Most of the "understanding" runs locally inside Lambda**, with no per-token cost: reading text from images (OCR), masking personal data, rule-based scoring, URL analysis, and retrieving related warnings (RAG). Only the final classification step calls an open-weight LLM on Amazon Bedrock, and only with masked text.
2. **Every AI change must be backed by measurements**: each component has a dataset, a re-runnable measurement script and a clear pass threshold; evaluation runs in CI to block PRs that lower quality.
3. **Everything runs serverless on AWS**, with no always-on servers and no VPC/NAT; cost follows the number of checks.

| Item | Details |
|---|---|
| Channel | Telegram Bot API, webhook |
| Team | 4 people: BE1, BE2 (Java 21), AI1, AI2 (Python 3.12) |
| Timeframe | 6 weeks, 19/10/2026 – 29/11/2026 |
| Region | Lambda, SQS, DynamoDB, S3 in `ap-southeast-1`; Bedrock in `ap-southeast-2` |
| Source code | [ask-first-fcaj/ask-first](https://github.com/ask-first-fcaj/ask-first) |

### 2. Problem statement

**Scam messages are written to make the reader act immediately.** A fake message usually has three parts: a worrying reason (unpaid bills, a traffic fine, a relative in trouble), time pressure ("within 24 hours"), and a harmful action (tap a link, install an app, read out an OTP, transfer money). By the time the reader performs the third part, it is usually too late.

**Elderly people find it hard to check by themselves.** They struggle to tell a real domain from a look-alike, rarely know a bank's official hotline, and often receive these messages when no family member is around. Banks mostly intervene at the transfer step, which is after the scammer has already done the persuading.

**Three technical problems I had to solve in the AI part:**

| Problem | Why it is hard | How the project handles it |
|---|---|---|
| Most scams arrive as screenshots | Images contain accented Vietnamese; URLs wrap across lines or lose their first characters | Local OCR, regex-based URL repair, re-reading Vietnamese lines with VietOCR |
| Messages contain the user's phone number, bank account, ID number, OTP | Personal data must not leave Lambda | Mask PII into tokens (`[SĐT_1]`, `[OTP_1]`) before any Bedrock call |
| The LLM may hallucinate, be slow, or fail | The bot must never go silent or give a misleading answer | A rule layer independent of the LLM with a score floor; RAG retrieves real warnings to cite |

### 3. Objectives and scope

**The overall goal** is an MVP running for real on AWS, ready for the demo on 27/11/2026 and the workshop submission on 29/11/2026.

**Measurable success criteria** (thresholds set up front in `docs/system-architecture.md` §1.3 and the gates in `docs/implementation-plan.md` §4):

| Area | Metric | Pass threshold | Measured with |
|---|---|---|---|
| Classification quality | Recall on scam messages | ≥ 90% | `eval/run_eval.py`, enforced in CI |
| Classification quality | False alarms on benign-but-suspicious messages | ≤ 10% | same |
| OCR (gate G2) | Correct URL transcription on an independent image set | ≥ 90% | `eval/ocr_benchmark.py` |
| OCR (gate G2) | CER | ≤ 10% | same |
| RAG (milestone M3) | Scenario card Recall@4 on `real_scam` | ≥ 90% | RAG eval |
| RAG (milestone M3) | Query time | < 100 ms | measured on Lambda |
| Latency | End-to-end p95 with a warm container, text and images | ≤ 15 seconds | CloudWatch, `latency_ms` logs |
| Cost | Per check | ≤ 0.005 USD | actual token counts |
| Privacy | Eval samples leaking numbers or OTPs into the prompt | 0 | security tests for `mask()` |

**In MVP scope:**

- Inputs: text, forwarded messages, links, screenshots and albums; consecutive messages are batched.
- AI pipeline: normalization, OCR, PII masking, rules, URL analysis (including SSRF-safe expansion of short links), RAG, Guardrails, LLM judge, fusion, template-based replies.
- Users: explicit consent, consent withdrawal, data deletion, linking and alerting relatives, management through chat commands.
- Operations: kill switch to fall back to rules-only mode, DLQ alarm, budgets.

**Out of scope for the 6 weeks** (postponed in plan v3): voice/ASR, VLM for hard images, multi-turn follow-up questions, Telegram Mini App, Phase 2 (living knowledge base), pilot with households outside the team.

**My share of this scope:** the image set and OCR spike (AI-20), PII masking (AI-22), normalization and rules (AI-23), URL intel (AI-24a, wiring `url_net` in AI-24b), knowledge base and validator (AI-25), Telegram media download and `S3ArtifactStore` (AI-03b), local RAG (AI-29, INT-03), eval in CI (AI-09), VietOCR re-reading of Vietnamese (AI-26), wiring `PIPELINE_IMPL=real` to S3, DynamoDB and Telegram (INT-01), and building the team's shared AWS environment.

### 4. Solution architecture

![Ask First architecture on AWS: Telegram calls the webhook Lambda through a Function URL, SQS passes jobs to the worker Lambda, the worker invokes the ai Lambda (local AI pipeline), and ai calls Amazon Bedrock in Sydney; data in DynamoDB and S3, monitoring with CloudWatch and SNS](/images/architecture.png)

*The team's shared diagram, showing only the MVP that is running.*

#### 4.1 Three Lambdas, three responsibilities

- `webhook` (Java): receives updates, checks `secret_token`, deduplicates, batches messages, returns 200 to Telegram quickly and pushes a job to SQS.
- `worker` (Java): reads the job, invokes `ai`, sends the verdict, alerts relatives, writes metadata.
- `ai` (Python, 3008 MB container image): the whole AI pipeline. This is the part I wrote and operated the most.

The Java ↔ Python boundary is a JSON Schema contract (gate G0). Thanks to it, the AI side could be developed and measured entirely in Python without waiting for the backend.

#### 4.2 The AI pipeline inside the `ai` Lambda

| Step | Runs where | What it does | Key measurement (source) |
|---|---|---|---|
| Media download | `ai` Lambda | Fetches the image from Telegram by `file_id`, kept only in memory and `/tmp`; images never pass through the Java payload | AI-03b |
| OCR | Local, ONNX | RapidOCR (PP-OCRv6 small) detects lines and reads URLs/phone numbers; VietOCR re-reads accented lines; regex repairs URL prefixes | Independent 16-image set: URLs 17/18 (94%), CER 7.2% ([ocr-spike](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/ocr-spike.md)); correctly accented words 37% → 89% with VietOCR ([ocr-vietocr](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/ocr-vietocr.md)) |
| PII masking | Local, regex + keywords | Phone, bank account, ID number, OTP, email, numbers written in words → tokens | Held-out: 130/132 PII items masked ([mask-pii](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/mask-pii.md)) |
| Rules | Local, `rules.yaml` | Signals such as OTP requests, urgency, impersonating authorities; a score floor independent of the LLM | OTP rules catch 100/100 scams, 1/100 false alarm on genuine messages ([otp-rules](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/otp-rules.md)) |
| URL intel | Local + controlled network | Extracts domains, detects brand look-alikes, expands short links via SSRF-safe `url_net` | AI-24a, AI-24b (PR #175) |
| RAG | Local, e5-small int8 | Finds the closest scenario cards and `.gov.vn` warnings by in-memory cosine similarity | Dev machine: Recall@4 35/38 = 92.1%; on Lambda: p95 45 ms ([lambda-p95](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/lambda-p95.md)) |
| Guard + Judge | Bedrock (Sydney) | Guardrails `PROMPT_ATTACK`, then Qwen3 32B returns structured JSON; gpt-oss-120b as fallback | Text p95 3.5 s with a warm container |
| Fuse + Respond | Local | Combines rule and LLM scores into 4 levels, replies from templates, never lets the LLM write freely | No message over 600 characters ([readability](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/readability.md)) |

OCR and RAG models are preloaded in the Lambda INIT phase (PR #173), so the first request after a cold start does not wait for model loading. Weights live on S3 with checksums; swapping a model does not require rebuilding the image.

#### 4.3 AWS services and why they were chosen

| Service | Used for | Why chosen, alternatives rejected |
|---|---|---|
| AWS Lambda (Function URL) | The 3 functions `webhook`, `worker`, `ai` | Low, irregular load, pay per request. A Function URL is enough for the webhook, so no API Gateway |
| Lambda container image + Amazon ECR | Packaging onnxruntime, RapidOCR, VietOCR, the embedder | Zip packages are capped at 250 MB, too small for ML libraries; images go up to 10 GB, the team set its own 2 GB cap |
| Amazon SQS + DLQ | `webhook` → `worker` queue, batching via `DelaySeconds` | Decouples the Telegram reply from slow processing; built-in retries; the DLQ keeps failed jobs for manual handling |
| Amazon DynamoDB (on-demand) | Users, consent, relatives, check metadata, `CACHE#` and `URL#` caches, `FLAGS` | Key-based access, TTL for automatic deletion, no capacity management |
| Amazon S3 | OCR/embedding weights, the RAG index, donated samples auto-deleted after 30 days | Cheap, versions and checksums for artifacts |
| Amazon Bedrock + Guardrails | LLM judge and prompt injection filtering | Open-weight models priced per token, no GPU operations. EC2/SageMaker GPU rejected as far more expensive at this load (ARCH §6.0 estimates about 530 USD/month for self-hosting) |
| SSM Parameter Store | Bot token, webhook secret | No secrets in code or environment variables |
| CloudWatch + SNS | JSON logs keyed by `trace_id`, DLQ alarm by email | Enough for MVP scale |
| IAM | Per-function roles; `ai` can only read/write `CACHE#` and `URL#` keys | Least privilege |

Not used: VPC/NAT, Step Functions, OpenSearch or a dedicated vector DB (the corpus is about 100 articles, in-memory cosine is enough), Textract and Transcribe (local OCR exists, voice is postponed).

#### 4.4 The team's shared AWS environment

From 19/11/2026, when AI1's account could only use Bedrock, I built the AWS side on my own account. On 24/11/2026 the team agreed to use this account as the shared runtime environment. Bedrock stays in AI1's account, and the `ai` Lambda reaches it through a role (`BEDROCK_ROLE_ARN`).

The steps I carried out and verified, in order:

| Step | Content |
|---|---|
| T0 | Dedicated IAM user, MFA on root, AWS Budgets with email alerts, a test Telegram bot |
| T4–T6 | S3 bucket for artifacts, DynamoDB table for caching, SSM for the bot token, test image download from Telegram |
| T7–T9 | Build the image, create the `ai` Lambda, wire `PIPELINE_IMPL=real` (PR #173), invoke with fixtures from `contracts/fixtures/` |
| T10–T11 | Wire `url_net`, run the full chain `webhook` → `worker` → `ai` → Telegram with the backend's SAM stack |
| T12–T13 | Measure cost, prepare cleanup following the team's clean-up guide |

Some measurements taken while building the environment drove the configuration: with RAG and OCR preloaded, `ai` Init is 6.2 s at 3008 MB, 7.1 s at 2048 MB, and hits the 10 s limit at 1769 MB. I therefore recommended keeping `ai` at 3008 MB with at least 512 MB of `/tmp`.

### 5. Plan and timeline

Plan v3 compressed the project from 12 weeks to 6 weeks (19/10 – 29/11/2026). The backend, AI and shared tracks run in parallel and meet at gates and milestones.

**Gates (decision points):**

| Gate | Due | Condition | My part |
|---|---|---|---|
| G0: Contracts | W1, 23/10 | `models.py` schema, fixtures, `StubPipeline`, contract tests green in CI | Read and used the contract to build a `Bundle` from Telegram messages |
| G1: LLM choice | W2, 30/10 | On 100 samples: valid JSON ≥ 98%, p95 ≤ 5 s, preliminary recall ≥ 85% | Led by AI1; the team chose Qwen3 32B (ADR-002) |
| G2: OCR choice | W2, 30/10 | Correct URLs ≥ 90%, CER ≤ 10% on an independent image set | Built 50 images, ran 3 OCR stacks, checked 16 more independent images; these measurements were the basis for AI1's decision to use RapidOCR (ADR-003) |

**Milestones:**

| Milestone | Week | Content | My part |
|---|---|---|---|
| M1: Text end-to-end | W3 (06/11) | A "traffic fine" message with a fake link → a real verdict | PII masking, rules, URL intel for the text pipeline |
| M2: Images + relatives | W4 (13/11) | Image → local OCR → verdict; alerting relatives | Media download, `S3ArtifactStore`, URL repair in OCR |
| M3: RAG | W5 (20/11) | Verdicts cite official warnings | e5-small embedder, retriever, `kb/alerts.yaml` KB, wiring RAG into `ai` |
| M4: MVP freeze | W5 (20/11) | All MVP-level FRs, eval running in CI | Eval gate in CI, 500-sample mask measurement, OTP rule measurement, readability measurement |
| M5: Internal testing | W6 (until 25/11) | Team and family members try the bot | Operating the shared environment, DLQ testing, fixing integration bugs |
| M6: Presentation | W6 (27/11) | Slides, demo, eval and cost report | OCR, VietOCR and Lambda latency figures |
| M7: Workshop submission | W6 (29/11) | Each member submits their own site with a worklog | This site |

After M4 the team only fixes bugs, tunes prompts or thresholds (through the eval gate) and does infrastructure work. The further cut order in case of delay is already written in `implementation-plan.md` §9.

### 6. Cost estimate

**Load assumptions** (from `docs/system-architecture.md` §12 and `docs/results/cost-estimate.md`):

- 3,000 checks per month: about 2,000 text messages and 1,000 images; no voice or VLM.
- 25% of checks answered by the fast path or cache, leaving 2,250 LLM calls. The 25% rate is **not verified**.
- Each LLM check costs 0.000792 USD in tokens (measured in the final eval with the `judge_v3` prompt, including escalation to the fallback model).
- `ai` Lambda 5 s per check at 3008 MB (rounded up from dev-machine measurements); `worker` 5 s, `webhook` 0.5 s at 512 MB.
- Services other than Bedrock are priced with the [AWS Pricing Calculator](https://calculator.aws/#/estimate?id=073f413f6ac370de48bf3bff0ba10a7b870ae807) (24/11/2026, `ap-southeast-1`, "Without Free Tier" mode). Bedrock is calculated by hand because the Calculator does not offer Qwen3 in Sydney.

The table below groups costs by architectural role, showing that the local AI part costs almost nothing in tokens:

| Group | Service | Without Free Tier (USD/month) | With always-free (USD/month) | Source |
|---|---|---|---|---|
| LLM inference | Bedrock Qwen3 32B + gpt-oss-120b | 1.78 | 1.78 | By hand: 2,250 × 0.000792 |
| LLM inference | Bedrock Guardrails | 0.34 | 0.34 | By hand: 2,250 × 0.15 USD/1,000 text units |
| Local AI | `ai` Lambda (OCR, masking, rules, RAG) | 0.73 | 0 | Pricing Calculator |
| Local AI | ECR (`ai` image ≈ 2 GB) | 0.20 | 0.20 | Pricing Calculator |
| Local AI | S3 (artifacts, index, donated samples) | 0.03 | 0.03 | Pricing Calculator |
| Orchestration | `worker` + `webhook` Lambdas | 0.16 | 0 | Pricing Calculator |
| Orchestration | SQS (including long polling on an empty queue) | 0.28 | 0 | Pricing Calculator |
| Data | DynamoDB on-demand | 0.29 | < 0.05 | Pricing Calculator |
| Monitoring | CloudWatch (logs, metrics, alarms, dashboard) | 2.35 | 0 | Pricing Calculator |
| Monitoring | SNS, SSM Parameter Store | ≈ 0 | 0 | Not entered in the Calculator |
| **Total** | | **≈ 6.2** | **≈ 2.4** | `docs/results/cost-estimate.md` |

Reading the table:

- The Calculator part (excluding Bedrock) is 4.04 USD/month, i.e. 48.48 USD for 12 months, with no upfront cost.
- If every check called the LLM, Bedrock would rise to about 2.7 USD/month; with p95 token counts on top, about 4.6 USD/month. Both stay far below the 50 USD/month ceiling.
- The table excludes tax; the real bill adds about 10% (checked against the team's Cost Explorer).
- Comparison in ARCH §12: the v1 option with Claude + Transcribe + Cohere was estimated at 5–15 USD/month, self-hosted GPU at about 530 USD/month.

**Keeping costs low:** local-first AI (OCR, embeddings and rules have no token cost); fast path and cache skip the LLM when the rules are already confident; the `FLAGS` kill switch turns off the judge or image intake under spam; per-user rate limits; S3 lifecycle deletes `donated/` after 30 days; proposed additions are `RetentionInDays: 7` for logs and a lifecycle policy for the ECR repository.

### 7. Risks and mitigation

| Risk | How we would notice | Mitigation |
|---|---|---|
| OCR misreads URLs or drops Vietnamese accents, so rules do not fire | Correct URL or accented word rates drop on the image sets | Regex URL repair keeping both original and repaired versions; VietOCR re-reads accented lines; re-measure on the 16-image independent set before deciding |
| Optimistic numbers because rules and regexes were tuned on the same data | Good results on dev data, worse on new data | Dev/held-out split (PII masking 349/151), the independent OCR set run exactly once, limitations stated in every report |
| Personal data leaking to Bedrock | A `mask()` test finds a leaked number | PII masking written as security tests; 12-digit ID numbers are masked even when they look like order codes (false masking accepted) |
| Cold start and image latency above 15 s | `ai` Init close to the 10 s limit; measured image p95 of 17.0 s | Load models in INIT, keep 3008 MB; the team accepted up to about 30 s for images in the MVP and logged it for later optimization |
| LLM failure, throttling, or prompt injection | Bedrock errors in logs, Guardrails detecting attacks | Fallback model, then rules-only mode; Guardrails `PROMPT_ATTACK`; LLM output only goes through templates |
| Changes to prompts, KB or rules lowering quality | Eval CI turns red | Eval gate blocks merges when recall < 90% or false alarms > 10% |
| The shared environment depends on a personal account | Lost access or forgotten resources | Budgets with several email alert levels; a recorded resource list; cleanup order with S3 and ECR before `sam delete` |
| Small, mostly self-written eval set | No real user data from outside the team | Limitations disclosed in reports; keep the consented sample donation flow with 30-day auto-deletion |
