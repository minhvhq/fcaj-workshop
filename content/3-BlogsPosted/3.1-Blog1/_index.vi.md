---
title: "Blog 1"
date: 2026-11-27
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Thiết kế luồng dữ liệu IoT (MQTT → AWS IoT Core → Glue → S3) tối ưu chi phí cho đồ án quy mô nhỏ

**Tác giả:** Ngô Thị Kim Ngân (thành viên nhóm) · Blog nhóm, dùng chung theo quy định của FCAJ

**Bài đăng:** [AWS Study Group VN](https://www.facebook.com/groups/awsstudygroupfcj/permalink/2351133745651584/)

{{% notice info %}}
Bài đăng trong group AWS Study Group VN, cần là thành viên group mới xem được. Phần dưới đây là tóm tắt do tôi viết lại.
{{% /notice %}}

### Vấn đề bài viết đặt ra

Một hệ thống cảm biến gửi bản tin JSON nhỏ qua MQTT mỗi vài giây sẽ sinh ra hàng triệu file trên Amazon S3 sau vài tháng. Theo tính toán trong bài, 10 thiết bị gửi mỗi 10 giây trong 3 tháng tạo ra khoảng 7,7 triệu file. Khi đó phí request PUT/GET có thể vượt phí lưu trữ, và Amazon Athena phải mở từng file JSON nên truy vấn vừa chậm vừa tốn tiền quét dữ liệu.

### Kiến trúc đề xuất

Luồng dữ liệu chia làm hai giai đoạn, không cần máy chủ:

1. **Thu thập thô:** AWS IoT Core Rule Engine lọc bản tin bằng SQL và ghi thẳng xuống bucket S3 thô, phân vùng theo `year=/month=/day=/hour=`. Không cần EC2 hay Lambda trung gian chỉ để nhận MQTT.
2. **Nén và tối ưu:** mỗi ngày, Amazon EventBridge Scheduler kích hoạt một AWS Glue Job đọc dữ liệu JSON của ngày trước, làm sạch kiểu dữ liệu, gộp file và ghi ra Apache Parquet (nén Snappy) vào bucket đã xử lý.
3. **Dọn dẹp:** S3 Lifecycle tự xoá file JSON thô sau 7 ngày, vừa giữ bản sao để gỡ lỗi vừa không phải trả phí lưu trữ lâu dài.

### Kết quả nêu trong bài

Với kịch bản 10 triệu bản tin mỗi tháng (khoảng 2 GB JSON thô):

| Chỉ số | Trước | Sau |
|---|---|---|
| Dung lượng trên S3 | ~2,0 GB | ~280 MB |
| Số file trên S3 | ~10.000.000 | ~30 (1 file/ngày) |
| Dữ liệu Athena quét khi truy vấn 1 cột | ~2,0 GB | ~40 MB |

Bài cũng gợi ý cách giữ chi phí Glue thấp: với Spark thì chọn worker G.1X và tối đa 2 worker; với dữ liệu dưới vài trăm MB mỗi ngày thì dùng Glue Python Shell (0,0625 DPU) kết hợp thư viện `awswrangler`.

### Điều tôi rút ra

- Chi phí trên S3 không chỉ nằm ở dung lượng; số lượng request và số lượng object cũng là một khoản cần tính khi thiết kế.
- Định dạng cột như Parquet giảm mạnh lượng dữ liệu Athena phải quét, nên ảnh hưởng trực tiếp đến tiền truy vấn.
- Dùng tính năng có sẵn của dịch vụ (IoT Rule, S3 Lifecycle) thay cho code tự viết giúp ít thành phần phải vận hành hơn.
