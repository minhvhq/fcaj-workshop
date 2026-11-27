---
title: "Week 12 Worklog"
date: 2026-11-30
weight: 12
chapter: false
pre: " <b> 1.12. </b> "
---

{{% notice info %}}
Week 12 is a **plan**, written before 30/11/2026. The items below have not happened yet and will be updated later.
{{% /notice %}}

<!-- NHÁP: toàn bộ tuần 12 là kế hoạch, Minh cập nhật kết quả thật sau khi làm -->

### Week 12 objectives

* Submit the workshop and the internship report site.
* Demo the Ask First bot running on the team's shared AWS environment.
* Clean up every resource on my test account so no further cost is incurred.

### Plan for the week

| Day | Task (planned) | Start date | Completion date | Reference |
| --- | --- | --- | --- | --- |
| Mon | - Review the workshop and worklog pages, replace missing images, check PR links <br> - Submit the workshop | 30/11/2026 | 30/11/2026 | [Workshop](../../5-workshop/) section |
| Tue | - Rehearse the demo script on the `askfirst-dev` stack: a text message with an OTP, a fake bank screenshot, a short link | 01/12/2026 | 01/12/2026 | [implementation-plan.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/implementation-plan.md) |
| Wed | - Demo with the team; record bugs found during the demo as issues | 02/12/2026 | 02/12/2026 | [Issues](https://github.com/ask-first-fcaj/ask-first/issues) |
| Thu | - Clean up following SH-10: remove the test bot's webhook, delete the `ai` Lambda and ECR images, empty and delete the S3 bucket, delete the DynamoDB table, SSM parameters and log groups, `sam delete` the stack; delete temporary access keys | 03/12/2026 | 03/12/2026 | [SAM delete](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-cli-command-reference-sam-delete.html), [implementation-plan.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/implementation-plan.md) |
| Fri | - Check Cost Explorer and the resource list after cleanup <br> - Finish the Self-evaluation and Feedback sections | 04/12/2026 | 04/12/2026 | [Cost Explorer](https://docs.aws.amazon.com/cost-management/latest/userguide/ce-what-is.html) |

### Expected results

* The workshop and report site are submitted on time.
* The demo runs on the shared environment, with a list of remaining bugs.
* The test account has no billable resources left.

### Notes

* The S3 bucket must be emptied and the ECR images deleted before running `sam delete`, otherwise the stack deletion fails.
* Clean up only after the team demo, because this account is currently the shared runtime environment.
