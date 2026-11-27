---
title: "Week 10 Worklog"
date: 2026-11-16
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

### Week 10 objectives

* Measure with numbers three things that had only been checked by unit tests: PII masking (AI-31), the OTP rules (AI-30) and readability of explanations (AI-32).
* Re-verify the 20 hotlines before their 30-day `verified_at` expires (SH-05).
* Not wait for the BE infrastructure: build my own AWS account to run the real `ai` Lambda with S3, DynamoDB, SSM and Telegram, and wire up `PIPELINE_IMPL=real` (INT-01, handed to me by AI1 on 19/11).

### Tasks this week

| Day | Task | Start date | Completion date | Reference |
| --- | --- | --- | --- | --- |
| Mon | - Open the bot wording PR: the bot calls itself "cháu", a call-family button, an easy-to-read checklist (SH-08, #157) <br> - Open the 500-sample mask benchmark PR (AI-31, #158) and the OTP rule benchmark on real OTP and scam messages (AI-30, #159) | 16/11/2026 | 17/11/2026 | [#157](https://github.com/ask-first-fcaj/ask-first/pull/157), [#158](https://github.com/ask-first-fcaj/ask-first/pull/158), [#159](https://github.com/ask-first-fcaj/ask-first/pull/159) |
| Tue | - Fix the mask: drop the keyword "mấy số" because it collides with "máy số" once diacritics are removed; merge #158, #159 <br> - Reopen the `source_url` of all 20 hotlines, update the MobiFone number, merge #168 <br> - Review AI1's PRs: SH-09/SH-10 in the plan (#161), fuse thresholds (#163), M4 eval report (#164), the `R_OVERPAY_REFUND` rule (#166), `judge_v3` (#167), Bedrock token measurement (#169) | 17/11/2026 | 17/11/2026 | [#168](https://github.com/ask-first-fcaj/ask-first/pull/168), [commit 3216682](https://github.com/ask-first-fcaj/ask-first/commit/3216682), [#161](https://github.com/ask-first-fcaj/ask-first/pull/161), [#163](https://github.com/ask-first-fcaj/ask-first/pull/163), [#164](https://github.com/ask-first-fcaj/ask-first/pull/164), [#166](https://github.com/ask-first-fcaj/ask-first/pull/166), [#167](https://github.com/ask-first-fcaj/ask-first/pull/167), [#169](https://github.com/ask-first-fcaj/ask-first/pull/169) |
| Wed | - Plan tests T0–T13 on my own account; prepare the account: IAM user, root MFA, 3 budgets, a test Telegram bot <!-- NHÁP: chỉ có trong nhật ký cá nhân, chưa có link công khai --> | 18/11/2026 | 19/11/2026 | [Comment on #39](https://github.com/ask-first-fcaj/ask-first/issues/39#issuecomment-7126855221) |
| Thu | - Set up S3 (model artifacts with sha256 checks), DynamoDB (cache with TTL), an SSM `SecureString` for the bot token, ECR + an image-based `ai` Lambda <br> - Wire the `real` `init_runtime` to S3, DynamoDB and Telegram; preload RAG and OCR in the INIT phase; open PR #173 <br> - Invoke the Lambda with fixtures, measure cold/warm and compare 3 memory sizes 1769/2048/3008 MB <br> - Open the readability script PR (AI-32, #172) | 19/11/2026 | 19/11/2026 | [PR #173](https://github.com/ask-first-fcaj/ask-first/pull/173), [Comment on #39](https://github.com/ask-first-fcaj/ask-first/issues/39#issuecomment-7126855221), [PR #172](https://github.com/ask-first-fcaj/ask-first/pull/172) |
| Fri | - Remove duplicate quotes in "Why the bot is suspicious" and deploy to the test Lambda (AI-06, #174) <br> - Wire `url_net` into `RealPipeline` to expand short links (AI-24b, #175) <br> - DLQ test: block `ai` (concurrency 0), watch `worker` retry 5 times and push to the DLQ; report 2 bugs to BE1 in #42 and #43 <br> - Check the real cost in Cost Explorer | 20/11/2026 | 20/11/2026 | [#174](https://github.com/ask-first-fcaj/ask-first/pull/174), [#175](https://github.com/ask-first-fcaj/ask-first/pull/175), [Comment on #42](https://github.com/ask-first-fcaj/ask-first/issues/42#issuecomment-7030554738), [Comment on #43](https://github.com/ask-first-fcaj/ask-first/issues/43#issuecomment-7030554190), [Cost Explorer](https://docs.aws.amazon.com/cost-management/latest/userguide/ce-what-is.html) |

### Results

* 500-sample PII mask benchmark ([#158](https://github.com/ask-first-fcaj/ask-first/pull/158), [mask-pii.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/mask-pii.md)): heldout recall rose from 101/132 to 130/132; false masking on trap samples stayed at 1/20.
* OTP rules ([#159](https://github.com/ask-first-fcaj/ask-first/pull/159), [otp-rules.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/otp-rules.md)): real OTP messages wrongly triggering `R_OTP_REQUEST` 1/100; scam messages missed 0/100.
* Readability ([#172](https://github.com/ask-first-fcaj/ask-first/pull/172), [readability.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/readability.md)): 0/99 explanations longer than 600 characters, but 25/99 have an average sentence over 20 words and 20/99 contain English other than "OTP"/"link".
* The `ai` Lambda on my own account, 3008 MB, x86_64, `llm_enabled=false` ([#173](https://github.com/ask-first-fcaj/ask-first/pull/173)):

| Metric | Before preloading at INIT | After preloading at INIT |
| --- | --- | --- |
| First text message after a cold start | 17.1 s | 6.6 s |
| First image after a cold start | ~19 s | 2.5 s (Init 6.2 s) |

* Memory comparison ([#173](https://github.com/ask-first-fcaj/ask-first/pull/173)): 2048 MB Init 7.1 s; at 1769 MB Init hit the 10 s ceiling and the first message took 35 s, so the `ai` Lambda needs at least 2048 MB; the team chose 3008 MB.
* Cost measured in Cost Explorer for 01–20/11: 0.0025 USD in total before credits, 0 USD paid. <!-- NHÁP: số chi phí chỉ có trong nhật ký cá nhân (ai2-quan-ly-cong-viec.md, T13), chưa có link công khai; Minh cân nhắc dán ảnh Cost Explorer -->
* Reviewed 6 of AI1's PRs on 17/11.

### Lessons learned

* Only measuring on a real Lambda revealed the 17–19 s cold start; the dev machine does not have this problem. Preloading models at INIT makes every cold start longer, in exchange for a first message that does not wait long ([#173](https://github.com/ask-first-fcaj/ask-first/pull/173)).
* The Lambda INIT phase has a 10 s ceiling; at 1769 MB (about 1 vCPU) the models cannot load in time. Memory is the only way to get more CPU.
* Testing the DLQ by deliberately blocking the Lambda uncovered a bug that sent the "busy" message twice, which unit tests did not catch ([#42](https://github.com/ask-first-fcaj/ask-first/issues/42#issuecomment-7030554738)).
