---
title: "Week 7 Worklog"
date: 2026-10-26
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Week 7 objectives

* Mask personal information in a message before it reaches the rules and the LLM prompt (AI-22).
* Build knowledge base (KB) v1 where every entry has a source and a verification date, plus an automatic checking script (AI-25, SH-05).

### Tasks this week

| Day | Task | Start date | Completion date | Reference |
| --- | --- | --- | --- | --- |
| Mon | - Read ARCH §9.2 (PII masking) and list the tokens to generate: `[SĐT_n]`, `[STK_n • bank]`, `[CCCD_n]`, `[OTP_n]`, `[EMAIL_n]` | 26/10/2026 | 26/10/2026 | [system-architecture.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/system-architecture.md) |
| Tue | - Collect hotlines of banks, carriers and e-wallets from official sites for the KB | 27/10/2026 | 28/10/2026 | [kb/hotlines.yaml](https://github.com/ask-first-fcaj/ask-first/blob/main/kb/hotlines.yaml) |
| Wed | - Write `mask(text)` returning `MaskResult(text, entities)`; original values stay only in RAM | 28/10/2026 | 30/10/2026 | [PR #110](https://github.com/ask-first-fcaj/ask-first/pull/110) |
| Thu | - Review AI1's Bedrock Converse client and Qwen3 spike skeleton (#103) | 29/10/2026 | 29/10/2026 | [#103](https://github.com/ask-first-fcaj/ask-first/pull/103) |
| Fri | - Open the PII mask PR (#110) and fix it after review: leaks of 4-2-4 phone numbers, international numbers, account numbers with the holder's name, OTPs repeated inside URLs; merged the same day <br> - Open the KB v1 + validator PR (#111), merged 31/10 <br> - Review the batch of AI1 PRs opened the same day: boundary-based OCR URL scoring (#105), LABELING v2 (#106), relabeling eval v0 (#107, #108), labels for 16 images (#109), Gate G2 (#112), eval harness (#113), privacy v0.1 (#114), judge prompt v1 (#115), `ai` Lambda handler + DynamoKV (#116) | 30/10/2026 | 31/10/2026 | [PR #110](https://github.com/ask-first-fcaj/ask-first/pull/110), [PR #111](https://github.com/ask-first-fcaj/ask-first/pull/111), [commit 1f41843](https://github.com/ask-first-fcaj/ask-first/commit/1f41843) |
| Sat | - Review AI1's PR adding cards N2.2 and N5.2 to the KB (#117) | 31/10/2026 | 31/10/2026 | [#117](https://github.com/ask-first-fcaj/ask-first/pull/117) |

### Results

* PII masking merged ([#110](https://github.com/ask-first-fcaj/ask-first/pull/110)): 90 cases in `test_mask.py`; the whole `ai/` test suite at that time had 132 passing tests.
* KB v1 merged ([#111](https://github.com/ask-first-fcaj/ask-first/pull/111)): 19 scenario cards, 39 official domains, 35 brands, 20 hotlines, one 5-step checklist; every entry has `source_url` and `verified_at`; `example_phrases` are quoted verbatim from sources, not invented by an LLM.
* Reviewed 14 of AI1's PRs this week (#103, #105–#109, #112–#117).

### Lessons learned

* A PII masking function needs both positive tests and trap samples (amounts of money, order codes) to know whether it masks the wrong things.
* Putting a verification date on each hotline creates a re-check deadline; I used exactly that deadline in week 10 ([#168](https://github.com/ask-first-fcaj/ask-first/pull/168)).
