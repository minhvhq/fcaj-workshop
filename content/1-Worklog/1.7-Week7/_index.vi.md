---
title: "Nhật ký tuần 7"
date: 2026-10-26
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Mục tiêu tuần 7

* Che thông tin cá nhân trong tin trước khi tin đi vào luật và prompt của LLM (AI-22).
* Dựng kho tri thức (KB) v1 mà mục nào cũng có nguồn và ngày xác minh, kèm script kiểm tự động (AI-25, SH-05).

### Công việc trong tuần

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Đọc ARCH §9.2 (che PII) và liệt kê các token cần sinh: `[SĐT_n]`, `[STK_n • ngân hàng]`, `[CCCD_n]`, `[OTP_n]`, `[EMAIL_n]` <!-- NHÁP: không có commit 26/10, Minh kiểm lại --> | 26/10/2026 | 26/10/2026 | [system-architecture.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/system-architecture.md) |
| 3 | - Gom hotline ngân hàng, nhà mạng, ví điện tử từ trang chính thức cho KB <!-- NHÁP: không có commit 27/10, Minh kiểm lại --> | 27/10/2026 | 28/10/2026 | [kb/hotlines.yaml](https://github.com/ask-first-fcaj/ask-first/blob/main/kb/hotlines.yaml) |
| 4 | - Viết `mask(text)` trả `MaskResult(text, entities)`, giá trị gốc chỉ nằm trong RAM <!-- NHÁP: ngày bắt đầu tự điền, commit đầu tiên là 30/10 --> | 28/10/2026 | 30/10/2026 | [PR #110](https://github.com/ask-first-fcaj/ask-first/pull/110) |
| 5 | - Review Bedrock Converse client và khung spike Qwen3 của AI1 (#103) | 29/10/2026 | 29/10/2026 | [#103](https://github.com/ask-first-fcaj/ask-first/pull/103) |
| 6 | - Mở và sửa theo review PR mask PII (#110): vá lọt SĐT dạng 4-2-4, SĐT quốc tế, STK có tên chủ tài khoản, OTP lặp trong URL; merge cùng ngày <br> - Mở PR KB v1 + validator (#111), merge 31/10 <br> - Review loạt PR của AI1 mở cùng ngày: chấm URL OCR theo ranh giới (#105), LABELING bản 2 (#106), gán lại eval v0 (#107, #108), nhãn 16 ảnh (#109), Gate G2 (#112), harness eval (#113), privacy v0.1 (#114), judge prompt v1 (#115), handler Lambda `ai` + DynamoKV (#116) | 30/10/2026 | 31/10/2026 | [PR #110](https://github.com/ask-first-fcaj/ask-first/pull/110), [PR #111](https://github.com/ask-first-fcaj/ask-first/pull/111), [commit 1f41843](https://github.com/ask-first-fcaj/ask-first/commit/1f41843) |
| 7 | - Review PR bổ sung thẻ N2.2, N5.2 vào KB của AI1 (#117) | 31/10/2026 | 31/10/2026 | [#117](https://github.com/ask-first-fcaj/ask-first/pull/117) |

### Kết quả đạt được

* Mask PII đã merge ([#110](https://github.com/ask-first-fcaj/ask-first/pull/110)): 90 case trong `test_mask.py`, toàn bộ test của `ai/` lúc đó là 132 pass.
* KB v1 đã merge ([#111](https://github.com/ask-first-fcaj/ask-first/pull/111)): 19 thẻ kịch bản, 39 tên miền chính thức, 35 thương hiệu, 20 hotline, 1 checklist 5 bước; mục nào cũng có `source_url` và `verified_at`; `example_phrases` lấy nguyên văn từ nguồn, không để LLM tự nghĩ.
* Review 14 PR của AI1 trong tuần (#103, #105–#109, #112–#117).

### Bài học rút ra

* Một hàm che PII cần cả test dương lẫn mẫu bẫy (số tiền, mã đơn hàng) thì mới biết nó che nhầm hay không.
* Gắn ngày xác minh cho từng hotline tạo ra hạn phải kiểm lại; tôi dùng chính hạn này ở tuần 10 ([#168](https://github.com/ask-first-fcaj/ask-first/pull/168)).
