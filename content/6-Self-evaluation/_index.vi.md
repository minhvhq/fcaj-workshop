---
title: "Tự đánh giá"
date: 2026-11-27
weight: 6
chapter: false
pre: " <b> 6. </b> "
---

Từ 14/09/2026 đến 14/12/2026, tôi thực tập tại Công ty TNHH Amazon Web Services Vietnam trong chương trình First Cloud AI Journey, giữ vai trò AI2 của dự án Ask First (Hỏi Trước), một bot Telegram giúp người cao tuổi kiểm tra tin nhắn nghi lừa đảo. Phần việc chính của tôi gồm OCR tiếng Việt, che thông tin cá nhân, tầng luật, RAG, bộ đo đánh giá, nối pipeline thật trên Lambda và dựng môi trường AWS chạy chung cho nhóm. Tôi mở 26 PR trên repo của nhóm, 24 PR được merge.

Bảng dưới là tự đánh giá theo 8 tiêu chí của FCAJ. Tôi cố ghi cả chỗ chưa tốt để lần sau làm khác.

| # | Tiêu chí | Mức | Nhận xét và dẫn chứng |
|---|---|---|---|
| 1 | Kiến thức chuyên môn | Khá | Dùng được Lambda container image, S3, DynamoDB, Bedrock, ONNX Runtime trong việc thật. Dẫn chứng: nạp model từ S3 có kiểm sha256 ở pha INIT ([PR #173](https://github.com/ask-first-fcaj/ask-first/pull/173)), VietOCR ONNX đọc lại dòng tiếng Việt ([PR #178](https://github.com/ask-first-fcaj/ask-first/pull/178)). Chưa vững phần IAM và IaC: một số quyền vẫn cấp tay ngoài template. |
| 2 | Tự học và nghiên cứu | Tốt | Tự tìm ra model PP-OCRv6 thiếu 88 ký tự tiếng Việt bằng cách đọc metadata của file ONNX, rồi tự xuất VietOCR sang ONNX để khỏi đưa PyTorch vào image. Tỉ lệ từ có dấu đọc đúng tăng từ 37% lên 89% (bộ 50 ảnh) và 27% lên 97% (bộ 16 ảnh). |
| 3 | Chủ động | Tốt | Tự dựng và vận hành môi trường AWS chạy chung của nhóm trên một account riêng từ 19/11, test từng thành phần và báo lỗi tích hợp phía backend (issue #42, #43, #85). Tự đề xuất đo trên Lambda thật bằng function tạm thay vì chỉ đo trên laptop. |
| 4 | Kỷ luật và tuân thủ | Khá | Mỗi PR gắn mã việc (AI-20, AI-22, INT-01...) và có số đo đi kèm. Điểm yếu: tuần đầu tôi chậm hoàn thành phần ôn tập và tài liệu so với kế hoạch; một số PR vượt mức khoảng 300 dòng mà nhóm thống nhất, khiến review lâu hơn (ví dụ [PR #173](https://github.com/ask-first-fcaj/ask-first/pull/173) mở 19/11, merge 23/11). |
| 5 | Giao tiếp | Khá | Viết mô tả PR có bối cảnh, cách đo và kết quả; viết blog OCR chia sẻ cho cộng đồng. Còn hay viết dài, chưa tóm tắt được trong vài câu khi báo cáo nhanh. |
| 6 | Làm việc nhóm | Khá | Review PR của các bạn, phối hợp với BE khi nối pipeline thật với S3, DynamoDB, Telegram. Còn phụ thuộc vào review của bạn cùng vai trò AI trước khi merge, có lúc chờ thay vì tự kiểm thêm. |
| 7 | Giải quyết vấn đề | Tốt | Thay vì đổi model OCR, tôi dùng hai model, mỗi model làm phần nó giỏi, ghép theo từng từ. Tín hiệu luật bị mất giảm từ 15/33 xuống 6/33, URL và số điện thoại giữ nguyên. Sửa lỗi tên miền bị xuống dòng sau dấu gạch ([PR #176](https://github.com/ask-first-fcaj/ask-first/pull/176)). |
| 8 | Đóng góp cho Workshop | Khá | Đóng góp phần nối pipeline thật, nạp model từ S3 và môi trường AWS để nhóm demo và viết workshop. Phần viết workshop chủ yếu do bạn khác đảm nhận. |


### Điều tôi muốn cải thiện

- Chia PR nhỏ hơn, mỗi PR một mục tiêu, để review nhanh hơn.
- Học kỹ IAM và đưa toàn bộ quyền vào IaC.
- Tập báo cáo ngắn: vấn đề, cách đo, kết quả, trong không quá năm câu.
- Tự kiểm kỹ hơn trước khi nhờ review, thay vì dựa vào review để bắt lỗi.
