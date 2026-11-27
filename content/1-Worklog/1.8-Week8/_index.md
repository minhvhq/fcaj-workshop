---
title: "Week 8 Worklog"
date: 2026-11-02
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Week 8 objectives

* Address the remaining review items on PII masking (AI-22), especially the O(n²) slowdown on messages with many repeated numbers.
* Finish the three missing input blocks: text normalization + rules engine (AI-23), offline URL intel (AI-24a), downloading images/voice from Telegram and model artifacts from S3 (AI-03b).

### Tasks this week

| Day | Task | Start date | Completion date | Reference |
| --- | --- | --- | --- | --- |
| Mon | - Fix the mask: cover every repetition of an OTP, remove the O(n²) behavior with `endpos` and `bisect`, stop treating `code` inside a URL as an OTP keyword; open PR #118 | 02/11/2026 | 03/11/2026 | [PR #118](https://github.com/ask-first-fcaj/ask-first/pull/118) |
| Tue | - Write `media/telegram.py` (getFile, up to 4 parallel downloads, ≤ 10 s per file, reject > 20 MB, token read from SSM) and `S3ArtifactStore` with sha256 checks (#121) <br> - Fix after review: total download time bounded by `deadline_s`, `ArtifactError`, cleanup of `.part` files (#125) <br> - Text normalization + 14 rules on masked text (#128) <br> - URL intel: allowlist, typosquat, punycode, `text_link` differing from the real link, based on the Public Suffix List (#129; #130 was a duplicate and was closed) <br> - Review AI1's PRs from the same day: fuse (#119), respond (#120), judge cascade (#122, #123), `RealPipeline` (#124), local OCR (#126), SSRF-safe short link expansion (#127) | 03/11/2026 | 03/11/2026 | [#121](https://github.com/ask-first-fcaj/ask-first/pull/121), [#125](https://github.com/ask-first-fcaj/ask-first/pull/125), [#128](https://github.com/ask-first-fcaj/ask-first/pull/128), [#129](https://github.com/ask-first-fcaj/ask-first/pull/129) |
| Wed | - Pull `main` after the 03/11 merges and rerun the whole `ai/` test suite locally | 04/11/2026 | 04/11/2026 | [CHANGELOG.md](https://github.com/ask-first-fcaj/ask-first/blob/main/CHANGELOG.md) |
| Thu | - Reread the `R_*` rules in `kb/rules.yaml` and note messages they still miss, for later benchmarks | 05/11/2026 | 05/11/2026 | [kb/rules.yaml](https://github.com/ask-first-fcaj/ask-first/blob/main/kb/rules.yaml) |
| Fri | - Read about the ONNX int8 build of `multilingual-e5-small` to prepare RAG (AI-29) | 06/11/2026 | 06/11/2026 | [multilingual-e5-small](https://huggingface.co/intfloat/multilingual-e5-small) |

### Results

* The mask is no longer slow on long messages ([#118](https://github.com/ask-first-fcaj/ask-first/pull/118)): `"otp 1234 "` repeated 5,000 times (45,000 characters) went from 39.2 s to 0.38 s; `"OTP 482913 "` from 34.2 s to 0.28 s.
* All 5 of my PRs on 03/11 were merged the same day. The `ai/` suite grew to 433 passing tests after #125 and 495 after #128 ([#125](https://github.com/ask-first-fcaj/ask-first/pull/125), [#128](https://github.com/ask-first-fcaj/ask-first/pull/128)).
* The rules engine on 100 eval samples, rules only: 0 samples changed signals compared with before the review fixes, and no benign sample took the fast path ([#128](https://github.com/ask-first-fcaj/ask-first/pull/128)).
* Reviewed 8 of AI1's PRs on 03/11 (#119, #120, #122–#127).

### Lessons learned

* The O(n²) bug did not show up in normal tests, only when I deliberately tried a long string; since then I add a "very long message" case to performance tests.
* URL intel should flag typosquatting only when there is a clear sign of imitation; a domain that merely "carries a brand name" gets a light signal (`U_BRAND_UNVERIFIED`) to avoid false alarms.
