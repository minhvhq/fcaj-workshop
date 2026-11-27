---
title: "Workshop"
date: 2026-11-27
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Tự dựng môi trường chạy thật cho Ask First trên một tài khoản AWS: từ model AI trên S3 tới bot Telegram

Ask First ("Hỏi Trước") là Telegram bot giúp người cao tuổi kiểm tra một tin nhắn, đường link hoặc ảnh chụp màn hình đáng ngờ **trước khi** làm theo. Bot trả lời bằng tiếng Việt: tin đó rủi ro tới mức nào, giống kiểu lừa đảo nào, và nên làm gì ngay.

Workshop này đi theo đúng con đường tôi đã làm khi dựng môi trường chạy chung của nhóm trên tài khoản AWS cá nhân (từ 19/11/2026): **đưa từng thành phần lên AWS, kiểm thử nó, rồi mới nối thành cả chuỗi**. Thứ tự đó giúp khi có lỗi, ta biết ngay lỗi nằm ở thành phần nào.

Kết thúc workshop, bạn có:

- một S3 bucket chứa model AI (OCR, VietOCR, embedder) và index của knowledge base, có kiểm SHA-256;
- stack AWS SAM gồm 3 Lambda Java (`webhook`, `worker`, `worker-dlq`), DynamoDB, SQS kèm DLQ, CloudWatch alarm gửi email qua SNS;
- Lambda `ai` (Python 3.12, container image trên Amazon ECR) chạy OCR, mask dữ liệu cá nhân, bộ luật, RAG và gọi Amazon Bedrock;
- một bot Telegram trả lời thật, cùng log, alarm và số đo để chứng minh hệ thống chạy đúng.

{{% notice info %}}
Toàn bộ lệnh trong workshop chạy bằng **PowerShell trên Windows**, vì đó là máy tôi dùng để deploy. Lệnh AWS CLI giống hệt trên macOS/Linux; chỉ khác cú pháp đặt biến (`$env:X = "..."` thay cho `export X=...`).
{{% /notice %}}

#### Nội dung

1. [Tổng quan và kiến trúc](5.1-overview/)
2. [Chuẩn bị tài khoản và công cụ](5.2-prerequisites/)
3. [Đưa model AI lên S3](5.3-model-artifacts/)
4. [Deploy stack backend bằng AWS SAM](5.4-deploy-backend/)
5. [Đóng gói và deploy Lambda AI](5.5-deploy-ai-lambda/)
6. [Nối bot Telegram](5.6-connect-telegram/)
7. [Kiểm thử và đo lường](5.7-testing/)
8. [Giám sát và cảnh báo](5.8-monitoring/)
9. [Chi phí và tối ưu](5.9-cost/)
10. [Dọn dẹp tài nguyên](5.10-cleanup/)
11. [Demo và bài học](5.11-demo-lessons/)
