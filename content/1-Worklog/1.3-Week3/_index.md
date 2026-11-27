---
title: "Week 3 Worklog"
date: 2026-09-28
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

<!-- NHÁP: cả tuần 3 chưa có bằng chứng (trước khi có repo), Minh kiểm lại trước khi nộp -->

### Week 3 objectives

* Package Python code as a container image and run it on AWS Lambda.
* Understand how Lambda memory determines CPU, and how the INIT phase affects cold starts.
* Use SQS with a dead-letter queue and read logs with CloudWatch Logs Insights.
* Watch AWS Cloud & AI Day Vietnam 2026 through a Watch Party.

### Tasks this week

| Day | Task | Start date | Completion date | Reference |
| --- | --- | --- | --- | --- |
| Mon | - Create an Amazon ECR repository, push a test image with `docker push` <br> - Read why Lambda needs a single-architecture image (`--provenance=false`) | 28/09/2026 | 28/09/2026 | [Amazon ECR](https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html) |
| Tue | - Attend the AWS Cloud & AI Day Vietnam 2026 Watch Party <br> - Note the parts about generative AI and serverless | 29/09/2026 | 29/09/2026 | [Events](../../4-eventparticipated/) section |
| Wed | - Write a Lambda from the `public.ecr.aws/lambda/python:3.12` image, test it locally with the Lambda Runtime Interface Emulator <br> - Set timeout, memory and `/tmp` ephemeral storage | 30/09/2026 | 30/09/2026 | [Lambda container image](https://docs.aws.amazon.com/lambda/latest/dg/images-create.html), [RIE](https://docs.aws.amazon.com/lambda/latest/dg/images-test.html) |
| Thu | - Read the Lambda execution environment lifecycle: INIT, INVOKE, SHUTDOWN <br> - Measure cold start when a large library is loaded outside versus inside the handler | 01/10/2026 | 01/10/2026 | [Lambda execution environment](https://docs.aws.amazon.com/lambda/latest/dg/lambda-runtime-environment.html) |
| Fri | - SQS: visibility timeout, `maxReceiveCount`, DLQ and redrive <br> - Write a few CloudWatch Logs Insights queries for JSON logs | 02/10/2026 | 02/10/2026 | [SQS DLQ](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-dead-letter-queues.html), [Logs Insights](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/AnalyzingLogData.html) |

### Results

* A container-based Lambda that runs both locally (RIE) and on AWS.
* I understand that Lambda allocates CPU by memory and that the INIT phase has a time limit; I used this again when choosing 3008 MB for the `ai` Lambda in week 10.
* I can write Logs Insights queries that filter logs by JSON fields.

<!-- NHÁP: kết quả tuần 3 tự điền, Minh kiểm lại -->

### Lessons learned

* The larger the image, the slower the cold start, so heavy models are better downloaded from S3 than baked into the image.
* A DLQ should be tested by forcing failures on purpose, because a correct configuration does not guarantee correct behavior.
