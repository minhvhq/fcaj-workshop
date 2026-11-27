---
title: "Workshop"
date: 2026-11-27
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Building a Production Environment for Ask First on a Single AWS Account: From AI Models on S3 to a Telegram Bot

Ask First ("Hỏi Trước") is a Telegram bot that helps older adults check a suspicious message, link, or screenshot **before** acting on it. The bot replies in Vietnamese: how risky the message is, which scam pattern it resembles, and what to do right away.

This workshop follows the exact path I took when building the team's shared runtime environment on my personal AWS account (from 19/11/2026): **deploy each component to AWS, test it, and only then wire everything into the full pipeline**. That order means that when something fails, we immediately know which component is at fault.

By the end of the workshop, you will have:

- an S3 bucket holding the AI models (OCR, VietOCR, embedder) and the knowledge base index, with SHA-256 verification;
- an AWS SAM stack with 3 Java Lambda functions (`webhook`, `worker`, `worker-dlq`), DynamoDB, SQS with a DLQ, and a CloudWatch alarm that sends email through SNS;
- an `ai` Lambda function (Python 3.12, container image on Amazon ECR) that runs OCR, masks personal data, applies the rule set, performs RAG, and calls Amazon Bedrock;
- a Telegram bot that gives real answers, along with logs, alarms, and measurements that prove the system works correctly.

{{% notice info %}}
All commands in this workshop are run in **PowerShell on Windows**, since that is the machine I deploy from. AWS CLI commands are identical on macOS/Linux; only the variable syntax differs (`$env:X = "..."` instead of `export X=...`).
{{% /notice %}}

#### Contents

1. [Overview and architecture](5.1-overview/)
2. [Preparing the account and tools](5.2-prerequisites/)
3. [Uploading AI models to S3](5.3-model-artifacts/)
4. [Deploying the backend stack with AWS SAM](5.4-deploy-backend/)
5. [Packaging and deploying the AI Lambda](5.5-deploy-ai-lambda/)
6. [Connecting the Telegram bot](5.6-connect-telegram/)
7. [Testing and measurement](5.7-testing/)
8. [Monitoring and alerting](5.8-monitoring/)
9. [Cost and optimization](5.9-cost/)
10. [Cleaning up resources](5.10-cleanup/)
11. [Demo and lessons learned](5.11-demo-lessons/)
