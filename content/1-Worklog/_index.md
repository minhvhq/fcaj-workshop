---
title: "Worklog"
date: 2026-09-14
weight: 1
chapter: false
pre: " <b> 1. </b> "
---

In the team project **Ask First (Hỏi Trước)**, I am **AI2**. My part sits at the two ends of the AI pipeline: the input side (the screenshot dataset and OCR, PII masking, text normalization, the rules engine, URL intel, downloading media from Telegram) and the data and measurement side (a sourced knowledge base, local RAG, the eval gate in CI, and the mask, OTP and readability benchmarks). From 19/11/2026 I also built my own AWS environment to run the `ai` Lambda together with S3, DynamoDB, SSM and Telegram; from 24/11/2026 that environment became the one the whole team runs on.

How this worklog is written:

- Weeks 1–5 (14/09–16/10/2026) happened before the team had a repository, so there are no commits or PRs to cite. These weeks are reconstructed from the [First Cloud AI Journey](https://cloudjourney.awsstudygroup.com/) learning path and each carries a draft note so I can check it again.
- Weeks 6–11 (19/10–27/11/2026) are tied to PRs, commits, issues and review comments in the [ask-first-fcaj/ask-first](https://github.com/ask-first-fcaj/ask-first) repository. Every measurement links to where it was recorded.
- Week 12 (30/11–04/12/2026) is a plan and has not happened yet.

| Week | Dates | Main content |
| --- | --- | --- |
| 1 | 14–18/09 | [Starting FCAJ, a developer toolchain, an IAM user and the AWS CLI](1.1-week1/) |
| 2 | 21–25/09 | [S3, DynamoDB, KMS and Parameter Store; attending the Buildrathon Kickoff](1.2-week2/) |
| 3 | 28/09–02/10 | [Containers, Lambda and queues; watching AWS Cloud & AI Day](1.3-week3/) |
| 4 | 05–09/10 | [Joining Ask First as AI2, surveying Vietnamese OCR](1.4-week4/) |
| 5 | 12–16/10 | [Collecting scam screenshots, labeling them and studying PII masking](1.5-week5/) |
| 6 | 19–23/10 | [Labeling guide, a three-stack OCR spike and an independent test set](1.6-week6/) |
| 7 | 26–30/10 | [PII masking and a sourced knowledge base](1.7-week7/) |
| 8 | 02–06/11 | [Rules engine, URL intel and Telegram media download](1.8-week8/) |
| 9 | 09–13/11 | [RAG with e5-small, wiring RAG into the pipeline, an eval gate in CI](1.9-week9/) |
| 10 | 16–20/11 | [Three quality benchmarks and a private AWS environment for the `ai` Lambda](1.10-week10/) |
| 11 | 23–27/11 | [VietOCR, the team's shared AWS environment, a blog post and the report site](1.11-week11/) |
| 12 | 30/11–04/12 | [Workshop submission, demo and resource cleanup (plan)](1.12-week12/) |
