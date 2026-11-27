---
title: "Nhật ký tuần 10"
date: 2026-11-16
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

### Mục tiêu tuần 10

* Đo bằng số ba thứ trước đó chỉ kiểm bằng test đơn vị: mask PII (AI-31), luật OTP (AI-30), độ dễ đọc của lời giải thích (AI-32).
* Xác minh lại 20 hotline trước khi `verified_at` hết hạn 30 ngày (SH-05).
* Không chờ hạ tầng của BE: tự dựng một account AWS riêng để chạy Lambda `ai` thật với S3, DynamoDB, SSM, Telegram, và nối `PIPELINE_IMPL=real` (INT-01, AI1 giao cho tôi ngày 19/11).

### Công việc trong tuần

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Mở PR câu chữ tin bot: xưng "cháu", nút gọi người nhà, checklist dễ đọc (SH-08, #157) <br> - Mở PR bộ đo mask 500 mẫu (AI-31, #158) và bộ đo luật OTP trên tin OTP thật và tin lừa (AI-30, #159) | 16/11/2026 | 17/11/2026 | [#157](https://github.com/ask-first-fcaj/ask-first/pull/157), [#158](https://github.com/ask-first-fcaj/ask-first/pull/158), [#159](https://github.com/ask-first-fcaj/ask-first/pull/159) |
| 3 | - Sửa mask: bỏ từ khoá "mấy số" vì trùng "máy số" khi bỏ dấu; merge #158, #159 <br> - Mở lại `source_url` của 20 hotline, cập nhật số MobiFone, merge #168 <br> - Review PR của AI1: SH-09/SH-10 vào plan (#161), ngưỡng fuse (#163), báo cáo eval M4 (#164), luật `R_OVERPAY_REFUND` (#166), `judge_v3` (#167), đo token Bedrock (#169) | 17/11/2026 | 17/11/2026 | [#168](https://github.com/ask-first-fcaj/ask-first/pull/168), [commit 3216682](https://github.com/ask-first-fcaj/ask-first/commit/3216682), [#161](https://github.com/ask-first-fcaj/ask-first/pull/161), [#163](https://github.com/ask-first-fcaj/ask-first/pull/163), [#164](https://github.com/ask-first-fcaj/ask-first/pull/164), [#166](https://github.com/ask-first-fcaj/ask-first/pull/166), [#167](https://github.com/ask-first-fcaj/ask-first/pull/167), [#169](https://github.com/ask-first-fcaj/ask-first/pull/169) |
| 4 | - Lập kế hoạch test T0–T13 trên account riêng; chuẩn bị account: IAM user, MFA root, 3 budget, bot Telegram test <!-- NHÁP: chỉ có trong nhật ký cá nhân, chưa có link công khai --> | 18/11/2026 | 19/11/2026 | [Comment #39](https://github.com/ask-first-fcaj/ask-first/issues/39#issuecomment-7126855221) |
| 5 | - Dựng S3 (artifact model, kiểm sha256), DynamoDB (cache có TTL), SSM `SecureString` cho token bot, ECR + Lambda `ai` dạng image <br> - Nối `init_runtime` bản `real` với S3, DynamoDB, Telegram; nạp trước RAG và OCR ở pha INIT; mở PR #173 <br> - Gọi Lambda bằng fixtures, đo cold/warm và so 3 mức memory 1769/2048/3008 MB <br> - Mở PR script đo độ dễ đọc (AI-32, #172) | 19/11/2026 | 19/11/2026 | [PR #173](https://github.com/ask-first-fcaj/ask-first/pull/173), [Comment #39](https://github.com/ask-first-fcaj/ask-first/issues/39#issuecomment-7126855221), [PR #172](https://github.com/ask-first-fcaj/ask-first/pull/172) |
| 6 | - Bỏ trích dẫn trùng trong "Vì sao Bot nghi ngờ", deploy lên Lambda thử (AI-06, #174) <br> - Nối `url_net` vào `RealPipeline` để mở link rút gọn (AI-24b, #175) <br> - Test DLQ: chặn `ai` (concurrency 0), xem `worker` thử lại 5 lần rồi đẩy vào DLQ; báo 2 lỗi cho BE1 ở #42, #43 <br> - Xem chi phí thật bằng Cost Explorer | 20/11/2026 | 20/11/2026 | [#174](https://github.com/ask-first-fcaj/ask-first/pull/174), [#175](https://github.com/ask-first-fcaj/ask-first/pull/175), [Comment #42](https://github.com/ask-first-fcaj/ask-first/issues/42#issuecomment-7030554738), [Comment #43](https://github.com/ask-first-fcaj/ask-first/issues/43#issuecomment-7030554190), [Cost Explorer](https://docs.aws.amazon.com/cost-management/latest/userguide/ce-what-is.html) |

### Kết quả đạt được

* Mask PII 500 mẫu ([#158](https://github.com/ask-first-fcaj/ask-first/pull/158), [mask-pii.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/mask-pii.md)): recall trên tập heldout từ 101/132 lên 130/132; che nhầm mẫu bẫy giữ 1/20.
* Luật OTP ([#159](https://github.com/ask-first-fcaj/ask-first/pull/159), [otp-rules.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/otp-rules.md)): tin OTP thật bật nhầm `R_OTP_REQUEST` 1/100; tin lừa bị sót 0/100.
* Độ dễ đọc ([#172](https://github.com/ask-first-fcaj/ask-first/pull/172), [readability.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/readability.md)): 0/99 lời giải thích dài quá 600 ký tự, nhưng 25/99 có câu trung bình quá 20 tiếng và 20/99 có tiếng Anh ngoài "OTP"/"link".
* Lambda `ai` trên account riêng, 3008 MB, x86_64, `llm_enabled=false` ([#173](https://github.com/ask-first-fcaj/ask-first/pull/173)):

| Chỉ số | Trước khi nạp ở INIT | Sau khi nạp ở INIT |
| --- | --- | --- |
| Tin chữ đầu tiên sau cold start | 17,1 s | 6,6 s |
| Ảnh đầu tiên sau cold start | ~19 s | 2,5 s (Init 6,2 s) |

* So memory ([#173](https://github.com/ask-first-fcaj/ask-first/pull/173)): 2048 MB Init 7,1 s; 1769 MB Init chạm trần 10 s và tin đầu mất 35 s, nên Lambda `ai` cần ít nhất 2048 MB; nhóm chọn 3008 MB.
* Chi phí đo trên Cost Explorer cho 01–20/11: tổng 0,0025 USD trước credit, trả 0 USD. <!-- NHÁP: số chi phí chỉ có trong nhật ký cá nhân (ai2-quan-ly-cong-viec.md, T13), chưa có link công khai; Minh cân nhắc dán ảnh Cost Explorer -->
* Review 6 PR của AI1 ngày 17/11.

### Bài học rút ra

* Đo trên Lambda thật mới thấy cold start 17–19 s; trên máy dev không có vấn đề này. Nạp model ở INIT làm mọi cold start dài thêm, đổi lại tin đầu không phải chờ lâu ([#173](https://github.com/ask-first-fcaj/ask-first/pull/173)).
* Pha INIT của Lambda có trần 10 s; ở 1769 MB (khoảng 1 vCPU) model không kịp nạp. Memory là cách duy nhất để tăng CPU.
* Test DLQ bằng cách cố tình chặn Lambda phát hiện được lỗi gửi tin "bận" hai lần mà test đơn vị không thấy ([#42](https://github.com/ask-first-fcaj/ask-first/issues/42#issuecomment-7030554738)).
