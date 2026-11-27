---
title: "Nhật ký tuần 3"
date: 2026-09-28
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---


### Mục tiêu tuần 3

* Đóng gói code Python thành container image và chạy trên AWS Lambda.
* Hiểu cách memory của Lambda quyết định CPU, và pha INIT ảnh hưởng tới cold start ra sao.
* Dùng SQS có dead-letter queue và xem log bằng CloudWatch Logs Insights.
* Xem AWS Cloud & AI Day Vietnam 2026 qua Watch Party.

### Công việc trong tuần

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Tạo repository trên Amazon ECR, đẩy một image thử bằng `docker push` <br> - Đọc vì sao Lambda cần image một kiến trúc (`--provenance=false`) | 28/09/2026 | 28/09/2026 | [Amazon ECR](https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html) |
| 3 | - Dự AWS Cloud & AI Day Vietnam 2026 Watch Party <br> - Ghi lại các phần nói về AI tạo sinh và serverless | 29/09/2026 | 29/09/2026 | Mục [Sự kiện](../../4-eventparticipated/) |
| 4 | - Viết Lambda từ image `public.ecr.aws/lambda/python:3.12`, chạy thử local bằng Lambda Runtime Interface Emulator <br> - Đặt timeout, memory và ephemeral storage `/tmp` | 30/09/2026 | 30/09/2026 | [Lambda container image](https://docs.aws.amazon.com/lambda/latest/dg/images-create.html), [RIE](https://docs.aws.amazon.com/lambda/latest/dg/images-test.html) |
| 5 | - Đọc vòng đời môi trường thực thi Lambda: INIT, INVOKE, SHUTDOWN <br> - Đo thử cold start khi nạp một thư viện lớn ngoài handler và trong handler | 01/10/2026 | 01/10/2026 | [Lambda execution environment](https://docs.aws.amazon.com/lambda/latest/dg/lambda-runtime-environment.html) |
| 6 | - SQS: visibility timeout, `maxReceiveCount`, DLQ và redrive <br> - Viết vài câu truy vấn CloudWatch Logs Insights cho log JSON | 02/10/2026 | 02/10/2026 | [SQS DLQ](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-dead-letter-queues.html), [Logs Insights](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/AnalyzingLogData.html) |

### Kết quả đạt được

* Một Lambda dạng container chạy được cả local (RIE) lẫn trên AWS.
* Hiểu rằng Lambda cấp CPU theo memory và pha INIT có giới hạn thời gian; kiến thức này dùng lại khi chọn 3008 MB cho Lambda `ai` ở tuần 10.
* Viết được truy vấn Logs Insights lọc log theo trường JSON.


### Bài học rút ra

* Image càng lớn thì cold start càng chậm, nên model nặng tốt hơn là tải từ S3 thay vì đóng vào image.
* Nên thử DLQ bằng cách cố tình làm lỗi, vì cấu hình đúng chưa chắc hành vi đúng.
