---
title: "Nhật ký tuần 8"
date: 2026-11-02
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Mục tiêu tuần 8

* Xử lý phần review còn lại của mask PII (AI-22), nhất là lỗi chậm O(n²) khi tin có nhiều số lặp.
* Hoàn thành ba khối đầu vào còn thiếu: chuẩn hoá văn bản + rules engine (AI-23), URL intel không gọi mạng (AI-24a), tải ảnh/voice từ Telegram và tải artifact model từ S3 (AI-03b).

### Công việc trong tuần

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Sửa mask: che mọi lần lặp của OTP, bỏ độ phức tạp O(n²) bằng `endpos` và `bisect`, `code` nằm trong URL không còn bị coi là từ khoá OTP; mở PR #118 | 02/11/2026 | 03/11/2026 | [PR #118](https://github.com/ask-first-fcaj/ask-first/pull/118) |
| 3 | - Viết `media/telegram.py` (getFile, tải song song ≤ 4 file, ≤ 10 s/file, chặn > 20 MB, token đọc từ SSM) và `S3ArtifactStore` kiểm sha256 (#121) <br> - Sửa theo review: tổng thời gian tải theo `deadline_s`, `ArtifactError`, dọn file `.part` (#125) <br> - Chuẩn hoá văn bản + 14 luật trên bản đã mask (#128) <br> - URL intel: allowlist, typosquat, punycode, `text_link` lệch link thật, theo Public Suffix List (#129; #130 là bản trùng, đã đóng) <br> - Review PR của AI1 cùng ngày: fuse (#119), respond (#120), cascade judge (#122, #123), `RealPipeline` (#124), OCR local (#126), mở link rút gọn chống SSRF (#127) | 03/11/2026 | 03/11/2026 | [#121](https://github.com/ask-first-fcaj/ask-first/pull/121), [#125](https://github.com/ask-first-fcaj/ask-first/pull/125), [#128](https://github.com/ask-first-fcaj/ask-first/pull/128), [#129](https://github.com/ask-first-fcaj/ask-first/pull/129) |
| 4 | - Kéo `main` sau đợt merge 03/11, chạy lại toàn bộ test `ai/` trên máy | 04/11/2026 | 04/11/2026 | [CHANGELOG.md](https://github.com/ask-first-fcaj/ask-first/blob/main/CHANGELOG.md) |
| 5 | - Đọc lại các luật `R_*` trong `kb/rules.yaml` và ghi các tin còn bắt sót để đưa vào bộ đo sau | 05/11/2026 | 05/11/2026 | [kb/rules.yaml](https://github.com/ask-first-fcaj/ask-first/blob/main/kb/rules.yaml) |
| 6 | - Đọc về `multilingual-e5-small` bản ONNX int8 để chuẩn bị RAG (AI-29) | 06/11/2026 | 06/11/2026 | [multilingual-e5-small](https://huggingface.co/intfloat/multilingual-e5-small) |

### Kết quả đạt được

* Mask hết chậm với tin dài ([#118](https://github.com/ask-first-fcaj/ask-first/pull/118)): chuỗi `"otp 1234 "` lặp 5.000 lần (45.000 ký tự) từ 39,2 s xuống 0,38 s; `"OTP 482913 "` từ 34,2 s xuống 0,28 s.
* Cả 5 PR của tôi trong ngày 03/11 đều merge cùng ngày. Số test `ai/` tăng lên 433 pass sau #125 và 495 pass sau #128 ([#125](https://github.com/ask-first-fcaj/ask-first/pull/125), [#128](https://github.com/ask-first-fcaj/ask-first/pull/128)).
* Rules engine chạy trên 100 mẫu eval chỉ bằng luật: 0 mẫu đổi tín hiệu so với trước khi sửa review, không mẫu benign nào đi fast path ([#128](https://github.com/ask-first-fcaj/ask-first/pull/128)).
* Review 8 PR của AI1 ngày 03/11 (#119, #120, #122–#127).

### Bài học rút ra

* Lỗi O(n²) không lộ ra trên test thường, chỉ lộ khi thử chuỗi dài cố ý; từ đó tôi thêm ca "tin rất dài" vào test hiệu năng.
* URL intel chỉ nên gắn cờ typosquat khi có dấu hiệu giả rõ ràng; tên miền chỉ "mang tên thương hiệu" thì để mức nhẹ (`U_BRAND_UNVERIFIED`) để tránh báo nhầm.
