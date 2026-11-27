---
title: "Cleaning Up Resources"
date: 2026-11-27
weight: 10
chapter: false
pre: " <b> 5.10. </b> "
---

The system costs almost nothing when there are no messages, but a few storage-based charges remain (ECR, S3, logs), as do secrets that are still valid. After finishing the workshop, clean up in **exactly the order** below. The order matters because: CloudFormation cannot delete a bucket that still contains objects; the `ai` Lambda and resources created with the CLI are not part of the stack, so `sam delete` does not touch them.

{{% notice warning %}}
Steps 3–7 are **irreversible**: the DynamoDB table (including PITR backups), the models on S3, the images, and the bot token are all lost. The team's environment is the shared runtime environment until the end of the program, so I have **not yet** run this section on it; the commands below are the procedure I will use at the end.
{{% /notice %}}

```powershell
$env:AWS_PROFILE = "ai2test"; $env:AWS_DEFAULT_REGION = "ap-southeast-1"
$acct = aws sts get-caller-identity --query Account --output text
```

#### Step 1: Disconnect Telegram from the system

Remove the webhook first so Telegram stops sending updates to the Function URL while deletion is in progress:

```powershell
$env:TELEGRAM_BOT_TOKEN = aws ssm get-parameter --name /askfirst/dev/telegram/bot_token --with-decryption --query Parameter.Value --output text
python scripts/set_webhook.py --delete
Remove-Item Env:TELEGRAM_BOT_TOKEN
```

#### Step 2: Delete the `ai` Lambda and its role (created with the CLI in 5.5)

```powershell
aws lambda delete-alias --function-name askfirst-dev-ai --name live
aws lambda delete-function --function-name askfirst-dev-ai          # also deletes all versions
aws iam delete-role-policy --role-name askfirst-ai2test-ai-role --policy-name ai-least-privilege
aws iam detach-role-policy --role-name askfirst-ai2test-ai-role `
  --policy-arn arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole
aws iam delete-role --role-name askfirst-ai2test-ai-role
```

#### Step 3: Delete the ECR images and repository

```powershell
aws ecr delete-repository --repository-name askfirst-dev-ai --force   # --force also deletes the images inside
```

#### Step 4: Empty and delete the buckets

```powershell
aws s3 rm "s3://askfirst-ai2-test-$acct" --recursive
aws s3 rb "s3://askfirst-ai2-test-$acct"
aws s3 rm "s3://askfirst-dev-data-$acct" --recursive                  # this bucket was created by the stack, just empty it
```

#### Step 5: Delete the SAM stack

```powershell
sam delete --stack-name askfirst-dev --no-prompts
aws cloudformation describe-stacks --stack-name askfirst-dev            # must report "does not exist"
```

This command deletes the 3 Java Lambda functions, the DynamoDB table, the 2 queues, the alarm, the (now empty) data bucket, and the SSM configuration parameters created by the stack. If `sam delete` fails on `DataBucket`, go back to step 4.

#### Step 6: Delete manually created items outside the stack

```powershell
# Secrets and the policy hash (created in 5.4 step 1)
aws ssm delete-parameters --names /askfirst/dev/telegram/bot_token /askfirst/dev/telegram/webhook_secret /askfirst/dev/config/policy_sha256

# The alarm's SNS topic (5.4 step 2)
aws sns delete-topic --topic-arn "arn:aws:sns:ap-southeast-1:${acct}:askfirst-dev-alarms"

# Log groups are not deleted along with the functions
foreach ($f in "webhook","worker","worker-dlq","ai") { aws logs delete-log-group --log-group-name "/aws/lambda/askfirst-dev-$f" }

# The bucket SAM uses for deployment packages (if no other stack uses SAM)
aws cloudformation describe-stacks --stack-name aws-sam-cli-managed-default --query "Stacks[0].Outputs[0].OutputValue" --output text
```

The `aws-sam-cli-managed-default-samclisourcebucket-...` bucket has versioning enabled, so you must also delete the old versions in the console (**Empty**) before deleting the `aws-sam-cli-managed-default` stack.

#### Step 7: Revoke access

1. In @BotFather: `/revoke` the bot token (or `/deletebot` if it is no longer needed).
2. IAM → Users → `ai2-dev` → Security credentials: deactivate and then delete the access key used for the workshop.
3. Keep AWS Budgets for one more month to make sure no charges remain, then delete it if you wish.

#### Final check

```powershell
aws lambda list-functions --query "Functions[?starts_with(FunctionName,'askfirst-dev')].FunctionName"
aws dynamodb list-tables --query "TableNames[?starts_with(@,'askfirst')]"
aws sqs list-queues --queue-name-prefix askfirst-dev
aws ecr describe-repositories --query "repositories[?starts_with(repositoryName,'askfirst')].repositoryName"
aws s3 ls | Select-String askfirst
aws ssm get-parameters-by-path --path /askfirst --recursive --query "Parameters[].Name"
```

Every command must return an empty list. A few days later, open **Billing → Bills** to make sure there are no new charges for Lambda, ECR, or S3.
