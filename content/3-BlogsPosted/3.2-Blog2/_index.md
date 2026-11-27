---
title: "Blog 2"
date: 2026-11-27
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# Testing a serverless, event-driven flow (SQS + DLQ + DynamoDB) locally with LocalStack and Java 21

**Author:** Ngo Thi Kim Ngan (team member) · Team blog, shared under the FCAJ rules

**Post:** [AWS Study Group VN](https://www.facebook.com/groups/awsstudygroupfcj/posts/2349976352433990/)

{{% notice info %}}
The post lives in the AWS Study Group VN group, so only group members can open it. Below is my own summary of it.
{{% /notice %}}

### Context

The asynchronous flow in the post is: an event goes into Amazon SQS, an AWS Lambda function (Java 21) processes it, writes to Amazon DynamoDB and reads configuration from SSM Parameter Store, with a DLQ for failed messages. Deploying to AWS to try every small fix is slow and can cause unexpected charges.

### The "local-first" approach

- **Emulate the services locally:** LocalStack runs SQS, the DLQ, DynamoDB and SSM on the developer's machine.
- **Keep the endpoint switch clean:** with the AWS SDK for Java v2, `endpointOverride(URI.create(localEndpoint))` is called only in local mode (`http://localhost:4566`); in the cloud the default endpoint is used, so the same code runs in both places.
- **Test both the happy path and the failure path:** send a valid payload to check that the item lands in DynamoDB and the message is deleted from the main queue; send a bad payload to check that the message ends up in the DLQ after the configured number of retries.
- **Go to the cloud last:** build and deploy with AWS SAM only once the logic and configuration work locally.

### What I took away

- The failure path (retries, DLQ) should be tested on purpose like the main path, because it is the hardest part to observe once it runs on AWS.
- Keeping the endpoint choice in one place stops the code from branching all over between local and cloud.
- Our Ask First project also uses SQS with a DLQ for background processing, so the DLQ test approach in the post applies directly.
