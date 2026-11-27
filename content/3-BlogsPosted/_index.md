---
title: "Blogs Posted"
date: 2026-11-27
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

This section collects the team's technical posts published in the [AWS Study Group VN](https://www.facebook.com/groups/awsstudygroupfcj), shared under the FCAJ rules, plus my own post. Each page names the author and includes a summary I wrote myself.

### [Blog 1 - Designing a cost-optimised IoT data flow (MQTT → AWS IoT Core → Glue → S3)](3.1-Blog1/)
Author: Ngo Thi Kim Ngan. Compacting millions of small JSON messages into Parquet files with AWS Glue to cut S3 request charges and Athena scan costs.

### [Blog 2 - Testing an SQS + DLQ + DynamoDB flow locally with LocalStack and Java 21](3.2-Blog2/)
Author: Ngo Thi Kim Ngan. Emulating AWS services with LocalStack to test both the happy path and the DLQ path before deploying with AWS SAM.

### [Blog 3 - Lambda and EventBridge: refreshing data every day with an event-driven design](3.3-Blog3/)
Author: Tri Duc Vo. Using EventBridge Scheduler, two Lambda functions and S3/CloudFront to build a self-updating page, plus two design pitfalls to avoid.

### [Blog 4 - Vietnamese OCR on AWS Lambda: when the model reads "chuyển tiền" as "chuyn tin"](3.4-Blog4/)
My own post, submitted to the group but not approved for publication. It explains how I traced why the OCR model dropped Vietnamese letters, how VietOCR (ONNX) re-reads selected lines and merges word by word, how the model is loaded from S3 during the Lambda INIT phase, and what we measured on a real Lambda in ap-southeast-1.
