---
title: "Blog 1"
date: 2026-11-27
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Designing a cost-optimised IoT data flow (MQTT → AWS IoT Core → Glue → S3) for small projects

**Author:** Ngo Thi Kim Ngan (team member) · Team blog, shared under the FCAJ rules

**Post:** [AWS Study Group VN](https://www.facebook.com/groups/awsstudygroupfcj/permalink/2351133745651584/)

{{% notice info %}}
The post lives in the AWS Study Group VN group, so only group members can open it. Below is my own summary of it.
{{% /notice %}}

### The problem

A sensor system that sends a small JSON message over MQTT every few seconds produces millions of files in Amazon S3 after a few months. The post estimates that 10 devices sending every 10 seconds for 3 months create about 7.7 million files. At that point PUT/GET request charges can exceed storage charges, and Amazon Athena has to open every JSON file, so queries become slow and expensive to scan.

### Proposed architecture

The data flow has two stages and no servers:

1. **Raw collection:** the AWS IoT Core Rule Engine filters messages with SQL and writes them straight to a raw S3 bucket, partitioned by `year=/month=/day=/hour=`. No EC2 instance or Lambda function is needed just to receive MQTT.
2. **Compaction:** once a day, Amazon EventBridge Scheduler starts an AWS Glue job that reads the previous day's JSON, cleans the data types, merges the files and writes Apache Parquet (Snappy compression) to a curated bucket.
3. **Cleanup:** an S3 Lifecycle rule deletes raw JSON after 7 days, which keeps a week of raw data for debugging without paying to store it forever.

### Results reported in the post

For a scenario of 10 million messages per month (about 2 GB of raw JSON):

| Metric | Before | After |
|---|---|---|
| Storage in S3 | ~2.0 GB | ~280 MB |
| Number of files in S3 | ~10,000,000 | ~30 (1 file/day) |
| Data scanned by Athena for a one-column query | ~2.0 GB | ~40 MB |

The post also suggests how to keep Glue cheap: with Spark, use the G.1X worker type and at most 2 workers; for data under a few hundred MB per day, use Glue Python Shell (0.0625 DPU) with the `awswrangler` library.

### What I took away

- S3 cost is not only about storage size; the number of requests and objects also has to be counted at design time.
- A columnar format such as Parquet sharply reduces how much data Athena scans, which directly lowers query cost.
- Using built-in service features (IoT rules, S3 Lifecycle) instead of custom code leaves fewer components to operate.
