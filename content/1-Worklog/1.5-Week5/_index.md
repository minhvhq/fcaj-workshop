---
title: "Week 5 Worklog"
date: 2026-10-12
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

<!-- NHÁP: cả tuần 5 chưa có bằng chứng (trước khi có repo), Minh kiểm lại trước khi nộp -->

### Week 5 objectives

* Collect enough scam message screenshots to measure OCR once the repository opens.
* Agree on how to label images: reference text, URLs, phone numbers.
* Study the kinds of personal information (PII) that must be masked before a message goes to a model.

### Tasks this week

| Day | Task | Start date | Completion date | Reference |
| --- | --- | --- | --- | --- |
| Mon | - Collect scam screenshots from public warning articles and build extra simulated ones; remove or replace real numbers in the images | 12/10/2026 | 12/10/2026 | [Authority of Information Security](https://ais.gov.vn/) |
| Tue | - Write reference text for each image, and keep URLs and phone numbers in separate lists for later scoring | 13/10/2026 | 14/10/2026 | [CER (character error rate)](https://en.wikipedia.org/wiki/Word_error_rate) |
| Wed | - Read the team's draft research concept and architecture, comment on the image input and PII masking parts | 14/10/2026 | 14/10/2026 | [system-architecture.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/system-architecture.md) |
| Thu | - List PII commonly found in Vietnamese scam messages: OTPs, account numbers, phone numbers, national ID numbers; draft a regex for each | 15/10/2026 | 15/10/2026 | [Python `re`](https://docs.python.org/3/library/re.html) |
| Fri | - Read the basics of AWS SAM and GitHub Actions to prepare for working in the shared repository | 16/10/2026 | 16/10/2026 | [AWS SAM](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html), [GitHub Actions](https://docs.github.com/en/actions) |

### Results

* A draft image set with reference text is ready; the first 25 images were added to the repository in week 6 ([PR #96](https://github.com/ask-first-fcaj/ask-first/pull/96)).
* A list of PII types and trial regexes, the starting point for PII masking in week 7.

<!-- NHÁP: kết quả tuần 5 tự điền (trừ link PR #96), Minh kiểm lại -->

### Lessons learned

* Images from public sources can still contain real numbers, so each image must be checked before it goes into the repository.
* Labeling URLs and phone numbers separately from plain text makes it possible to measure exactly what the rules need.
