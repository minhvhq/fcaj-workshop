---
title: "Nhật ký tuần 11"
date: 2026-11-23
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

### Mục tiêu tuần 11

* Sửa lỗi OCR làm mất chữ tiếng Việt có dấu trong ảnh chụp màn hình (AI-26), đo trên Lambda thật.
* Đưa các PR tuần trước (#173, #174, #175) vào `main`, kiểm lại trên Lambda.
* Test cả chuỗi `webhook` → `worker` → `ai` → Telegram với bản sửa của BE (#170); chốt account của tôi làm môi trường chạy chung của nhóm.
* Viết blog kỹ thuật về OCR tiếng Việt trên Lambda và dựng site báo cáo thực tập.

### Công việc trong tuần

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Sửa `repair_urls`: nối tên miền bị OCR ngắt dòng ngay sau dấu `-` (#176) <br> - Thêm VietOCR `vgg_seq2seq` (ONNX) đọc lại các dòng có dấu, ghép kết quả theo từ; đo trên 66 ảnh và trên Lambda 3008 MB (#178) <br> - #173, #174, #175, #176, #178 được merge; deploy lại Lambda `ai`, kiểm URL intel và link rút gọn trên Lambda <br> - Review phần gom tin và webhook của BE1 trong #170 | 23/11/2026 | 23/11/2026 | [#176](https://github.com/ask-first-fcaj/ask-first/pull/176), [#178](https://github.com/ask-first-fcaj/ask-first/pull/178), [ocr-vietocr.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/ocr-vietocr.md), [Comment #170](https://github.com/ask-first-fcaj/ask-first/pull/170#issuecomment-7091667854) |
| 3 | - Deploy nhánh #170 lên stack `askfirst-dev` trên account của tôi, test cả chuỗi từ `webhook` và test lại DLQ; cả #42 và #43 đều đạt <br> - Nhóm chốt account của tôi làm môi trường AWS chạy chung <br> - #172 (độ dễ đọc) được merge <br> - Review PR của AI1: test rules engine đỡ chập chờn (#177), eval cuối W6 (#179), cache Maven (#180), image Lambda `ai` trong SAM (#181), gọi Bedrock khác account (#182), đo độ trễ trên account team (#183) | 24/11/2026 | 24/11/2026 | [Comment #170](https://github.com/ask-first-fcaj/ask-first/pull/170#issuecomment-7126853881), [Comment #39](https://github.com/ask-first-fcaj/ask-first/issues/39#issuecomment-7126855221), [Comment #85](https://github.com/ask-first-fcaj/ask-first/issues/85#issuecomment-7126854553), [#172](https://github.com/ask-first-fcaj/ask-first/pull/172) |
| 4 | - PR câu chữ tin bot (SH-08, #157) được merge <br> - Review PR đo p95 ảnh đầu-cuối của AI1 (#184) | 25/11/2026 | 25/11/2026 | [#157](https://github.com/ask-first-fcaj/ask-first/pull/157), [#184](https://github.com/ask-first-fcaj/ask-first/pull/184), [lambda-p95.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/lambda-p95.md) |
| 5 | - Viết blog "OCR tiếng Việt trên AWS Lambda" kèm sơ đồ kiến trúc, gửi lên group AWS Study Group (chưa được duyệt đăng) | 26/11/2026 | 26/11/2026 | Mục [Blog](../../3-blogsposted/) |
| 6 | - Dựng site báo cáo thực tập bằng Hugo theo template FCAJ, song ngữ Việt/Anh <br> - Review 2 PR sửa nút trên bot của BE (#194, #195) | 27/11/2026 | 27/11/2026 | [Site báo cáo](https://minhvhq.github.io/fcaj-workshop/), [#194](https://github.com/ask-first-fcaj/ask-first/pull/194), [#195](https://github.com/ask-first-fcaj/ask-first/pull/195) |

### Kết quả đạt được

* VietOCR ([#178](https://github.com/ask-first-fcaj/ask-first/pull/178), [ocr-vietocr.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/ocr-vietocr.md)): URL và SĐT giữ nguyên; từ có dấu đọc đúng từ 37% lên 89% trên 50 ảnh và từ 27% lên 97% trên 16 ảnh độc lập; tín hiệu luật bị mất giảm từ 15/33 xuống 6/33.
* Cái giá trên Lambda 3008 MB: mỗi ảnh chậm thêm p50 0,56 s, p95 3,42 s. AI1 quyết định bật VietOCR ngày 24/11 ([ocr-vietocr.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/ocr-vietocr.md)).
* Sửa URL xuống dòng ([#176](https://github.com/ask-first-fcaj/ask-first/pull/176)): `oi-004` (`dichvucong-vn.com`) và `oi-047` (`vietcombank.vn-yys.club`) chuyển từ UNSURE sang DANGEROUS.
* Cả chuỗi từ `webhook` chạy được với bản #170 ([comment](https://github.com/ask-first-fcaj/ask-first/pull/170#issuecomment-7126853881)): 2 tin gửi cách 0,5 s được gộp thành 1 kết quả; ảnh ra kết quả sau 11,7 s (warm); DLQ không còn báo "bận" 2 lần và không sửa nhầm tin "⏳" của job khác.
* 7 PR của tôi được merge trong tuần: #173, #174, #175, #176, #178 (23/11), #172 (24/11), #157 (25/11). Tôi review 10 PR của AI1 và BE (#170, #177, #179–#184, #194, #195).

### Bài học rút ra

* Lỗi mất dấu nằm ở bộ chữ của model nhận dạng, không sửa được bằng hậu xử lý; phải thêm một model đọc lại và chấp nhận đánh đổi độ trễ.
* Đo trên Lambda cho thấy riêng RapidOCR đã có p95 5,07 s, vượt mục tiêu 3 s của ADR-003; số đo thay thế trên GitHub Actions trước đó đã che mất điều này ([ocr-vietocr.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/ocr-vietocr.md)).
* Có một môi trường AWS chạy được thật giúp cả BE và AI kiểm bản sửa trước khi merge, thay vì đoán qua CI.
