---
title: "Week 9 Worklog"
date: 2026-11-09
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

### Week 9 objectives

* Build a RAG that runs locally inside Lambda with no external embedding service (AI-29), then wire it into `RealPipeline` (INT-03).
* Put eval into CI so that any PR that breaks the rules or the KB is caught (AI-09).
* Contribute human scores to AI1's LLM-as-a-judge experiment (AI-11).

### Tasks this week

| Day | Task | Start date | Completion date | Reference |
| --- | --- | --- | --- | --- |
| Mon | - Build a corpus of scam warnings from `.gov.vn` sites and newspapers for RAG | 09/11/2026 | 11/11/2026 | [kb/alerts.yaml](https://github.com/ask-first-fcaj/ask-first/blob/main/kb/alerts.yaml) |
| Tue | - Write the e5-small int8 embedder, `build_index.py` and the retriever; measure Recall@4 | 10/11/2026 | 11/11/2026 | [PR #147](https://github.com/ask-first-fcaj/ask-first/pull/147) |
| Wed | - Open and merge local RAG (#147) <br> - Hand-score 20 AI-11 samples with the rubric, before looking at the LLM scores (#146) <br> - Open the PR wiring RAG into `RealPipeline` + artifact upload script (#151) and the CI eval gate PR (#153) <br> - Review AI1's batch of PRs: Bedrock Qwen3 32B spike (#132), 4-model benchmark (#133), `outputConfig` (#134), Guardrails (#135), LLM-as-a-judge (#136), red-team (#137), Gemini reference (#141), fallback model (#142), judge fixes (#144, #145 approved), OTP/ID-card rules (#149), G1 hand scoring (#150) | 11/11/2026 | 11/11/2026 | [PR #146](https://github.com/ask-first-fcaj/ask-first/pull/146), [PR #147](https://github.com/ask-first-fcaj/ask-first/pull/147), [#151](https://github.com/ask-first-fcaj/ask-first/pull/151), [#153](https://github.com/ask-first-fcaj/ask-first/pull/153) |
| Thu | - Fix after review and merge #151, #153; the eval gate ran for real on a PR and commented a comparison table | 12/11/2026 | 12/11/2026 | [PR #151](https://github.com/ask-first-fcaj/ask-first/pull/151), [PR #153](https://github.com/ask-first-fcaj/ask-first/pull/153) |
| Fri | - Start the bot message wording (SH-08) using a reading sheet for older users | 13/11/2026 | 13/11/2026 | [PR #157](https://github.com/ask-first-fcaj/ask-first/pull/157) |
| Sat | - Review 2 BE2 PRs: the "Bot was wrong" flow (BE-30, #155) and `/rutdongy` data deletion (BE-31, #156) | 14/11/2026 | 14/11/2026 | [#155](https://github.com/ask-first-fcaj/ask-first/pull/155), [#156](https://github.com/ask-first-fcaj/ask-first/pull/156) |

### Results

* Local RAG ([#147](https://github.com/ask-first-fcaj/ask-first/pull/147)): a corpus of 103 warnings; scenario-card Recall@4 on `real_scam` 35/38 = 92.1% (threshold 90%); query p50 7.9 ms, p95 9.7 ms on a laptop, about 57 ms for a 512-token message.
* RAG wired into the pipeline ([#151](https://github.com/ask-first-fcaj/ask-first/pull/151)): cites up to 3 warnings matching the verdict's scenario code; if model loading fails, it continues without RAG and logs no message content.
* CI eval gate ([#153](https://github.com/ask-first-fcaj/ask-first/pull/153)): runs eval on the base branch and the PR and compares with the baseline; without a Bedrock role it runs rules only and blocks when the result is more than 2 points worse than the baseline.
* Reviewed 15 PRs this week (13 from AI1, 2 from BE2).

### Lessons learned

* With rules only, recall is only about 19%, so the absolute 90% threshold cannot apply; comparing with the baseline is what catches PRs that break the rules ([#153](https://github.com/ask-first-fcaj/ask-first/pull/153)).
* The retriever's top-3 score aggregation was chosen on the same benchmark, so Recall@4 needs an independent set to be more trustworthy.
