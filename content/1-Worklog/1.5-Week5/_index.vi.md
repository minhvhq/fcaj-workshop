---
title: "Nhật ký tuần 5"
date: 2026-10-12
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

<!-- NHÁP: cả tuần 5 chưa có bằng chứng (trước khi có repo), Minh kiểm lại trước khi nộp -->

### Mục tiêu tuần 5

* Gom bộ ảnh chụp màn hình tin lừa đảo đủ để đo OCR khi repo mở.
* Thống nhất cách gán nhãn ảnh: chữ đáp án, URL, số điện thoại.
* Tìm hiểu các loại thông tin cá nhân (PII) cần che trước khi gửi tin cho mô hình.

### Công việc trong tuần

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Gom ảnh tin lừa từ các bài cảnh báo công khai và tự dựng thêm ảnh giả lập; xoá hoặc thay số thật trong ảnh | 12/10/2026 | 12/10/2026 | [Cục An toàn thông tin](https://ais.gov.vn/) |
| 3 | - Viết chữ đáp án cho từng ảnh, tách riêng danh sách URL và số điện thoại để chấm sau | 13/10/2026 | 14/10/2026 | [CER (character error rate)](https://en.wikipedia.org/wiki/Word_error_rate) |
| 4 | - Đọc bản nháp research concept và kiến trúc của nhóm, góp ý phần đầu vào ảnh và phần che PII | 14/10/2026 | 14/10/2026 | [system-architecture.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/system-architecture.md) |
| 5 | - Liệt kê PII hay gặp trong tin lừa tiếng Việt: OTP, số tài khoản, số điện thoại, CCCD; viết thử regex cho từng loại | 15/10/2026 | 15/10/2026 | [Python `re`](https://docs.python.org/3/library/re.html) |
| 6 | - Đọc AWS SAM và GitHub Actions ở mức cơ bản để chuẩn bị làm việc trên repo chung | 16/10/2026 | 16/10/2026 | [AWS SAM](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html), [GitHub Actions](https://docs.github.com/en/actions) |

### Kết quả đạt được

* Bộ ảnh nháp và chữ đáp án sẵn sàng; 25 ảnh đầu tiên được đưa lên repo ở tuần 6 ([PR #96](https://github.com/ask-first-fcaj/ask-first/pull/96)).
* Danh sách các kiểu PII và regex thử, là điểm bắt đầu cho mask PII ở tuần 7.

<!-- NHÁP: kết quả tuần 5 tự điền (trừ link PR #96), Minh kiểm lại -->

### Bài học rút ra

* Ảnh lấy từ nguồn công khai vẫn có thể chứa số thật, nên phải kiểm từng ảnh trước khi đưa vào repo.
* Gán nhãn URL và số điện thoại riêng với chữ thường giúp đo được đúng thứ mà luật cần.
