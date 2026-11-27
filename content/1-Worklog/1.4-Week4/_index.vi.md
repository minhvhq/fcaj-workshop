---
title: "Nhật ký tuần 4"
date: 2026-10-05
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

<!-- NHÁP: cả tuần 4 chưa có bằng chứng (trước khi có repo), Minh kiểm lại trước khi nộp -->

### Mục tiêu tuần 4

* Vào nhóm dự án, cùng chốt đề tài Ask First và nhận vai trò AI2.
* Trả lời câu hỏi của nhóm: đọc chữ tiếng Việt trong ảnh chụp màn hình bằng dịch vụ AWS hay bằng OCR mã nguồn mở chạy trong Lambda.
* Tìm hiểu trước embedding đa ngôn ngữ cho phần tra cứu cảnh báo.

### Công việc trong tuần

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Họp lập nhóm 4 người (BE1, BE2, AI1, AI2), tôi nhận AI2 <br> - Góp ý tưởng cho đề tài: bot để người lớn tuổi gửi tin nghi lừa đảo và hỏi trước khi làm theo | 05/10/2026 | 05/10/2026 | [research-concept.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/research-concept.md) |
| 3 | - Đọc ngôn ngữ mà Amazon Textract và Rekognition `DetectText` hỗ trợ; ghi lại là cả hai chưa đọc tốt chữ tiếng Việt có dấu | 06/10/2026 | 06/10/2026 | [Amazon Textract](https://docs.aws.amazon.com/textract/latest/dg/what-is.html), [Rekognition text](https://docs.aws.amazon.com/rekognition/latest/dg/text-detection.html) |
| 4 | - Chạy thử Tesseract (`vie`), PaddleOCR, RapidOCR trên 5 ảnh chụp tin nhắn tự tạo, ghi nhận xét về URL và chữ có dấu | 07/10/2026 | 07/10/2026 | [RapidOCR](https://github.com/RapidAI/RapidOCR), [PaddleOCR](https://github.com/PaddlePaddle/PaddleOCR), [Tesseract](https://github.com/tesseract-ocr/tesseract) |
| 5 | - Đọc về `intfloat/multilingual-e5-small` và ONNX Runtime để chạy embedding trên CPU | 08/10/2026 | 08/10/2026 | [multilingual-e5-small](https://huggingface.co/intfloat/multilingual-e5-small), [ONNX Runtime](https://onnxruntime.ai/docs/) |
| 6 | - Cùng nhóm phân vai AI1/AI2: tôi nhận đầu vào (ảnh, OCR, che PII, URL) và dữ liệu đo; AI1 nhận judge Bedrock và eval | 09/10/2026 | 09/10/2026 | [implementation-plan.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/implementation-plan.md) |

### Kết quả đạt được

* Có đề tài và vai trò rõ ràng trong nhóm.
* Ghi chú so sánh nhanh OCR dịch vụ AWS và OCR mã nguồn mở, là đầu vào cho spike OCR ở tuần 6.

<!-- NHÁP: kết quả tuần 4 tự điền, Minh kiểm lại -->

### Bài học rút ra

* Trước khi chọn dịch vụ managed cần kiểm ngôn ngữ nó hỗ trợ, nhất là với tiếng Việt.
* Thử trên vài ảnh thật cho cảm nhận nhanh, nhưng muốn quyết định thì cần bộ ảnh có nhãn và cách chấm rõ ràng.
