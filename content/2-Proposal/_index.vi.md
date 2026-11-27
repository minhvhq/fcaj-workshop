---
title: "Bản đề xuất"
date: 2026-11-27
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Đề xuất dự án: Ask First (Hỏi Trước)

*Người viết: Võ Hoàng Quốc Minh, thành viên AI2 của nhóm. Bản đề xuất này trình bày dự án chung của nhóm theo góc nhìn phần việc tôi phụ trách: pipeline AI chạy local trong Lambda, dữ liệu đo và eval, và môi trường AWS dùng chung của nhóm.*

### 1. Tổng quan bài toán và giải pháp

**Bài toán trong một câu:** người cao tuổi nhận một tin nhắn "phạt nguội", "hoàn tiền bảo hiểm" hay "công an mời làm việc" và cần một ai đó nói cho họ biết có nên làm theo hay không, trước khi họ bấm link hoặc đọc mã OTP.

**Giải pháp:** một bot Telegram tên *Hỏi Trước*. Người dùng chuyển tiếp tin, dán link hoặc gửi ảnh chụp màn hình. Bot trả về một kết luận ngắn bằng tiếng Việt, có mức rủi ro, lý do dựa trên chính câu chữ trong tin, việc nên làm và nguồn cảnh báo chính thức liên quan. Nếu người dùng đã đồng ý liên kết, người thân được báo khi tin có rủi ro cao.

Ba điểm làm nên thiết kế, cũng là ba phần tôi tham gia nhiều nhất:

1. **Phần lớn việc "hiểu tin" chạy local trong Lambda**, không tốn tiền theo token: đọc chữ trong ảnh (OCR), che thông tin cá nhân, chấm điểm bằng luật, phân tích URL, tìm bài cảnh báo liên quan (RAG). Chỉ bước phân loại cuối mới gọi một LLM open-weight trên Amazon Bedrock, và chỉ với văn bản đã che.
2. **Mọi thay đổi về AI phải qua số đo**: mỗi thành phần có bộ dữ liệu, script đo chạy lại được và ngưỡng đạt rõ ràng; eval chạy trong CI để chặn PR làm giảm chất lượng.
3. **Toàn bộ chạy serverless trên AWS**, không có máy chủ thường trực, không VPC/NAT; chi phí đi theo số lượt kiểm tra.

| Thông tin | Nội dung |
|---|---|
| Kênh | Telegram Bot API, webhook |
| Nhóm | 4 người: BE1, BE2 (Java 21), AI1, AI2 (Python 3.12) |
| Khung thời gian | 6 tuần, 19/10/2026 – 29/11/2026 |
| Region | Lambda, SQS, DynamoDB, S3 ở `ap-southeast-1`; Bedrock ở `ap-southeast-2` |
| Mã nguồn | [ask-first-fcaj/ask-first](https://github.com/ask-first-fcaj/ask-first) |

### 2. Vấn đề cần giải quyết

**Tin lừa đảo được viết để người đọc hành động ngay.** Một tin giả mạo thường có ba thành phần: một lý do gây lo lắng (nợ cước, phạt giao thông, người thân gặp nạn), một áp lực thời gian ("trong 24 giờ"), và một hành động có hại (bấm link, cài ứng dụng, đọc mã OTP, chuyển tiền). Khi người đọc làm theo hành động thứ ba thì thường đã muộn.

**Người cao tuổi khó tự kiểm tra.** Họ khó phân biệt tên miền thật với tên miền nhái, ít khi biết số tổng đài chính thức của ngân hàng, và thường nhận tin khi không có con cháu bên cạnh. Ngân hàng chủ yếu chặn ở bước chuyển tiền, tức là sau khi kẻ gian đã thuyết phục xong.

**Ba vấn đề kỹ thuật tôi phải giải quyết trong phần AI:**

| Vấn đề | Vì sao khó | Hướng xử lý trong dự án |
|---|---|---|
| Phần lớn tin lừa đến dưới dạng ảnh chụp màn hình | Ảnh có tiếng Việt có dấu, URL bị xuống dòng hoặc mất ký tự đầu | OCR local, sửa URL bằng regex, đọc lại dòng tiếng Việt bằng VietOCR |
| Tin có số điện thoại, số tài khoản, CCCD, OTP của người dùng | Không được đưa dữ liệu cá nhân ra ngoài Lambda | Che PII bằng token (`[SĐT_1]`, `[OTP_1]`) trước mọi lời gọi Bedrock |
| LLM có thể bịa, chậm, hoặc lỗi | Bot không được im lặng hay trả lời sai lệch | Tầng luật độc lập với LLM, có sàn điểm; RAG lấy bài cảnh báo thật để trích dẫn |

### 3. Mục tiêu và phạm vi

**Mục tiêu chung của dự án** là một MVP chạy thật trên AWS, demo được ngày 27/11/2026 và nộp workshop ngày 29/11/2026.

**Tiêu chí thành công đo được** (ngưỡng đặt từ đầu trong `docs/system-architecture.md` §1.3 và các gate ở `docs/implementation-plan.md` §4):

| Nhóm | Chỉ số | Ngưỡng đạt | Đo bằng |
|---|---|---|---|
| Chất lượng phân loại | Recall trên tin lừa | ≥ 90% | `eval/run_eval.py`, chặn trong CI |
| Chất lượng phân loại | Báo nhầm trên tin lành nhưng trông đáng ngờ | ≤ 10% | như trên |
| OCR (gate G2) | Chép đúng URL trên tập ảnh độc lập | ≥ 90% | `eval/ocr_benchmark.py` |
| OCR (gate G2) | CER | ≤ 10% | như trên |
| RAG (mốc M3) | Recall@4 scenario card trên `real_scam` | ≥ 90% | eval RAG |
| RAG (mốc M3) | Thời gian truy vấn | < 100 ms | đo trên Lambda |
| Độ trễ | p95 đầu-cuối khi container ấm, văn bản và ảnh | ≤ 15 giây | CloudWatch, log `latency_ms` |
| Chi phí | Mỗi lượt kiểm tra | ≤ 0,005 USD | đếm token thực tế |
| Quyền riêng tư | Số mẫu eval lọt số hoặc OTP vào prompt | 0 | test bảo mật của `mask()` |

**Trong phạm vi MVP:**

- Đầu vào: tin chữ, tin chuyển tiếp, link, ảnh chụp màn hình và album; nhiều tin gửi liền được gom lại.
- Pipeline AI: chuẩn hoá, OCR, che PII, luật, phân tích URL (kể cả mở link rút gọn có chống SSRF), RAG, Guardrails, judge LLM, fuse, trả lời theo template.
- Người dùng: đồng ý chủ động, rút đồng ý, xoá dữ liệu, liên kết và cảnh báo người thân, quản lý bằng lệnh trong chat.
- Vận hành: kill switch chuyển về chế độ chỉ luật, alarm DLQ, budget.

**Ngoài phạm vi 6 tuần** (đã hoãn trong kế hoạch v3): voice/ASR, VLM cho ảnh khó, hỏi thêm nhiều lượt, Telegram Mini App, Phase 2 (kho tri thức sống), pilot với hộ gia đình ngoài nhóm.

**Phần việc tôi nhận trong phạm vi trên:** bộ ảnh và spike OCR (AI-20), che PII (AI-22), chuẩn hoá và luật (AI-23), URL intel (AI-24a, nối `url_net` ở AI-24b), knowledge base và validator (AI-25), tải media Telegram và `S3ArtifactStore` (AI-03b), RAG local (AI-29, INT-03), eval trong CI (AI-09), VietOCR đọc lại tiếng Việt (AI-26), nối `PIPELINE_IMPL=real` với S3, DynamoDB, Telegram (INT-01), và dựng môi trường AWS chạy chung của nhóm.

### 4. Kiến trúc giải pháp

![Sơ đồ kiến trúc Ask First trên AWS: Telegram gọi Lambda webhook qua Function URL, SQS đưa job sang Lambda worker, worker gọi Lambda ai (pipeline AI local), ai gọi Amazon Bedrock ở Sydney; dữ liệu ở DynamoDB và S3, giám sát bằng CloudWatch và SNS](/images/architecture.png)

*Sơ đồ chung của nhóm, chỉ vẽ phần MVP đang chạy.*

#### 4.1 Ba Lambda, ba trách nhiệm

- `webhook` (Java): nhận update, kiểm `secret_token`, chống trùng, gom tin, trả 200 cho Telegram thật nhanh rồi đẩy job vào SQS.
- `worker` (Java): đọc job, gọi `ai`, gửi kết luận, báo người thân, ghi metadata.
- `ai` (Python, container image 3008 MB): toàn bộ pipeline AI. Đây là phần tôi viết và vận hành nhiều nhất.

Ranh giới Java ↔ Python là một hợp đồng JSON Schema (gate G0). Nhờ vậy phía AI phát triển và đo được hoàn toàn bằng Python, không cần chờ backend.

#### 4.2 Pipeline AI bên trong Lambda `ai`

| Bước | Chạy ở đâu | Việc làm | Số đo chính (nguồn) |
|---|---|---|---|
| Tải media | Lambda `ai` | Lấy ảnh bằng `file_id` từ Telegram, chỉ giữ trong bộ nhớ và `/tmp`; ảnh không đi qua payload Java | AI-03b |
| OCR | Local, ONNX | RapidOCR (PP-OCRv6 small) tìm dòng và đọc URL/SĐT; VietOCR đọc lại dòng có dấu; sửa đầu URL bằng regex | Tập độc lập 16 ảnh: URL 17/18 (94%), CER 7,2% ([ocr-spike](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/ocr-spike.md)); từ có dấu đúng 37% → 89% sau VietOCR ([ocr-vietocr](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/ocr-vietocr.md)) |
| Che PII | Local, regex + từ khoá | SĐT, STK, CCCD, OTP, email, số viết bằng chữ → token | Heldout: 130/132 PII bị che ([mask-pii](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/mask-pii.md)) |
| Luật | Local, `rules.yaml` | Dấu hiệu như xin OTP, giục gấp, mạo danh cơ quan; có sàn điểm không phụ thuộc LLM | Luật OTP bắt 100/100 tin lừa, báo nhầm 1/100 tin thật ([otp-rules](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/otp-rules.md)) |
| URL intel | Local + mạng có kiểm soát | Tách tên miền, nhận diện nhái thương hiệu, mở link rút gọn qua `url_net` có chống SSRF | AI-24a, AI-24b (PR #175) |
| RAG | Local, e5-small int8 | Tìm scenario card và bài cảnh báo `.gov.vn` gần nhất bằng cosine trong RAM | Máy dev: Recall@4 35/38 = 92,1%; trên Lambda: p95 45 ms ([lambda-p95](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/lambda-p95.md)) |
| Guard + Judge | Bedrock (Sydney) | Guardrails `PROMPT_ATTACK`, rồi Qwen3 32B trả JSON có cấu trúc; gpt-oss-120b là dự phòng | Văn bản p95 3,5 s khi container ấm |
| Fuse + Respond | Local | Ghép điểm luật và điểm LLM thành 4 mức, trả lời bằng template, không để LLM viết tự do | Không tin nào quá 600 ký tự ([readability](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/readability.md)) |

Model OCR và RAG được nạp sẵn ở pha INIT của Lambda (PR #173), nên lượt đầu sau cold start không phải chờ tải model. Trọng số nằm trên S3 kèm checksum, đổi model không cần build lại image.

#### 4.3 Dịch vụ AWS và lý do chọn

| Dịch vụ | Dùng cho việc gì | Lý do chọn, phương án đã loại |
|---|---|---|
| AWS Lambda (Function URL) | 3 hàm `webhook`, `worker`, `ai` | Tải ít và không đều, trả theo lượt. Function URL đủ cho webhook nên không cần API Gateway |
| Lambda container image + Amazon ECR | Đóng gói onnxruntime, RapidOCR, VietOCR, embedder | Gói zip giới hạn 250 MB, không đủ cho thư viện ML; image tối đa 10 GB, nhóm tự đặt trần 2 GB |
| Amazon SQS + DLQ | Hàng đợi `webhook` → `worker`, gom tin bằng `DelaySeconds` | Tách phần trả lời Telegram khỏi phần xử lý lâu; retry có sẵn; DLQ giữ job lỗi để xử lý tay |
| Amazon DynamoDB (on-demand) | Người dùng, đồng ý, người thân, metadata kiểm tra, cache `CACHE#`, `URL#`, cờ `FLAGS` | Truy cập theo khoá, TTL để tự xoá dữ liệu, không phải quản lý dung lượng |
| Amazon S3 | Trọng số OCR/embedding, index RAG, mẫu đóng góp tự xoá sau 30 ngày | Rẻ, có version và checksum cho artifact |
| Amazon Bedrock + Guardrails | Judge LLM và lọc prompt injection | Model open-weight trả theo token, không vận hành GPU. Loại EC2/SageMaker GPU vì đắt hơn nhiều ở tải này (ARCH §6.0 ước khoảng 530 USD/tháng nếu tự host) |
| SSM Parameter Store | Bot token, webhook secret | Không để secret trong code hay trong biến môi trường |
| CloudWatch + SNS | Log JSON theo `trace_id`, alarm DLQ gửi email | Đủ cho quy mô MVP |
| IAM | Role tách theo hàm; `ai` chỉ được đọc/ghi khoá `CACHE#`, `URL#` | Least privilege |

Không dùng: VPC/NAT, Step Functions, OpenSearch hay vector DB riêng (corpus khoảng 100 bài, tính cosine trong RAM là đủ), Textract và Transcribe (đã có OCR local, voice đã hoãn).

#### 4.4 Môi trường AWS chạy chung của nhóm

Từ 19/11/2026, khi tài khoản của AI1 chỉ còn dùng được Bedrock, tôi dựng phần AWS trên tài khoản riêng của mình. Ngày 24/11/2026 nhóm chốt dùng tài khoản này làm môi trường chạy chung. Bedrock vẫn ở tài khoản của AI1, Lambda `ai` gọi sang qua một role (`BEDROCK_ROLE_ARN`).

Các bước tôi làm và kiểm theo thứ tự:

| Bước | Nội dung |
|---|---|
| T0 | IAM user riêng, bật MFA cho root, tạo AWS Budgets có email cảnh báo, tạo bot Telegram test |
| T4–T6 | Bucket S3 cho artifact, bảng DynamoDB cho cache, SSM cho bot token, thử tải ảnh từ Telegram |
| T7–T9 | Build image, tạo Lambda `ai`, nối `PIPELINE_IMPL=real` (PR #173), invoke bằng fixtures trong `contracts/fixtures/` |
| T10–T11 | Nối `url_net`, chạy cả chuỗi `webhook` → `worker` → `ai` → Telegram với stack SAM của backend |
| T12–T13 | Đo chi phí, chuẩn bị dọn dẹp theo hướng dẫn clean-up của nhóm |

Một số đo khi dựng môi trường dùng để chọn cấu hình: với RAG và OCR nạp sẵn, Init của `ai` ở 3008 MB là 6,2 s, ở 2048 MB là 7,1 s, còn ở 1769 MB chạm trần 10 s. Vì vậy tôi đề xuất giữ `ai` ở 3008 MB và `/tmp` tối thiểu 512 MB.

### 5. Kế hoạch và timeline

Kế hoạch v3 nén dự án từ 12 tuần xuống 6 tuần (19/10 – 29/11/2026). Ba track backend, AI và chung chạy song song, gặp nhau ở gate và mốc.

**Gate (điểm phải ra quyết định):**

| Gate | Hạn | Điều kiện | Phần của tôi |
|---|---|---|---|
| G0: Contracts | W1, 23/10 | Schema `models.py`, fixtures, `StubPipeline`, contract test xanh trong CI | Đọc và dùng contract để viết phần dựng `Bundle` từ tin Telegram |
| G1: Chốt LLM | W2, 30/10 | Trên 100 mẫu: JSON hợp lệ ≥ 98%, p95 ≤ 5 s, recall sơ bộ ≥ 85% | AI1 làm chính; nhóm chốt Qwen3 32B (ADR-002) |
| G2: Chốt OCR | W2, 30/10 | Chép đúng URL ≥ 90%, CER ≤ 10% trên tập ảnh độc lập | Dựng 50 ảnh, chạy 3 stack OCR, kiểm thêm 16 ảnh độc lập; số đo này là căn cứ để AI1 chốt RapidOCR (ADR-003) |

**Mốc:**

| Mốc | Tuần | Nội dung | Phần của tôi |
|---|---|---|---|
| M1: Văn bản end-to-end | W3 (06/11) | Tin "phạt nguội" có link giả → kết luận thật | Che PII, luật, URL intel cho pipeline văn bản |
| M2: Ảnh + người thân | W4 (13/11) | Ảnh → OCR local → kết luận; cảnh báo người thân | Tải media, `S3ArtifactStore`, sửa URL trong OCR |
| M3: RAG | W5 (20/11) | Kết luận kèm trích nguồn cảnh báo | Embedder e5-small, retriever, KB `kb/alerts.yaml`, nối RAG vào `ai` |
| M4: MVP freeze | W5 (20/11) | Đủ FR mức MVP, eval chạy trong CI | Eval gate trong CI, đo mask 500 mẫu, đo luật OTP, đo độ dễ đọc |
| M5: Test nội bộ | W6 (đến 25/11) | Nhóm và người nhà dùng thử | Vận hành môi trường chung, test DLQ, sửa lỗi tích hợp |
| M6: Thuyết trình | W6 (27/11) | Slide, demo, báo cáo eval và chi phí | Số đo OCR, VietOCR, độ trễ trên Lambda |
| M7: Nộp workshop | W6 (29/11) | Mỗi người nộp site riêng kèm worklog | Site này |

Sau M4 nhóm chỉ sửa bug, chỉnh prompt hoặc ngưỡng (qua eval gate) và làm hạ tầng. Thứ tự cắt tiếp nếu trễ đã ghi sẵn ở `implementation-plan.md` §9.

### 6. Dự toán chi phí

**Giả định tải** (theo `docs/system-architecture.md` §12 và `docs/results/cost-estimate.md`):

- 3.000 lượt kiểm tra mỗi tháng: khoảng 2.000 tin chữ và 1.000 ảnh; chưa có voice và VLM.
- 25% lượt được trả lời bằng fast path hoặc cache, còn 2.250 lượt gọi LLM. Tỉ lệ 25% **chưa kiểm chứng**.
- Mỗi lượt có LLM tốn 0,000792 USD tiền token (đo ở eval cuối với prompt `judge_v3`, đã gồm leo thang sang model dự phòng).
- Lambda `ai` 5 s mỗi lượt ở 3008 MB (làm tròn lên từ đo trên máy dev); `worker` 5 s, `webhook` 0,5 s ở 512 MB.
- Các dịch vụ ngoài Bedrock tính bằng [AWS Pricing Calculator](https://calculator.aws/#/estimate?id=073f413f6ac370de48bf3bff0ba10a7b870ae807) (24/11/2026, `ap-southeast-1`, chế độ "Without Free Tier"). Bedrock tính tay vì Calculator không cho chọn Qwen3 ở Sydney.

Bảng dưới gom theo vai trò trong kiến trúc để thấy phần AI local gần như không tốn tiền token:

| Nhóm | Dịch vụ | Không Free Tier (USD/tháng) | Có always-free (USD/tháng) | Nguồn số |
|---|---|---|---|---|
| Suy luận LLM | Bedrock Qwen3 32B + gpt-oss-120b | 1,78 | 1,78 | Tính tay: 2.250 × 0,000792 |
| Suy luận LLM | Bedrock Guardrails | 0,34 | 0,34 | Tính tay: 2.250 × 0,15 USD/1.000 text unit |
| AI local | Lambda `ai` (OCR, mask, luật, RAG) | 0,73 | 0 | Pricing Calculator |
| AI local | ECR (image `ai` ≈ 2 GB) | 0,20 | 0,20 | Pricing Calculator |
| AI local | S3 (artifact, index, mẫu đóng góp) | 0,03 | 0,03 | Pricing Calculator |
| Điều phối | Lambda `worker` + `webhook` | 0,16 | 0 | Pricing Calculator |
| Điều phối | SQS (gồm long poll khi hàng đợi rỗng) | 0,28 | 0 | Pricing Calculator |
| Dữ liệu | DynamoDB on-demand | 0,29 | < 0,05 | Pricing Calculator |
| Giám sát | CloudWatch (log, metric, alarm, dashboard) | 2,35 | 0 | Pricing Calculator |
| Giám sát | SNS, SSM Parameter Store | ≈ 0 | 0 | Không đưa vào Calculator |
| **Tổng** | | **≈ 6,2** | **≈ 2,4** | `docs/results/cost-estimate.md` |

Đọc bảng:

- Phần Calculator (không gồm Bedrock) là 4,04 USD/tháng, tức 48,48 USD cho 12 tháng, không có chi phí trả trước.
- Nếu mọi lượt đều gọi LLM thì phần Bedrock lên khoảng 2,7 USD/tháng; nếu thêm token ở mức p95 thì khoảng 4,6 USD/tháng. Cả hai vẫn thấp hơn nhiều so với trần 50 USD/tháng.
- Bảng chưa gồm thuế; hoá đơn thật có thêm khoảng 10% (đối chiếu Cost Explorer của nhóm).
- So sánh trong ARCH §12: phương án v1 dùng Claude + Transcribe + Cohere ước 5–15 USD/tháng, tự host GPU ước khoảng 530 USD/tháng.

**Cách giữ chi phí thấp:** AI local-first (OCR, embedding, luật không tính token); fast path và cache bỏ qua LLM khi luật đã đủ chắc; kill switch `FLAGS` tắt judge hoặc tắt nhận ảnh khi có spam; rate limit theo người dùng; lifecycle S3 xoá `donated/` sau 30 ngày; đề xuất thêm `RetentionInDays: 7` cho log và lifecycle policy cho repo ECR.

### 7. Rủi ro và giảm thiểu

| Rủi ro | Dấu hiệu nhận biết | Giảm thiểu |
|---|---|---|
| OCR đọc sai URL hoặc mất dấu tiếng Việt, làm luật không bật | Tỉ lệ chép đúng URL hoặc từ có dấu giảm trên tập ảnh | Sửa URL bằng regex giữ cả bản gốc và bản sửa; VietOCR đọc lại dòng có dấu; đo lại trên tập 16 ảnh độc lập trước khi chốt |
| Số đo lạc quan vì luật và regex được chỉnh trên chính bộ dữ liệu | Kết quả tốt ở bộ dev nhưng kém ở bộ mới | Tách dev/heldout (mask PII 349/151), tập OCR độc lập chạy đúng một lần, ghi rõ hạn chế trong từng báo cáo |
| Lọt dữ liệu cá nhân sang Bedrock | Test `mask()` có mẫu lọt số | Viết che PII như test bảo mật; CCCD 12 số được che kể cả khi trùng mã đơn hàng (chấp nhận che nhầm) |
| Cold start và độ trễ ảnh vượt 15 s | Init `ai` gần trần 10 s; p95 ảnh đo được 17,0 s | Nạp model ở INIT, giữ 3008 MB; nhóm chấp nhận ảnh tới khoảng 30 s cho MVP và ghi lại để tối ưu sau |
| LLM lỗi, throttle, hoặc bị prompt injection | Lỗi Bedrock trong log, Guardrails phát hiện tấn công | Model dự phòng, rồi chế độ chỉ luật; Guardrails `PROMPT_ATTACK`; output LLM chỉ đi qua template |
| Thay đổi prompt, KB hoặc luật làm giảm chất lượng | Eval CI báo đỏ | Eval gate chặn merge khi recall < 90% hoặc báo nhầm > 10% |
| Môi trường chung phụ thuộc một tài khoản cá nhân | Mất quyền hoặc quên dọn tài nguyên | Budgets có nhiều mức cảnh báo email; ghi lại danh sách tài nguyên; dọn theo thứ tự S3, ECR trước `sam delete` |
| Bộ eval nhỏ, chủ yếu tự soạn | Không có số liệu người dùng thật ngoài nhóm | Công bố hạn chế trong báo cáo; giữ cơ chế đóng góp mẫu có đồng ý và tự xoá sau 30 ngày |
