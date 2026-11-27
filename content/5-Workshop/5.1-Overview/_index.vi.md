---
title: "Tổng quan và kiến trúc"
date: 2026-11-27
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

#### Bài toán

Tin nhắn lừa đảo nhắm vào người lớn tuổi thường giống hệt tin thật: giả ngân hàng, giả công an, giả "phạt nguội", hay "con đang cấp cứu, chuyển tiền gấp". Điểm chung là chúng thúc người nhận làm ngay, trước khi kịp hỏi ai. Ask First tạo ra một chỗ để **hỏi trước**: người dùng chuyển tiếp nội dung cho bot, bot trả lời trong vài giây.

Bài toán kỹ thuật đi kèm:

- Người dùng gửi cả **ảnh chụp màn hình**, nên hệ thống phải đọc được chữ tiếng Việt trong ảnh.
- Nội dung có số điện thoại, số tài khoản, mã OTP. Ảnh thô **không được rời khỏi** hạ tầng của mình; chỉ văn bản đã che số mới được gửi tới LLM.
- Lượng tin ít và không đều (vài trăm tới vài nghìn lượt mỗi tháng), nên không thể trả tiền cho server chạy 24/7.

#### Giải pháp trên AWS

Hệ thống chia thành hai phần:

- **Phần nhận và điều phối** (Java 21, 3 Lambda): nhận update từ Telegram, gom các tin gửi liền nhau, xếp job vào hàng đợi, gửi kết quả về cho người dùng và người thân.
- **Phần phân tích** (Python 3.12, 1 Lambda container image): đọc chữ trong ảnh, che thông tin cá nhân, chạy bộ luật, tra knowledge base về các kịch bản lừa đảo, rồi mới hỏi LLM trên Amazon Bedrock nếu bộ luật chưa đủ chắc.

#### Sơ đồ kiến trúc

![Sơ đồ kiến trúc Ask First của nhóm: người dùng và Telegram bên trái; Region ap-southeast-1 gồm Lambda webhook, worker, worker-dlq, ai, SQS và DLQ, DynamoDB, S3, ECR; Bedrock và Bedrock Guardrails ở ap-southeast-2; khối bảo mật (IAM, SSM, KMS) và giám sát (CloudWatch, CloudTrail, Budgets, Alarm, SNS) ở dưới](/images/architecture.png)

#### Hành trình của một ảnh chụp màn hình

Sơ đồ có 17 mũi tên đánh số. Tôi gom chúng thành 5 chặng theo đường đi của một ảnh:

| Chặng | Mũi tên | Diễn ra gì | Thành phần |
|---|---|---|---|
| **A. Nhận** | 1–3 | Người dùng gửi ảnh. Telegram POST update tới Function URL của `webhook`. Request không có header `X-Telegram-Bot-Api-Secret-Token` đúng bị trả 401. `webhook` ghi tin vào vùng đệm `BUFFER#<chat>` trong DynamoDB. | Lambda `webhook`, DynamoDB |
| **B. Xếp hàng** | 4–5 | `webhook` trả "⏳" cho người dùng, đẩy một `AnalyzeJob` vào SQS với độ trễ 3 giây (để gom tin gửi liền nhau), rồi trả HTTP 200 cho Telegram. `worker` lấy job ra. | SQS `jobs`, Lambda `worker` |
| **C. Phân tích** | 6–12 | `worker` gọi `ai` (alias `live`), chỉ gửi `file_id` chứ không gửi ảnh. `ai` tự tải ảnh vào RAM bằng bot token đọc từ SSM, dùng model đã nạp từ S3 để OCR, che PII, chạy luật, mở thử link rút gọn, tra RAG, kiểm prompt attack bằng Guardrails rồi hỏi Bedrock. Kết luận được cache 24 giờ trong DynamoDB. | Lambda `ai`, S3, SSM, DynamoDB, Bedrock |
| **D. Trả lời** | 13–17 | `worker` sửa tin "⏳" thành kết quả, báo người thân nếu rủi ro cao, lưu `CHECK#` (chỉ metadata, TTL 30 ngày) và có thể hẹn hỏi lại sau 15 phút. | Lambda `worker`, Telegram |
| **E. Khi lỗi** | (đường đứt) | Job lỗi 5 lần sẽ vào DLQ. `worker-dlq` báo người dùng là bot đang bận, CloudWatch alarm chuyển sang `ALARM` và gửi email qua SNS. | SQS DLQ, `worker-dlq`, CloudWatch, SNS |

{{% notice note %}}
Trên môi trường chạy chung của nhóm, Bedrock nằm ở **một tài khoản khác** (tài khoản của AI1), nên `ai` phải `sts:AssumeRole` sang đó trước khi gọi Bedrock (mũi tên 11). Khi bạn tự làm theo workshop trong một tài khoản duy nhất, chỉ cần bật model Bedrock ngay trong tài khoản của bạn và **bỏ qua** bước assume role.
{{% /notice %}}

#### Dịch vụ AWS và lý do chọn

| Dịch vụ | Dùng để | Vì sao chọn, và đã cân nhắc gì |
|---|---|---|
| **AWS Lambda** (zip Java 21) | `webhook`, `worker`, `worker-dlq` | Trả tiền theo lượt gọi; Function URL cho sẵn endpoint HTTPS nên không cần API Gateway. |
| **AWS Lambda** (container image) | `ai` | Thư viện OCR và ONNX Runtime vượt giới hạn 250 MB của gói zip; image cho phép tới 10 GB. Chạy trên CPU, không cần GPU. |
| **Amazon ECR** | Chứa image `ai` | Lambda container image chỉ lấy image từ ECR. |
| **Amazon S3** | Model (OCR 32 MB, VietOCR 90 MB, embedder 123 MB) và index của knowledge base (0,3 MB) | Đổi model không phải build lại image; file có version theo prefix nên quay lại bản cũ dễ. |
| **Amazon SQS + DLQ** | Bộ đệm giữa phần nhận tin và phần phân tích | Telegram nhận HTTP 200 trong chưa tới 1 giây dù phân tích mất nhiều giây; thử lại, hoãn 3 giây để gom tin và chuyển job hỏng sang DLQ đều là tính năng sẵn của SQS. |
| **Amazon DynamoDB** | Một bảng cho người dùng, đồng ý điều khoản, vùng đệm tin, kết quả, cache | On-demand, TTL tự xoá dữ liệu cũ, không có chi phí khi không ai dùng. |
| **Amazon Bedrock** + **Guardrails** | LLM đánh giá (Qwen3 32B, dự phòng gpt-oss-120b) và chặn prompt attack | Trả theo token, model open-weight giá thấp; không phải tự vận hành GPU. |
| **SSM Parameter Store** | Bot token, webhook secret (`SecureString`), cấu hình stack | Secret không nằm trong code hay biến môi trường của Lambda. |
| **CloudWatch + SNS** | Log JSON, metric, alarm DLQ gửi email | Có sẵn cho mọi Lambda, không cần cài agent. |
| **AWS SAM / CloudFormation** | Hạ tầng dạng code (`infra/template.yaml`) | Một lệnh build + deploy, tạo lại được cả stack. |
| **IAM** | Role riêng cho từng Lambda, quyền tối thiểu | Ví dụ role của `ai` chỉ đọc được đúng bucket model và một tham số SSM. |

Không dùng: VPC và NAT Gateway (NAT riêng đã tốn khoảng 32 USD/tháng dù không có tin nào), API Gateway, Step Functions, EC2/SageMaker có GPU.

#### Đối chiếu AWS Well-Architected

Mỗi trụ cột kèm một bằng chứng tôi đã kiểm trên môi trường thật (chi tiết ở các mục sau).

| Trụ cột | Thiết kế | Bằng chứng đã kiểm |
|---|---|---|
| **Operational Excellence** | Stack backend bằng SAM; image `ai` gắn tag theo commit; Lambda `ai` có version + alias `live` để quay lại bản cũ bằng một lệnh. | Alias `live` đã qua 11 version, mỗi version ghi commit trong mô tả (5.5). |
| **Security** | Function URL chặn request thiếu secret; secret trong SSM `SecureString`; role tối thiểu; S3 chặn public; PII bị che trước khi tới Bedrock; log không chứa nội dung tin. | POST không có secret trả 401 (5.6); quét log CloudWatch không thấy nội dung tin hay token (5.7). |
| **Reliability** | SQS retry + DLQ, `worker-dlq` báo bận, cache verdict, chế độ "chỉ chạy luật" khi LLM lỗi, VietOCR lỗi thì vẫn trả kết quả RapidOCR. | Chặn `ai` bằng reserved concurrency 0: job thử 5 lần, vào DLQ, người dùng nhận đúng một tin "bận" (5.7). |
| **Performance Efficiency** | `ai` 3008 MB (khoảng 1,7 vCPU); model nạp sẵn ở pha INIT; model nhỏ chạy CPU (ONNX). | Đo 3 mức memory: 1769 MB chạm trần INIT 10 giây, 3008 MB INIT 6,2 giây (5.7). |
| **Cost Optimization** | Không có thành phần tính tiền theo giờ; DynamoDB on-demand; bỏ qua LLM khi luật đã chắc. | Chi phí thực tế 1–26/11/2026 của cả tài khoản: khoảng 0,10 USD trước credit (5.9). |
| **Sustainability** | Không giữ máy nào bật để chờ việc; khi bot im lặng, phần tính toán cũng về 0. | Không chấm ở FCAJ, ghi để đủ checklist. |
