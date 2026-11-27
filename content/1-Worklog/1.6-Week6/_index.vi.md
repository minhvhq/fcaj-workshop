---
title: "Nhật ký tuần 6"
date: 2026-10-19
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

Tuần đầu tiên của plan 6 tuần (19/10–29/11/2026). Từ tuần này mọi việc đều có PR hoặc commit trong repo [ask-first-fcaj/ask-first](https://github.com/ask-first-fcaj/ask-first).

### Mục tiêu tuần 6

* Viết hướng dẫn gán nhãn để cả nhóm gán bộ eval theo cùng một cách (SH-02).
* Đưa bộ ảnh OCR lên repo và trả lời câu hỏi của Gate G2: OCR nào đọc đúng URL trong ảnh chụp màn hình (AI-20).
* Có một tập ảnh độc lập để kiểm số đo của spike có bị "đẹp" quá không.

### Công việc trong tuần

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Đọc plan 6 tuần và phần việc AI2 trong `implementation-plan.md` <!-- NHÁP: không có commit ngày 19/10, Minh kiểm lại --> | 19/10/2026 | 19/10/2026 | [implementation-plan.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/implementation-plan.md) |
| 3 | - Viết `eval/LABELING.md`: cách gán nhãn kịch bản N0–N6 và nhóm `OTHER`, kèm 10 mẫu gán thử (SH-02) <br> - Review PR hợp đồng BE ↔ AI v1.0 (#89) và hướng dẫn 3 việc nghiên cứu phụ cho AI2 (#93) | 20/10/2026 | 21/10/2026 | [PR #94](https://github.com/ask-first-fcaj/ask-first/pull/94), [#89](https://github.com/ask-first-fcaj/ask-first/pull/89), [#93](https://github.com/ask-first-fcaj/ask-first/pull/93) |
| 4 | - Đưa 25 ảnh OCR `001–025` kèm nhãn gõ tay lên repo (#96, nội dung vào `main` qua #97) <br> - Viết spike OCR: so Tesseract, PaddleOCR (+VietOCR) và RapidOCR trên 50 ảnh, chấm URL, SĐT và CER (#97) <br> - Review bộ ảnh `026–050` của AI1 (#95) | 21/10/2026 | 22/10/2026 | [PR #96](https://github.com/ask-first-fcaj/ask-first/pull/96), [PR #97](https://github.com/ask-first-fcaj/ask-first/pull/97), [#95](https://github.com/ask-first-fcaj/ask-first/pull/95) |
| 5 | - Review ADR-003 của AI1 (chọn RapidOCR có điều kiện), đối chiếu với số đo của spike | 22/10/2026 | 22/10/2026 | [#98](https://github.com/ask-first-fcaj/ask-first/pull/98), [ADR-003](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/decisions/ADR-003-ocr-rapidocr.md) |
| 6 | - Thêm tập test OCR độc lập 16 ảnh `oi-test-001–016`, chưa dùng khi viết luật sắp cột và regex (#99) <br> - Mở rộng bộ eval v0 lên 78 mẫu chữ (SH-03, commit `1d90b2f`) <br> - Review validator + bộ eval 100 mẫu (#100) và bản cập nhật ADR-003 (#101) | 23/10/2026 | 23/10/2026 | [PR #99](https://github.com/ask-first-fcaj/ask-first/pull/99), [commit 1d90b2f](https://github.com/ask-first-fcaj/ask-first/commit/1d90b2f), [#100](https://github.com/ask-first-fcaj/ask-first/pull/100), [#101](https://github.com/ask-first-fcaj/ask-first/pull/101) |

### Kết quả đạt được

* 4 PR của tôi được mở trong tuần; #94, #97, #99 đã merge, #96 đóng vì nội dung đã đi vào #97.
* Spike OCR trên 50 ảnh ([#97](https://github.com/ask-first-fcaj/ask-first/pull/97), [ocr-spike.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/ocr-spike.md)):

| Cấu hình | URL đúng (nghiêm ngặt) | URL đúng sau regex | CER |
| --- | --- | --- | --- |
| RapidOCR + sắp dòng theo cột | 27/32 (84%) | 31/32 | 12,9% |
| RapidOCR thường | 25/32 | | 26,8% |
| PaddleOCR + sắp cột | 28/32 | 31/32 | nặng gần gấp 5 (~520 MB so với ~105 MB) |
| Tesseract `vie` | 10/32 | | |
| Paddle + VietOCR | 9/32 | | |

* Kết luận được AI1 ghi vào ADR-003: dùng RapidOCR + sắp cột; chưa engine nào đạt ngưỡng G2 ở dạng nghiêm ngặt.
* Tôi review 6 PR của AI1 trong tuần: [#89](https://github.com/ask-first-fcaj/ask-first/pull/89), [#93](https://github.com/ask-first-fcaj/ask-first/pull/93), [#95](https://github.com/ask-first-fcaj/ask-first/pull/95), [#98](https://github.com/ask-first-fcaj/ask-first/pull/98), [#100](https://github.com/ask-first-fcaj/ask-first/pull/100), [#101](https://github.com/ask-first-fcaj/ask-first/pull/101).

### Bài học rút ra

* Hai quy tắc sửa đầu URL được viết sau khi xem lỗi trên chính 50 ảnh, nên con số 31/32 là lạc quan. Tập 16 ảnh độc lập ([#99](https://github.com/ask-first-fcaj/ask-first/pull/99)) là cách tôi tự kiểm lại điều đó.
* Engine "tốt nhất" về độ chính xác (Paddle) chưa chắc hợp với Lambda: dung lượng và RAM quan trọng không kém.
