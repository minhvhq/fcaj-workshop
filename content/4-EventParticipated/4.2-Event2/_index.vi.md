---
title: "AWS Cloud & AI Day Vietnam 2026 (Watch Party)"
date: 2026-11-27
weight: 2
chapter: false
pre: " <b> 4.2. </b> "
---

# AWS Cloud & AI Day Vietnam 2026: Watch Party TP.HCM

| Thông tin | Chi tiết |
|---|---|
| Ngày | Thứ Ba, 29/09/2026 |
| Địa điểm | Văn phòng AWS, Tầng 26 tòa Bitexco, TP.HCM (xem phát trực tiếp từ sân khấu chính ở Hà Nội) |
| Chủ đề chung | Agentic AI |
| Diễn giả | Lãnh đạo AWS và các khách hàng của AWS (keynote phát trực tiếp) |
| Vai trò của tôi | Người tham dự |

### Chương trình

- Keynote phát trực tiếp: khách hàng kể cách dùng Agentic AI để hiện đại hoá và mở rộng hệ thống.
- Các phiên kỹ thuật chọn lọc thuộc track "Builders x AI".
- Giao lưu tại chỗ với kỹ sư và người làm công nghệ ở TP.HCM.

### Điều tôi tiếp thu

Tôi xem sự kiện này với một câu hỏi trong đầu: khi nào nên để AI tự quyết, khi nào nên để code quyết. Những gì tôi ghi lại:

- **Truy xuất trước, sinh sau.** Nhiều câu chuyện khách hàng đều đưa dữ liệu nội bộ vào trước khi gọi model, thay vì để model tự nhớ. Đó chính là RAG.
- **Mỗi bước phải đo được.** Khi hệ thống có nhiều bước, người ta cần biết bước nào làm sai, chứ không chỉ biết kết quả cuối sai.
- **Chi phí đi theo từng lần gọi.** Một tính năng AI rẻ khi thử nhưng có thể đắt khi chạy thật nếu không tính trước số lần gọi model.

<!-- NHÁP: nội dung tiếp thu cần Minh xác nhận -->

### Liên hệ với phần việc của tôi

- **RAG.** Sáu tuần sau, tôi làm phần RAG chạy local cho Ask First: kho 103 cảnh báo lừa đảo, embedder e5-small dạng int8 và bộ truy xuất ([PR #147](https://github.com/ask-first-fcaj/ask-first/pull/147)), rồi nối vào pipeline thật, trích nguồn theo kịch bản ([PR #151](https://github.com/ask-first-fcaj/ask-first/pull/151)). Chạy embedder ngay trong Lambda giúp nhóm không phải trả thêm một dịch vụ vector riêng.
- **Đo từng bước.** Ý "mỗi bước phải đo được" là lý do tôi tách các phép đo riêng: mask PII trên 500 mẫu ([PR #158](https://github.com/ask-first-fcaj/ask-first/pull/158)), luật OTP ([PR #159](https://github.com/ask-first-fcaj/ask-first/pull/159)) và eval gate chạy trên mỗi PR ([PR #153](https://github.com/ask-first-fcaj/ask-first/pull/153)).
- **Chi phí.** Khi viết blog OCR, tôi quy độ trễ thêm của VietOCR ra GB-giây và USD trên 1.000 ảnh, thay vì chỉ nói "chậm hơn một chút".

### Bài học

Một hệ thống AI đáng tin không đến từ model lớn nhất, mà từ việc mỗi bước có số đo và có giới hạn rõ ràng.

### Ảnh check-in

{{% notice info %}}
Ảnh check-in rõ mặt: Minh bổ sung.
{{% /notice %}}
