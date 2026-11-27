---
title: "Nhật ký tuần 12"
date: 2026-11-30
weight: 12
chapter: false
pre: " <b> 1.12. </b> "
---

{{% notice info %}}
Tuần 12 là **kế hoạch**, viết trước ngày 30/11/2026. Các mục dưới đây chưa diễn ra và sẽ được cập nhật sau.
{{% /notice %}}

<!-- NHÁP: toàn bộ tuần 12 là kế hoạch, Minh cập nhật kết quả thật sau khi làm -->

### Mục tiêu tuần 12

* Nộp workshop và site báo cáo thực tập.
* Demo bot Ask First chạy trên môi trường AWS chung của nhóm.
* Dọn sạch tài nguyên trên account test của tôi để không phát sinh chi phí.

### Kế hoạch trong tuần

| Thứ | Công việc (dự kiến) | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Rà lại các trang workshop và nhật ký, thay ảnh còn thiếu, kiểm link PR <br> - Nộp workshop | 30/11/2026 | 30/11/2026 | Mục [Workshop](../../5-workshop/) |
| 3 | - Chạy thử kịch bản demo trên stack `askfirst-dev`: tin chữ có OTP, ảnh tin giả ngân hàng, link rút gọn | 01/12/2026 | 01/12/2026 | [implementation-plan.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/implementation-plan.md) |
| 4 | - Demo cùng nhóm; ghi lỗi gặp trong demo thành issue | 02/12/2026 | 02/12/2026 | [Issues](https://github.com/ask-first-fcaj/ask-first/issues) |
| 5 | - Dọn tài nguyên theo SH-10: gỡ webhook bot test, xoá Lambda `ai` và image ECR, làm rỗng rồi xoá bucket S3, xoá bảng DynamoDB, tham số SSM, log group, `sam delete` stack; xoá access key tạm | 03/12/2026 | 03/12/2026 | [SAM delete](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-cli-command-reference-sam-delete.html), [implementation-plan.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/implementation-plan.md) |
| 6 | - Kiểm Cost Explorer và danh sách tài nguyên sau khi dọn <br> - Hoàn thiện mục Tự đánh giá và Chia sẻ, góp ý | 04/12/2026 | 04/12/2026 | [Cost Explorer](https://docs.aws.amazon.com/cost-management/latest/userguide/ce-what-is.html) |

### Kết quả mong đợi

* Workshop và site báo cáo nộp đúng hạn.
* Demo chạy được trên môi trường chung, có danh sách lỗi còn lại.
* Account test không còn tài nguyên tính phí.

### Điều cần lưu ý

* Phải làm rỗng bucket S3 và xoá image ECR trước khi chạy `sam delete`, nếu không stack sẽ xoá lỗi.
* Chỉ dọn sau khi nhóm demo xong, vì account này đang là môi trường chạy chung.
