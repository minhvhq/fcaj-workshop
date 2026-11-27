---
title: "Week 1 Worklog"
date: 2026-09-14
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---

<!-- NHÁP: cả tuần 1 chưa có bằng chứng (trước khi có repo), Minh kiểm lại trước khi nộp -->

### Week 1 objectives

* Understand how the FCAJ program runs and its learning path on the Cloud Journey site.
* Prepare the work machine: Git, Python, Docker, VS Code.
* Have an AWS account accessed through IAM Identity Center and an AWS CLI using an SSO profile, with no long-lived access keys.

### Tasks this week

| Day | Task | Start date | Completion date | Reference |
| --- | --- | --- | --- | --- |
| Mon | - Receive program information, the activity schedule and how to submit the report <br> - Read the learning path and mark the modules related to serverless and AI | 14/09/2026 | 14/09/2026 | [FCAJ Cloud Journey](https://cloudjourney.awsstudygroup.com/), [FCAJ rules](https://hcm-rules.awsfcaj.com/) |
| Tue | - Install Git, set up an SSH key for GitHub <br> - Install Python 3.12 and `uv`, create a small test project with `pyproject.toml` | 15/09/2026 | 15/09/2026 | [uv](https://docs.astral.sh/uv/), [GitHub SSH](https://docs.github.com/en/authentication/connecting-to-github-with-ssh) |
| Wed | - Install Docker Desktop, run a sample container, read how to write multi-stage Dockerfiles | 16/09/2026 | 16/09/2026 | [Docker docs](https://docs.docker.com/get-started/) |
| Thu | - Enable IAM Identity Center, create a user and a permission set for daily work <br> - Configure `aws configure sso`, sign in with `aws sso login` | 17/09/2026 | 17/09/2026 | [IAM Identity Center](https://docs.aws.amazon.com/singlesignon/latest/userguide/what-is.html), [CLI SSO](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-sso.html) |
| Fri | - Open Cost Explorer, view cost by service and by day <br> - Try AWS CloudShell and write a boto3 script that lists Regions | 18/09/2026 | 18/09/2026 | [Cost Explorer](https://docs.aws.amazon.com/cost-management/latest/userguide/ce-what-is.html), [boto3](https://boto3.amazonaws.com/v1/documentation/api/latest/index.html) |

### Results

* My laptop runs Git over SSH, Python with `uv`, and Docker.
* The AWS CLI signs in with an SSO profile; sessions expire on their own, so no access key stays on the machine.
* I know where to see cost by service in Cost Explorer, which I used again in week 10 to measure the cost of the test environment.

<!-- NHÁP: kết quả tuần 1 tự điền, Minh kiểm lại -->

### Lessons learned

* Setting up tools early let the following weeks focus only on AWS services.
* Short-lived sessions are safer than keeping access keys in a config file.
