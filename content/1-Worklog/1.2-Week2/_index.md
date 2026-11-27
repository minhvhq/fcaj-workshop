---
title: "Week 2 Worklog"
date: 2026-09-21
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

<!-- NHÁP: cả tuần 2 chưa có bằng chứng (trước khi có repo), Minh kiểm lại trước khi nộp -->

### Week 2 objectives

* Study in depth the two data stores used most in serverless: Amazon S3 and Amazon DynamoDB.
* Learn how to keep secrets (tokens, keys) with AWS KMS and SSM Parameter Store.
* Attend the Buildrathon Kickoff × CMC Global event.

### Tasks this week

| Day | Task | Start date | Completion date | Reference |
| --- | --- | --- | --- | --- |
| Mon | - S3: Block Public Access, default SSE-S3 encryption, versioning <br> - Create a presigned URL to download a file for a limited time | 21/09/2026 | 21/09/2026 | [Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html), [Presigned URL](https://docs.aws.amazon.com/AmazonS3/latest/userguide/using-presigned-url.html) |
| Tue | - Write a boto3 script that uploads a file to S3 with its sha256, downloads it and checks the checksum again | 22/09/2026 | 22/09/2026 | [S3 checksums](https://docs.aws.amazon.com/AmazonS3/latest/userguide/checking-object-integrity.html) |
| Wed | - DynamoDB: on-demand tables, `PK`/`SK` keys, enabling TTL so items expire by themselves | 23/09/2026 | 23/09/2026 | [DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html), [TTL](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/TTL.html) |
| Thu | - Store a secret as a `SecureString` in Parameter Store, read it with `get_parameter(WithDecryption=True)` <br> - Read how the default `aws/ssm` KMS key grants decrypt permission | 24/09/2026 | 24/09/2026 | [Parameter Store](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html), [AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/overview.html) |
| Fri | - Write a least-privilege IAM policy for an app that only reads one bucket, one table and one SSM parameter <br> - Delete the week's test resources | 25/09/2026 | 25/09/2026 | [IAM policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html) |
| Sat | - Attend Buildrathon Kickoff × CMC Global | 26/09/2026 | 26/09/2026 | [Events](../../4-eventparticipated/) section |

### Results

* An S3 upload/download script with a sha256 check; the same idea came back in week 10 when checking model artifacts.
* I understand how DynamoDB TTL removes old items without a cleanup job.
* A policy template that grants only `s3:GetObject`, `dynamodb:GetItem`/`PutItem` and `ssm:GetParameter`.

<!-- NHÁP: kết quả tuần 2 tự điền, Minh kiểm lại -->

### Lessons learned

* DynamoDB TTL does not delete items exactly at expiry, so the reading code still has to check the expiry time itself.
* Moving secrets to Parameter Store keeps tokens out of the code and the repository.
