---
title: "Các bài blog đã đăng"
date: 2026-11-27
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

Mục này tập hợp các bài viết kỹ thuật của nhóm đã đăng trên [AWS Study Group VN](https://www.facebook.com/groups/awsstudygroupfcj), dùng chung theo quy định của FCAJ, cùng bài viết riêng của tôi. Mỗi trang ghi rõ tác giả và kèm tóm tắt do tôi viết lại.

### [Blog 1 - Thiết kế luồng dữ liệu IoT (MQTT → AWS IoT Core → Glue → S3) tối ưu chi phí](3.1-Blog1/)
Tác giả: Ngô Thị Kim Ngân. Gom hàng triệu bản tin JSON nhỏ thành file Parquet bằng AWS Glue để giảm phí request S3 và phí quét của Athena.

### [Blog 2 - Test luồng SQS + DLQ + DynamoDB trên máy với LocalStack và Java 21](3.2-Blog2/)
Tác giả: Ngô Thị Kim Ngân. Giả lập dịch vụ AWS bằng LocalStack để test cả luồng chạy đúng lẫn luồng rơi vào DLQ trước khi deploy bằng AWS SAM.

### [Blog 3 - Lambda và EventBridge: tự cập nhật dữ liệu hằng ngày theo hướng event-driven](3.3-Blog3/)
Tác giả: Trí Đức Võ. Dùng EventBridge Scheduler, hai hàm Lambda và S3/CloudFront để dựng một trang tự cập nhật, kèm hai lỗi thiết kế cần tránh.

### [Blog 4 - OCR tiếng Việt trên AWS Lambda: khi model đọc "chuyển tiền" thành "chuyn tin"](3.4-Blog4/)
Bài của tôi, đã gửi lên group nhưng chưa được duyệt đăng. Kể lại cách tìm ra nguyên nhân model OCR làm rơi chữ cái tiếng Việt, cách dùng VietOCR (ONNX) đọc lại có chọn lọc và ghép theo từng từ, cách nạp model từ S3 ở pha INIT của Lambda, và số đo trên Lambda thật ở ap-southeast-1.
