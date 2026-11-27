---
title: "Overview and Architecture"
date: 2026-11-27
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

#### The problem

Scam messages targeting older adults often look exactly like genuine ones: fake banks, fake police, fake "traffic camera fines", or "your child is in the emergency room, transfer money now". What they have in common is that they push the recipient to act immediately, before they have a chance to ask anyone. Ask First creates a place to **ask first**: the user forwards the content to the bot, and the bot replies within seconds.

The accompanying technical challenges:

- Users also send **screenshots**, so the system must be able to read Vietnamese text in images.
- The content contains phone numbers, account numbers, and OTP codes. Raw images **must never leave** our own infrastructure; only text with the numbers masked may be sent to the LLM.
- Traffic is low and irregular (a few hundred to a few thousand checks per month), so paying for a server running 24/7 is not an option.

#### Solution on AWS

The system is split into two parts:

- **Ingestion and orchestration** (Java 21, 3 Lambda functions): receives updates from Telegram, batches messages sent in quick succession, enqueues jobs, and sends results back to the user and their trusted contacts.
- **Analysis** (Python 3.12, 1 Lambda container image): reads text in images, masks personal information, runs the rule set, searches a knowledge base of scam scenarios, and only then asks an LLM on Amazon Bedrock if the rules are not confident enough.

#### Architecture diagram

![The team's Ask First architecture diagram: the user and Telegram on the left; Region ap-southeast-1 contains the webhook, worker, worker-dlq, and ai Lambda functions, SQS and DLQ, DynamoDB, S3, ECR; Bedrock and Bedrock Guardrails in ap-southeast-2; security (IAM, SSM, KMS) and monitoring (CloudWatch, CloudTrail, Budgets, Alarm, SNS) blocks at the bottom](/images/architecture.png)

#### The journey of a screenshot

The diagram has 17 numbered arrows. I group them into 5 stages following the path of one image:

| Stage | Arrows | What happens | Components |
|---|---|---|---|
| **A. Receive** | 1–3 | The user sends an image. Telegram POSTs the update to the `webhook` Function URL. Requests without the correct `X-Telegram-Bot-Api-Secret-Token` header are rejected with 401. `webhook` writes the message to the `BUFFER#<chat>` buffer in DynamoDB. | Lambda `webhook`, DynamoDB |
| **B. Enqueue** | 4–5 | `webhook` replies "⏳" to the user, pushes an `AnalyzeJob` to SQS with a 3-second delay (to batch messages sent in quick succession), then returns HTTP 200 to Telegram. `worker` picks up the job. | SQS `jobs`, Lambda `worker` |
| **C. Analyze** | 6–12 | `worker` invokes `ai` (alias `live`), sending only the `file_id`, not the image. `ai` downloads the image into memory itself using the bot token read from SSM, uses models loaded from S3 to run OCR, masks PII, runs the rules, probes shortened links, queries RAG, checks for prompt attacks with Guardrails, and then asks Bedrock. The verdict is cached for 24 hours in DynamoDB. | Lambda `ai`, S3, SSM, DynamoDB, Bedrock |
| **D. Reply** | 13–17 | `worker` edits the "⏳" message into the result, notifies trusted contacts if the risk is high, stores a `CHECK#` record (metadata only, 30-day TTL), and may schedule a follow-up question 15 minutes later. | Lambda `worker`, Telegram |
| **E. On failure** | (dashed line) | A job that fails 5 times goes to the DLQ. `worker-dlq` tells the user the bot is busy, and the CloudWatch alarm transitions to `ALARM` and sends an email through SNS. | SQS DLQ, `worker-dlq`, CloudWatch, SNS |

{{% notice note %}}
In the team's shared environment, Bedrock lives in **a different account** (AI1's account), so `ai` must call `sts:AssumeRole` into that account before calling Bedrock (arrow 11). When you follow this workshop in a single account, just enable the Bedrock models in your own account and **skip** the assume role step.
{{% /notice %}}

#### AWS services and why they were chosen

| Service | Used for | Why chosen, and what was considered |
|---|---|---|
| **AWS Lambda** (Java 21 zip) | `webhook`, `worker`, `worker-dlq` | Pay per invocation; Function URLs provide an HTTPS endpoint out of the box, so no API Gateway is needed. |
| **AWS Lambda** (container image) | `ai` | The OCR libraries and ONNX Runtime exceed the 250 MB limit of a zip package; images allow up to 10 GB. Runs on CPU, no GPU required. |
| **Amazon ECR** | Stores the `ai` image | Lambda container images can only be pulled from ECR. |
| **Amazon S3** | Models (OCR 32 MB, VietOCR 90 MB, embedder 123 MB) and the knowledge base index (0.3 MB) | Swapping a model does not require rebuilding the image; files are versioned by prefix, so rolling back is easy. |
| **Amazon SQS + DLQ** | Buffer between the ingestion part and the analysis part | Telegram receives HTTP 200 in under 1 second even though analysis takes several seconds; retries, the 3-second delay for batching messages, and moving failed jobs to the DLQ are all built-in SQS features. |
| **Amazon DynamoDB** | A single table for users, consent, message buffers, results, and cache | On-demand, TTL automatically deletes old data, zero cost when nobody is using it. |
| **Amazon Bedrock** + **Guardrails** | LLM judge (Qwen3 32B, with gpt-oss-120b as fallback) and prompt attack blocking | Pay per token, low-cost open-weight models; no need to operate GPUs ourselves. |
| **SSM Parameter Store** | Bot token, webhook secret (`SecureString`), stack configuration | Secrets are kept out of code and out of Lambda environment variables. |
| **CloudWatch + SNS** | JSON logs, metrics, DLQ alarm sending email | Available for every Lambda function, no agent to install. |
| **AWS SAM / CloudFormation** | Infrastructure as code (`infra/template.yaml`) | One command to build + deploy; the entire stack can be recreated. |
| **IAM** | A dedicated role per Lambda function, least privilege | For example, the `ai` role can only read the model bucket and a single SSM parameter. |

Not used: VPC and NAT Gateway (a NAT Gateway alone costs about 32 USD/month even with zero messages), API Gateway, Step Functions, EC2/SageMaker with GPUs.

#### Mapping to AWS Well-Architected

Each pillar comes with a piece of evidence I verified on the live environment (details in the following sections).

| Pillar | Design | Verified evidence |
|---|---|---|
| **Operational Excellence** | Backend stack in SAM; `ai` image tagged by commit; the `ai` Lambda has versions + a `live` alias so a rollback is one command. | The `live` alias has gone through 11 versions, each with the commit in its description (5.5). |
| **Security** | Function URL rejects requests missing the secret; secrets in SSM `SecureString`; least-privilege roles; S3 blocks public access; PII is masked before reaching Bedrock; logs contain no message content. | A POST without the secret returns 401 (5.6); scanning CloudWatch logs found no message content or tokens (5.7). |
| **Reliability** | SQS retries + DLQ, `worker-dlq` sends a busy notice, verdict cache, "rules only" mode when the LLM fails, RapidOCR results are still returned if VietOCR fails. | Blocking `ai` with reserved concurrency 0: the job was tried 5 times, went to the DLQ, and the user received exactly one "busy" message (5.7). |
| **Performance Efficiency** | `ai` at 3008 MB (about 1.7 vCPU); models preloaded during the INIT phase; small models running on CPU (ONNX). | Measured 3 memory settings: 1769 MB hit the 10-second INIT limit, 3008 MB had a 6.2-second INIT (5.7). |
| **Cost Optimization** | No hourly-billed components; DynamoDB on-demand; LLM skipped when the rules are already confident. | Actual cost for the whole account from 1–26/11/2026: about 0.10 USD before credits (5.9). |
| **Sustainability** | No machine is kept running while waiting for work; when the bot is idle, compute drops to zero as well. | Not scored in FCAJ; included to complete the checklist. |
