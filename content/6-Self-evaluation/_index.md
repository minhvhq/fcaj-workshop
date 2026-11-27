---
title: "Self-Assessment"
date: 2026-11-27
weight: 6
chapter: false
pre: " <b> 6. </b> "
---

From 14/09/2026 to 14/12/2026 I interned at Amazon Web Services Vietnam Company Limited in the First Cloud AI Journey program, as AI2 on the Ask First (Hỏi Trước) project, a Telegram bot that helps elderly people check suspected scam messages. My main work covered Vietnamese OCR, personal-data masking, the rules layer, RAG, evaluation measurements, wiring the real pipeline on Lambda, and building the team's shared AWS environment. I opened 26 PRs in the team repository, 24 of which were merged.

The table below is my self-assessment against the 8 FCAJ criteria. I tried to record the weak spots too, so I can do better next time.

| # | Criterion | Level | Comments and evidence |
|---|---|---|---|
| 1 | Professional knowledge | Fair | Used Lambda container images, S3, DynamoDB, Bedrock and ONNX Runtime in real work. Evidence: loading models from S3 with sha256 checks in the INIT phase ([PR #173](https://github.com/ask-first-fcaj/ask-first/pull/173)), VietOCR ONNX re-reading Vietnamese lines ([PR #178](https://github.com/ask-first-fcaj/ask-first/pull/178)). Still weak on IAM and IaC: some permissions are still granted by hand outside the template. |
| 2 | Self-learning and research | Good | Found on my own that the PP-OCRv6 model lacks 88 Vietnamese characters by reading the ONNX file metadata, then exported VietOCR to ONNX myself to keep PyTorch out of the image. Correct accented words rose from 37% to 89% (50-image set) and from 27% to 97% (16-image set). |
| 3 | Proactiveness | Good | Built and operated the team's shared AWS environment on a separate account from 19/11, tested each component and reported backend integration bugs (issues #42, #43, #85). Proposed measuring on a real Lambda with a temporary function instead of only on a laptop. |
| 4 | Discipline and compliance | Fair | Each PR is tagged with a task ID (AI-20, AI-22, INT-01...) and comes with measurements. Weak points: in the first week I fell behind the plan on review and documentation; some PRs exceeded the roughly 300-line limit the team agreed on, which slowed review (e.g. [PR #173](https://github.com/ask-first-fcaj/ask-first/pull/173), opened 19/11, merged 23/11). |
| 5 | Communication | Fair | I write PR descriptions with context, method and results, and wrote an OCR blog post for the community. I still tend to write long and struggle to summarise in a few sentences for quick updates. |
| 6 | Teamwork | Fair | Reviewed teammates' PRs and worked with the backend side when wiring the real pipeline to S3, DynamoDB and Telegram. I still depend on review from my fellow AI teammate before merging, and sometimes waited instead of checking more myself. |
| 7 | Problem solving | Good | Instead of replacing the OCR model, I used two models, each doing what it is good at, merged word by word. Lost rule signals dropped from 15/33 to 6/33 while URLs and phone numbers stayed intact. Fixed domains broken across lines after a hyphen ([PR #176](https://github.com/ask-first-fcaj/ask-first/pull/176)). |
| 8 | Contribution to the Workshop | Fair | Contributed the real pipeline wiring, S3 model loading and the AWS environment the team uses to demo and write the workshop. Most of the workshop writing was done by a teammate. |

<!-- NHÁP: Minh adjusts levels and evidence (especially: PRs over 300 lines, slow first week, issue numbers #42/#43/#85) before submitting -->

### What I want to improve

- Split PRs smaller, one goal per PR, so reviews are faster.
- Learn IAM properly and move every permission into IaC.
- Practise short reports: problem, method, result, in no more than five sentences.
- Check my own work more thoroughly before asking for review, instead of relying on review to catch bugs.
