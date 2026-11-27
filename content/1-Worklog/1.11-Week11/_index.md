---
title: "Week 11 Worklog"
date: 2026-11-23
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

### Week 11 objectives

* Fix the OCR bug that drops Vietnamese characters with diacritics in screenshots (AI-26), measured on a real Lambda.
* Get last week's PRs (#173, #174, #175) into `main` and recheck them on Lambda.
* Test the whole `webhook` → `worker` → `ai` → Telegram chain with BE's fix (#170); settle on my account as the team's shared runtime environment.
* Write a technical blog post about Vietnamese OCR on Lambda and build the internship report site.

### Tasks this week

| Day | Task | Start date | Completion date | Reference |
| --- | --- | --- | --- | --- |
| Mon | - Fix `repair_urls`: join domains that OCR broke across lines right after a `-` (#176) <br> - Add VietOCR `vgg_seq2seq` (ONNX) to re-read lines with diacritics and merge results word by word; measure on 66 images and on a 3008 MB Lambda (#178) <br> - #173, #174, #175, #176 and #178 were merged; redeploy the `ai` Lambda and check URL intel and short links on Lambda <br> - Review BE1's message buffering and webhook parts in #170 | 23/11/2026 | 23/11/2026 | [#176](https://github.com/ask-first-fcaj/ask-first/pull/176), [#178](https://github.com/ask-first-fcaj/ask-first/pull/178), [ocr-vietocr.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/ocr-vietocr.md), [Comment on #170](https://github.com/ask-first-fcaj/ask-first/pull/170#issuecomment-7091667854) |
| Tue | - Deploy the #170 branch to the `askfirst-dev` stack on my account, test the whole chain from `webhook` and retest the DLQ; both #42 and #43 pass <br> - The team settles on my account as the shared AWS runtime environment <!-- NHÁP: quyết định này chưa có link công khai (chỉ có trong nhật ký cá nhân mục I.6), Minh kiểm lại --> <br> - #172 (readability) was merged <br> - Review AI1's PRs: less flaky rules engine timing test (#177), final W6 eval (#179), Maven cache (#180), `ai` Lambda image in SAM (#181), cross-account Bedrock calls (#182), latency on the team account (#183) | 24/11/2026 | 24/11/2026 | [Comment on #170](https://github.com/ask-first-fcaj/ask-first/pull/170#issuecomment-7126853881), [Comment on #39](https://github.com/ask-first-fcaj/ask-first/issues/39#issuecomment-7126855221), [Comment on #85](https://github.com/ask-first-fcaj/ask-first/issues/85#issuecomment-7126854553), [#172](https://github.com/ask-first-fcaj/ask-first/pull/172) |
| Wed | - The bot wording PR (SH-08, #157) was merged <br> - Review AI1's end-to-end image p95 PR (#184) | 25/11/2026 | 25/11/2026 | [#157](https://github.com/ask-first-fcaj/ask-first/pull/157), [#184](https://github.com/ask-first-fcaj/ask-first/pull/184), [lambda-p95.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/lambda-p95.md) |
| Thu | - Write the blog post "Vietnamese OCR on AWS Lambda" with an architecture diagram and submit it to the AWS Study Group (not approved for publication) | 26/11/2026 | 26/11/2026 | [Blog](../../3-blogsposted/) section |
| Fri | - Build the internship report site with Hugo on the FCAJ template, bilingual Vietnamese/English <br> - Review 2 BE PRs fixing bot buttons (#194, #195) <!-- NHÁP: việc dựng site chưa có link công khai --> | 27/11/2026 | 27/11/2026 | [#194](https://github.com/ask-first-fcaj/ask-first/pull/194), [#195](https://github.com/ask-first-fcaj/ask-first/pull/195) |

### Results

* VietOCR ([#178](https://github.com/ask-first-fcaj/ask-first/pull/178), [ocr-vietocr.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/ocr-vietocr.md)): URLs and phone numbers unchanged; words with diacritics read correctly rose from 37% to 89% on 50 images and from 27% to 97% on the 16 independent images; lost rule signals dropped from 15/33 to 6/33.
* The cost on a 3008 MB Lambda: each image takes p50 0.56 s and p95 3.42 s longer. AI1 decided to enable VietOCR on 24/11 ([ocr-vietocr.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/ocr-vietocr.md)).
* Line-broken URL fix ([#176](https://github.com/ask-first-fcaj/ask-first/pull/176)): `oi-004` (`dichvucong-vn.com`) and `oi-047` (`vietcombank.vn-yys.club`) moved from UNSURE to DANGEROUS.
* The whole chain from `webhook` works with #170 ([comment](https://github.com/ask-first-fcaj/ask-first/pull/170#issuecomment-7126853881)): 2 messages sent 0.5 s apart are merged into 1 result; an image gets a result after 11.7 s (warm); the DLQ no longer sends "busy" twice and no longer edits another job's "⏳" message.
* 7 of my PRs were merged this week: #173, #174, #175, #176, #178 (23/11), #172 (24/11), #157 (25/11). I reviewed 10 PRs from AI1 and BE (#170, #177, #179–#184, #194, #195).

### Lessons learned

* The missing diacritics come from the recognition model's character set and cannot be fixed by post-processing; a second re-reading model is needed, with a latency trade-off.
* Measuring on Lambda showed that RapidOCR alone already has a p95 of 5.07 s, above ADR-003's 3 s target; the earlier substitute measurement on GitHub Actions had hidden this ([ocr-vietocr.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/ocr-vietocr.md)).
* A real, working AWS environment lets both BE and AI check fixes before merging instead of guessing from CI.
