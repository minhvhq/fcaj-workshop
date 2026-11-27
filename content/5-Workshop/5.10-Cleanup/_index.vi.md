---
title: "Dọn dẹp tài nguyên"
date: 2026-11-27
weight: 10
chapter: false
pre: " <b> 5.10. </b> "
---

Hệ thống gần như không tốn tiền khi không có tin, nhưng vẫn còn vài khoản tính theo dung lượng (ECR, S3, log) và các secret còn hiệu lực. Làm xong workshop thì dọn theo **đúng thứ tự** dưới đây. Thứ tự quan trọng vì: CloudFormation không xoá được bucket còn object; Lambda `ai` và tài nguyên tạo bằng CLI không thuộc stack nên `sam delete` không đụng tới.

{{% notice warning %}}
Các bước 3–7 **không hoàn tác được**: bảng DynamoDB (kèm bản sao lưu PITR), model trên S3, image, token bot đều mất. Môi trường của nhóm đang là môi trường chạy chung tới hết chương trình nên tôi **chưa** chạy phần này trên nó; các lệnh dưới là quy trình sẽ dùng khi kết thúc.
{{% /notice %}}

```powershell
$env:AWS_PROFILE = "ai2test"; $env:AWS_DEFAULT_REGION = "ap-southeast-1"
$acct = aws sts get-caller-identity --query Account --output text
```

#### Bước 1: Ngắt Telegram khỏi hệ thống

Gỡ webhook trước để Telegram ngừng gửi update vào Function URL trong lúc đang xoá:

```powershell
$env:TELEGRAM_BOT_TOKEN = aws ssm get-parameter --name /askfirst/dev/telegram/bot_token --with-decryption --query Parameter.Value --output text
python scripts/set_webhook.py --delete
Remove-Item Env:TELEGRAM_BOT_TOKEN
```

#### Bước 2: Xoá Lambda `ai` và role của nó (tạo bằng CLI ở 5.5)

```powershell
aws lambda delete-alias --function-name askfirst-dev-ai --name live
aws lambda delete-function --function-name askfirst-dev-ai          # xoá luôn mọi version
aws iam delete-role-policy --role-name askfirst-ai2test-ai-role --policy-name ai-least-privilege
aws iam detach-role-policy --role-name askfirst-ai2test-ai-role `
  --policy-arn arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole
aws iam delete-role --role-name askfirst-ai2test-ai-role
```

#### Bước 3: Xoá image và repository ECR

```powershell
aws ecr delete-repository --repository-name askfirst-dev-ai --force   # --force xoá cả image bên trong
```

#### Bước 4: Làm rỗng và xoá bucket

```powershell
aws s3 rm "s3://askfirst-ai2-test-$acct" --recursive
aws s3 rb "s3://askfirst-ai2-test-$acct"
aws s3 rm "s3://askfirst-dev-data-$acct" --recursive                  # bucket này do stack tạo, chỉ cần làm rỗng
```

#### Bước 5: Xoá stack SAM

```powershell
sam delete --stack-name askfirst-dev --no-prompts
aws cloudformation describe-stacks --stack-name askfirst-dev            # phải báo "does not exist"
```

Lệnh này xoá 3 Lambda Java, bảng DynamoDB, 2 queue, alarm, bucket dữ liệu (đã rỗng) và các tham số SSM cấu hình mà stack tạo. Nếu `sam delete` báo lỗi ở `DataBucket`, quay lại bước 4.

#### Bước 6: Xoá những thứ tạo bằng tay ngoài stack

```powershell
# Secret và mã băm chính sách (tạo ở 5.4 bước 1)
aws ssm delete-parameters --names /askfirst/dev/telegram/bot_token /askfirst/dev/telegram/webhook_secret /askfirst/dev/config/policy_sha256

# SNS topic của alarm (5.4 bước 2)
aws sns delete-topic --topic-arn "arn:aws:sns:ap-southeast-1:${acct}:askfirst-dev-alarms"

# Log group không bị xoá theo function
foreach ($f in "webhook","worker","worker-dlq","ai") { aws logs delete-log-group --log-group-name "/aws/lambda/askfirst-dev-$f" }

# Bucket SAM dùng để chứa gói deploy (nếu không còn stack nào khác dùng SAM)
aws cloudformation describe-stacks --stack-name aws-sam-cli-managed-default --query "Stacks[0].Outputs[0].OutputValue" --output text
```

Bucket `aws-sam-cli-managed-default-samclisourcebucket-...` có bật versioning nên phải xoá cả các phiên bản cũ trên console (**Empty**) trước khi xoá stack `aws-sam-cli-managed-default`.

#### Bước 7: Thu hồi quyền truy cập

1. Trong @BotFather: `/revoke` token của bot (hoặc `/deletebot` nếu không dùng nữa).
2. IAM → Users → `ai2-dev` → Security credentials: vô hiệu hoá rồi xoá access key dùng cho workshop.
3. Giữ AWS Budgets thêm một tháng để chắc không còn chi phí phát sinh, sau đó xoá nếu muốn.

#### Kiểm tra cuối

```powershell
aws lambda list-functions --query "Functions[?starts_with(FunctionName,'askfirst-dev')].FunctionName"
aws dynamodb list-tables --query "TableNames[?starts_with(@,'askfirst')]"
aws sqs list-queues --queue-name-prefix askfirst-dev
aws ecr describe-repositories --query "repositories[?starts_with(repositoryName,'askfirst')].repositoryName"
aws s3 ls | Select-String askfirst
aws ssm get-parameters-by-path --path /askfirst --recursive --query "Parameters[].Name"
```

Mọi lệnh phải trả danh sách rỗng. Vài ngày sau, mở **Billing → Bills** để chắc không còn dòng chi phí mới cho Lambda, ECR, S3.
