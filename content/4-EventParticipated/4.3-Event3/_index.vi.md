---
title: "AWS Student Community Day Vietnam 2026"
date: 2026-11-27
weight: 3
chapter: false
pre: " <b> 4.3. </b> "
---

# AWS Student Community Day Vietnam 2026

| Thông tin | Chi tiết |
|---|---|
| Ngày giờ | Thứ Bảy, 21/11/2026, 08:30–11:30 |
| Địa điểm | Văn phòng AWS, Tầng 26 tòa Bitexco, TP.HCM |
| Đơn vị tổ chức | 4 AWS Student Builder Group (HUFLIT, HCMUTE, PTIT HCM, SGU), trong khuôn khổ FCAJ |
| Vai trò của tôi | Người tham dự |

### Chương trình

- 08:30–09:10: đón khách và khai mạc.
- 09:10–09:20: "Câu chuyện đồng hành cùng những nhà phát triển trẻ".
- 09:20–09:40: "Bảo mật môi trường điện toán đám mây doanh nghiệp".
- 09:40–11:00: bốn nhóm Student Builder Group (HCMUTE, SGU, HUFLIT, PTIT) lần lượt chia sẻ, mỗi nhóm 20 phút.
- 11:00–11:30: mini game Quiz Quest, Word Quest và bế mạc.

### Điều tôi tiếp thu

Sự kiện rơi vào đúng tuần tôi đang tự dựng môi trường AWS chạy chung cho nhóm trên một account riêng, nên tôi nghe phần bảo mật rất kỹ.

- **Quyền tối thiểu cho từng thành phần.** Mỗi function nên có role chỉ đủ quyền cho việc của nó, và quyền nên nằm trong template hạ tầng chứ không cấp tay.
- **Secret không nằm trong code hay biến môi trường dạng rõ.**
- **Biết ai làm gì trên account.** Khi nhiều người cùng dùng một môi trường, cần dấu vết để lần lại thay đổi.
- Từ phần chia sẻ của các nhóm sinh viên: nhóm nào trình bày rõ thường cho xem số đo và lỗi đã gặp, không chỉ sơ đồ kiến trúc.

<!-- NHÁP: nội dung tiếp thu cần Minh xác nhận -->

### Liên hệ với phần việc của tôi

- Khi nối pipeline thật với S3, DynamoDB, Telegram và nạp model lúc Lambda khởi động ([PR #173](https://github.com/ask-first-fcaj/ask-first/pull/173)), tôi phải cấp cho function `ai` quyền đọc đúng prefix model trên S3 và quyền ghi bảng DynamoDB, không mở rộng hơn.
- Khi test từng thành phần trên môi trường tự dựng, tôi tìm ra và báo lại các lỗi tích hợp phía backend để nhóm sửa trước demo.
- Việc tôi muốn làm tiếp: rà lại các quyền còn cấp tay ngoài template để toàn bộ nằm trong IaC.

### Bài học

Vận hành một môi trường chung là việc của cả nhóm, nhưng người dựng phải giữ quyền và secret gọn gàng ngay từ đầu, sửa về sau tốn công hơn nhiều.

### Ảnh check-in

{{% notice info %}}
Ảnh check-in rõ mặt: Minh bổ sung.
{{% /notice %}}
