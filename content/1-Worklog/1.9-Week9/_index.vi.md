---
title: "Nhật ký tuần 9"
date: 2026-11-09
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

### Mục tiêu tuần 9

* Dựng RAG chạy local trong Lambda, không gọi dịch vụ embedding bên ngoài (AI-29), rồi nối vào `RealPipeline` (INT-03).
* Đưa eval vào CI để PR nào làm hỏng luật hay KB đều bị phát hiện (AI-09).
* Góp điểm chấm tay cho thí nghiệm LLM-as-a-judge của AI1 (AI-11).

### Công việc trong tuần

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Soạn corpus cảnh báo lừa đảo từ trang `.gov.vn` và báo chí cho RAG <!-- NHÁP: không có commit 09/11, ngày bắt đầu tự điền --> | 09/11/2026 | 11/11/2026 | [kb/alerts.yaml](https://github.com/ask-first-fcaj/ask-first/blob/main/kb/alerts.yaml) |
| 3 | - Viết embedder e5-small int8, `build_index.py` và retriever; đo Recall@4 <!-- NHÁP: không có commit 10/11, ngày bắt đầu tự điền --> | 10/11/2026 | 11/11/2026 | [PR #147](https://github.com/ask-first-fcaj/ask-first/pull/147) |
| 4 | - Mở và merge RAG local (#147) <br> - Chấm tay 20 mẫu AI-11 theo rubric, chấm trước khi xem điểm LLM (#146) <br> - Mở PR nối RAG vào `RealPipeline` + script upload artifact (#151) và PR eval gate trên CI (#153) <br> - Review loạt PR của AI1: spike Bedrock Qwen3 32B (#132), benchmark 4 model (#133), `outputConfig` (#134), Guardrails (#135), LLM-as-a-judge (#136), red-team (#137), Gemini tham chiếu (#141), model dự phòng (#142), sửa judge (#144, #145 approve), luật OTP/CCCD (#149), chấm tay G1 (#150) | 11/11/2026 | 11/11/2026 | [PR #146](https://github.com/ask-first-fcaj/ask-first/pull/146), [PR #147](https://github.com/ask-first-fcaj/ask-first/pull/147), [#151](https://github.com/ask-first-fcaj/ask-first/pull/151), [#153](https://github.com/ask-first-fcaj/ask-first/pull/153) |
| 5 | - Sửa theo review và merge #151, #153; eval gate chạy thật trên PR và comment bảng so sánh | 12/11/2026 | 12/11/2026 | [PR #151](https://github.com/ask-first-fcaj/ask-first/pull/151), [PR #153](https://github.com/ask-first-fcaj/ask-first/pull/153) |
| 6 | - Bắt đầu câu chữ tin bot (SH-08) theo phiếu đọc thử cho người lớn tuổi <!-- NHÁP: không có commit 13/11, Minh kiểm lại --> | 13/11/2026 | 13/11/2026 | [PR #157](https://github.com/ask-first-fcaj/ask-first/pull/157) |
| 7 | - Review 2 PR của BE2: luồng "Bot báo sai" (BE-30, #155) và `/rutdongy` xoá dữ liệu (BE-31, #156) | 14/11/2026 | 14/11/2026 | [#155](https://github.com/ask-first-fcaj/ask-first/pull/155), [#156](https://github.com/ask-first-fcaj/ask-first/pull/156) |

### Kết quả đạt được

* RAG local ([#147](https://github.com/ask-first-fcaj/ask-first/pull/147)): corpus 103 cảnh báo; Recall@4 thẻ kịch bản trên `real_scam` 35/38 = 92,1% (ngưỡng 90%); truy vấn p50 7,9 ms, p95 9,7 ms trên laptop, tin 512 token khoảng 57 ms.
* RAG nối vào pipeline ([#151](https://github.com/ask-first-fcaj/ask-first/pull/151)): trích tối đa 3 cảnh báo đúng mã kịch bản của kết luận; nạp model lỗi thì chạy tiếp không RAG, không log nội dung tin.
* Eval gate trên CI ([#153](https://github.com/ask-first-fcaj/ask-first/pull/153)): chạy eval trên nhánh gốc và PR, so với baseline; khi chưa có role Bedrock thì chỉ chạy luật và chặn khi tệ hơn baseline quá 2 điểm.
* Review 15 PR trong tuần (13 của AI1, 2 của BE2).

### Bài học rút ra

* Chỉ chạy luật thì recall chỉ khoảng 19%, nên ngưỡng tuyệt đối 90% không áp được; so với baseline mới là cách bắt PR làm hỏng luật ([#153](https://github.com/ask-first-fcaj/ask-first/pull/153)).
* Cách gộp điểm top-3 của retriever được chọn trên chính bộ đo, nên Recall@4 cần thêm một tập độc lập để tin hơn.
