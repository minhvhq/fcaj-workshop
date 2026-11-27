---
title: "Blog 2"
date: 2026-11-27
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# Test luồng Serverless và Event-Driven (SQS + DLQ + DynamoDB) trên máy với LocalStack và Java 21

**Tác giả:** Ngô Thị Kim Ngân (thành viên nhóm) · Blog nhóm, dùng chung theo quy định của FCAJ

**Bài đăng:** [AWS Study Group VN](https://www.facebook.com/groups/awsstudygroupfcj/posts/2349976352433990/)

{{% notice info %}}
Bài đăng trong group AWS Study Group VN, cần là thành viên group mới xem được. Phần dưới đây là tóm tắt do tôi viết lại.
{{% /notice %}}

### Bối cảnh

Luồng xử lý bất đồng bộ trong bài gồm: sự kiện vào Amazon SQS, AWS Lambda (Java 21) xử lý rồi ghi vào Amazon DynamoDB và đọc cấu hình từ SSM Parameter Store, kèm một hàng đợi DLQ cho bản tin lỗi. Nếu mỗi lần sửa một lỗi nhỏ đều phải deploy lên AWS để thử thì tốn thời gian và có thể phát sinh chi phí ngoài ý muốn.

### Cách làm "Local-First"

- **Giả lập dịch vụ trên máy:** dùng LocalStack để chạy SQS, DLQ, DynamoDB và SSM ngay trên máy cá nhân.
- **Tách endpoint rõ ràng:** trong AWS SDK for Java v2, chỉ gọi `endpointOverride(URI.create(localEndpoint))` khi chạy ở chế độ local (`http://localhost:4566`); trên cloud thì dùng endpoint mặc định, nên cùng một code chạy được ở cả hai nơi.
- **Test cả luồng đúng lẫn luồng lỗi:** gửi payload hợp lệ để kiểm tra dữ liệu được ghi vào DynamoDB và message bị xoá khỏi hàng đợi chính; gửi payload lỗi để kiểm tra message rơi vào DLQ sau đúng số lần thử lại đã cấu hình.
- **Lên cloud sau cùng:** chỉ khi logic và cấu hình đã ổn ở local mới build và deploy bằng AWS SAM.

### Điều tôi rút ra

- Luồng lỗi (retry, DLQ) cần được test chủ động như luồng chính, vì đây là phần khó quan sát nhất khi đã chạy trên AWS.
- Gom việc chọn endpoint vào một chỗ giúp code không bị rẽ nhánh lung tung giữa môi trường local và cloud.
- Dự án Ask First của nhóm cũng dùng SQS kèm DLQ cho phần xử lý nền, nên cách kiểm tra luồng DLQ trong bài áp dụng trực tiếp được.
