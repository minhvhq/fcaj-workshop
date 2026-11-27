---
title: "Week 1 Worklog"
date: 2026-09-14
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---


### Week 1 objectives

* Understand how the FCAJ program runs and its learning path on the Cloud Journey site.
* Prepare the work machine: Git, Python, Docker, VS Code.
* Have a dedicated IAM user for daily work (no root usage) and an AWS CLI that runs with a named profile.

### Tasks this week

| Day | Task | Start date | Completion date | Reference |
| --- | --- | --- | --- | --- |
| Mon | - Receive program information, the activity schedule and how to submit the report <br> - Read the learning path and mark the modules related to serverless and AI | 14/09/2026 | 14/09/2026 | [FCAJ Cloud Journey](https://cloudjourney.awsstudygroup.com/), [FCAJ rules](https://hcm-rules.awsfcaj.com/) |
| Tue | - Install Git, set up an SSH key for GitHub <br> - Install Python 3.12 and `uv`, create a small test project with `pyproject.toml` | 15/09/2026 | 15/09/2026 | [uv](https://docs.astral.sh/uv/), [GitHub SSH](https://docs.github.com/en/authentication/connecting-to-github-with-ssh) |
| Wed | - Install Docker Desktop, run a sample container, read how to write multi-stage Dockerfiles | 16/09/2026 | 16/09/2026 | [Docker docs](https://docs.docker.com/get-started/) |
| Thu | - Create a dedicated IAM user for daily work instead of using the root account <br> - Install AWS CLI v2, configure a named profile, try `aws sts get-caller-identity` | 17/09/2026 | 17/09/2026 | [IAM users](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users.html), [CLI profiles](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html) |
| Fri | - Open Cost Explorer, view cost by service and by day <br> - Try AWS CloudShell and write a boto3 script that lists Regions | 18/09/2026 | 18/09/2026 | [Cost Explorer](https://docs.aws.amazon.com/cost-management/latest/userguide/ce-what-is.html), [boto3](https://boto3.amazonaws.com/v1/documentation/api/latest/index.html) |

### Results

* My laptop runs Git over SSH, Python with `uv`, and Docker.
* The AWS CLI runs with a named profile for a dedicated IAM user; the access key stays outside every code folder.
* I know where to see cost by service in Cost Explorer, which I used again in week 10 to measure the cost of the test environment.


### Lessons learned

* Setting up tools early let the following weeks focus only on AWS services.
* Do not use root for daily work; access keys must stay out of the repo and be deleted once no longer needed.
