---
title: "Week 6 Worklog"
date: 2026-10-19
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

The first week of the 6-week plan (19/10–29/11/2026). From this week on, every task has a PR or commit in the [ask-first-fcaj/ask-first](https://github.com/ask-first-fcaj/ask-first) repository.

### Week 6 objectives

* Write a labeling guide so the whole team labels the eval set the same way (SH-02).
* Put the OCR image set in the repository and answer the Gate G2 question: which OCR reads URLs in screenshots correctly (AI-20).
* Have an independent image set to check whether the spike's numbers are too optimistic.

### Tasks this week

| Day | Task | Start date | Completion date | Reference |
| --- | --- | --- | --- | --- |
| Mon | - Read the 6-week plan and the AI2 tasks in `implementation-plan.md` | 19/10/2026 | 19/10/2026 | [implementation-plan.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/implementation-plan.md) |
| Tue | - Write `eval/LABELING.md`: how to label scenarios N0–N6 and the `OTHER` group, with 10 trial samples (SH-02) <br> - Review the BE ↔ AI contract v1.0 PR (#89) and the guide to 3 side research tasks for AI2 (#93) | 20/10/2026 | 21/10/2026 | [PR #94](https://github.com/ask-first-fcaj/ask-first/pull/94), [#89](https://github.com/ask-first-fcaj/ask-first/pull/89), [#93](https://github.com/ask-first-fcaj/ask-first/pull/93) |
| Wed | - Add 25 OCR images `001–025` with hand-typed labels (#96; the content reached `main` through #97) <br> - Write the OCR spike: compare Tesseract, PaddleOCR (+VietOCR) and RapidOCR on 50 images, scoring URLs, phone numbers and CER (#97) <br> - Review AI1's images `026–050` (#95) | 21/10/2026 | 22/10/2026 | [PR #96](https://github.com/ask-first-fcaj/ask-first/pull/96), [PR #97](https://github.com/ask-first-fcaj/ask-first/pull/97), [#95](https://github.com/ask-first-fcaj/ask-first/pull/95) |
| Thu | - Review AI1's ADR-003 (RapidOCR chosen with conditions) against the spike's measurements | 22/10/2026 | 22/10/2026 | [#98](https://github.com/ask-first-fcaj/ask-first/pull/98), [ADR-003](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/decisions/ADR-003-ocr-rapidocr.md) |
| Fri | - Add an independent OCR test set of 16 images `oi-test-001–016`, not used when writing the column-ordering and regex rules (#99) <br> - Extend eval set v0 to 78 text samples (SH-03, commit `1d90b2f`) <br> - Review the validator + 100-sample eval set (#100) and the ADR-003 update (#101) | 23/10/2026 | 23/10/2026 | [PR #99](https://github.com/ask-first-fcaj/ask-first/pull/99), [commit 1d90b2f](https://github.com/ask-first-fcaj/ask-first/commit/1d90b2f), [#100](https://github.com/ask-first-fcaj/ask-first/pull/100), [#101](https://github.com/ask-first-fcaj/ask-first/pull/101) |

### Results

* I opened 4 PRs this week; #94, #97 and #99 were merged, #96 was closed because its content went in through #97.
* OCR spike on 50 images ([#97](https://github.com/ask-first-fcaj/ask-first/pull/97), [ocr-spike.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/ocr-spike.md)):

| Configuration | Correct URLs (strict) | Correct URLs after regex | CER |
| --- | --- | --- | --- |
| RapidOCR + column ordering | 27/32 (84%) | 31/32 | 12.9% |
| Plain RapidOCR | 25/32 | | 26.8% |
| PaddleOCR + columns | 28/32 | 31/32 | about 5x heavier (~520 MB vs ~105 MB) |
| Tesseract `vie` | 10/32 | | |
| Paddle + VietOCR | 9/32 | | |

* The conclusion AI1 recorded in ADR-003: use RapidOCR + column ordering; no engine reached the G2 threshold in strict form.
* I reviewed 6 of AI1's PRs this week: [#89](https://github.com/ask-first-fcaj/ask-first/pull/89), [#93](https://github.com/ask-first-fcaj/ask-first/pull/93), [#95](https://github.com/ask-first-fcaj/ask-first/pull/95), [#98](https://github.com/ask-first-fcaj/ask-first/pull/98), [#100](https://github.com/ask-first-fcaj/ask-first/pull/100), [#101](https://github.com/ask-first-fcaj/ask-first/pull/101).

### Lessons learned

* The two URL-prefix repair rules were written after looking at errors on the same 50 images, so 31/32 is optimistic. The independent 16-image set ([#99](https://github.com/ask-first-fcaj/ask-first/pull/99)) is how I checked that myself.
* The most accurate engine (Paddle) is not necessarily the right fit for Lambda: size and RAM matter just as much.
