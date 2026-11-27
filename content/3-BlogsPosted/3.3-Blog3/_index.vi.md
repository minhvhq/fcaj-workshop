---
title: "Blog 3"
date: 2026-11-27
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# Lambda và EventBridge: dùng Event-driven Architecture để tự cập nhật dữ liệu hằng ngày

**Tác giả:** Trí Đức Võ (thành viên nhóm) · Blog nhóm, dùng chung theo quy định của FCAJ

**Bài đăng:** [AWS Study Group VN](https://www.facebook.com/groups/660548818043427/?multi_permalinks=2345717436193215)

{{% notice info %}}
Bài đăng trong group AWS Study Group VN, cần là thành viên group mới xem được. Phần dưới đây là tóm tắt do tôi viết lại.
{{% /notice %}}

### Bài toán

Tác giả muốn có một trang web duy nhất tự hiển thị giá xăng, giá vàng, tỉ số esport và thời tiết, thay cho việc mở nhiều tab mỗi sáng. Yêu cầu đặt ra: không chạy máy chủ 24/7, mỗi nguồn dữ liệu có nhịp cập nhật riêng, một nguồn lỗi không làm hỏng cả trang, dễ thêm nguồn mới và chi phí cá nhân nhỏ.

### Kiến trúc

```text
EventBridge Scheduler (mỗi nguồn một lịch, payload {"source": ...})
  → Lambda Collector → S3 data/<nguồn>.json
  → S3 "Object Created" → EventBridge Rule (lọc prefix data/)
  → Lambda Renderer → S3 index.html → CloudFront (invalidation)
```

- **EventBridge Scheduler** là "đồng hồ" của hệ thống: mỗi nguồn có lịch riêng (thời tiết mỗi 3 giờ, tỉ số mỗi 30 phút, giá xăng và vàng theo ngày hoặc tuần), đặt được theo múi giờ Việt Nam, có retry và DLQ cho lần gọi thất bại.
- **Lambda Collector** dùng chung cho mọi nguồn: đọc tên nguồn từ payload, gọi API, chuẩn hoá thành JSON gọn rồi ghi vào S3. Nếu nguồn lỗi thì báo lỗi và không ghi đè file cũ, nên trang vẫn giữ dữ liệu lần trước.
- **EventBridge Rule** bắt sự kiện có file mới trong `data/` và gọi **Lambda Renderer** để gộp dữ liệu, dựng lại `index.html` và làm mới cache CloudFront.

### Hai điểm cần lưu ý được nêu trong bài

1. **Phải lọc prefix `data/`.** Renderer cũng ghi `index.html` vào cùng bucket; nếu rule bắt mọi file mới thì chính `index.html` sẽ kích hoạt renderer lần nữa, tạo vòng lặp vô tận.
2. **S3 gửi sự kiện theo kiểu at-least-once**, có thể bị trùng. Renderer được thiết kế idempotent: luôn dựng lại trang từ dữ liệu hiện có và ghi đè, nên chạy lại cũng không làm sai kết quả.

### Điều tôi rút ra

- Tách hệ thống theo sự kiện giúp mỗi phần chạy, lỗi và thay đổi độc lập; thêm nguồn mới chỉ cần thêm một lịch và một hàm chuẩn hoá.
- Khi một hàm vừa đọc vừa ghi trên cùng bucket, cần nghĩ ngay đến nguy cơ tự kích hoạt lại chính nó.
- Hiển thị thời điểm cập nhật (`updatedAt`) cho từng mục là cách đơn giản để người đọc biết dữ liệu nào đang cũ.
