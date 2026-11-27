---
title: "Blog 3"
date: 2026-11-27
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# Lambda and EventBridge: using an event-driven architecture to refresh data every day

**Author:** Tri Duc Vo (team member) · Team blog, shared under the FCAJ rules

**Post:** [AWS Study Group VN](https://www.facebook.com/groups/660548818043427/?multi_permalinks=2345717436193215)

{{% notice info %}}
The post lives in the AWS Study Group VN group, so only group members can open it. Below is my own summary of it.
{{% /notice %}}

### The problem

The author wanted a single web page that shows fuel prices, gold prices, esports scores and the weather, instead of opening several tabs every morning. The requirements: no server running 24/7, each data source refreshed at its own pace, one failing source must not break the whole page, new sources easy to add, and a small personal budget.

### Architecture

```text
EventBridge Scheduler (one schedule per source, payload {"source": ...})
  → Lambda Collector → S3 data/<source>.json
  → S3 "Object Created" → EventBridge Rule (filters prefix data/)
  → Lambda Renderer → S3 index.html → CloudFront (invalidation)
```

- **EventBridge Scheduler** is the system's clock: each source has its own schedule (weather every 3 hours, scores every 30 minutes, fuel and gold daily or weekly), set in Vietnam's time zone, with retries and a DLQ for failed invocations.
- **Lambda Collector** is shared by all sources: it reads the source name from the payload, calls the API, normalises the result into compact JSON and writes it to S3. If a source fails, it reports the error and does not overwrite the old file, so the page keeps the previous data.
- **EventBridge Rule** catches new files under `data/` and invokes **Lambda Renderer**, which merges the data, rebuilds `index.html` and refreshes the CloudFront cache.

### Two pitfalls called out in the post

1. **Filter on the `data/` prefix.** The renderer writes `index.html` to the same bucket; if the rule matched every new object, `index.html` itself would trigger the renderer again and create an infinite loop.
2. **S3 delivers events at least once**, so duplicates can happen. The renderer is idempotent: it always rebuilds the page from the current data and overwrites it, so running it again does not change the result.

### What I took away

- Splitting the system by events lets each part run, fail and change independently; adding a source only needs one more schedule and one more normaliser.
- When a function reads from and writes to the same bucket, the risk of it triggering itself has to be considered right away.
- Showing an update timestamp (`updatedAt`) for each section is a simple way to tell readers which data is stale.
