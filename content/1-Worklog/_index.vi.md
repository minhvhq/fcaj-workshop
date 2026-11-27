---
title: "Nhật ký công việc"
date: 2026-09-14
weight: 1
chapter: false
pre: " <b> 1. </b> "
---

Trong dự án nhóm **Ask First (Hỏi Trước)**, tôi là **AI2**. Phần tôi phụ trách nằm ở hai đầu của pipeline AI: đầu vào (bộ ảnh và OCR cho ảnh chụp màn hình, mask PII, chuẩn hoá văn bản, rules engine, URL intel, tải media từ Telegram) và phần dữ liệu đo lường (kho tri thức có nguồn, RAG local, eval gate trên CI, các bộ đo mask, OTP, độ dễ đọc). Từ 19/11/2026, tôi tự dựng thêm một môi trường AWS riêng để chạy thử Lambda `ai` cùng S3, DynamoDB, SSM và Telegram; từ 24/11/2026, môi trường này trở thành nơi cả nhóm chạy chung.

Cách tôi ghi nhật ký:

- Tuần 1–5 (14/09–16/10/2026) diễn ra trước khi nhóm có repo, nên không có commit hay PR để dẫn chứng. Các tuần này là bản ghi lại theo lộ trình [First Cloud AI Journey](https://cloudjourney.awsstudygroup.com/) và đều có ghi chú nháp để tôi tự kiểm lại.
- Tuần 6–11 (19/10–27/11/2026) gắn với PR, commit, issue và bình luận review trong repo [ask-first-fcaj/ask-first](https://github.com/ask-first-fcaj/ask-first). Mỗi số đo đều có link tới nơi nó được ghi.
- Tuần 12 (30/11–04/12/2026) là kế hoạch, chưa diễn ra.

| Tuần | Thời gian | Nội dung chính |
| --- | --- | --- |
| 1 | 14–18/09 | [Khởi động FCAJ, bộ công cụ lập trình, IAM user và AWS CLI](1.1-week1/) |
| 2 | 21–25/09 | [S3, DynamoDB, KMS và Parameter Store; dự Buildrathon Kickoff](1.2-week2/) |
| 3 | 28/09–02/10 | [Container, Lambda và hàng đợi; xem AWS Cloud & AI Day](1.3-week3/) |
| 4 | 05–09/10 | [Vào nhóm Ask First với vai AI2, khảo sát OCR tiếng Việt](1.4-week4/) |
| 5 | 12–16/10 | [Gom bộ ảnh tin lừa, gán nhãn và tìm hiểu cách che PII](1.5-week5/) |
| 6 | 19–23/10 | [Hướng dẫn gán nhãn, spike OCR ba stack và tập test độc lập](1.6-week6/) |
| 7 | 26–30/10 | [Mask PII và kho tri thức có nguồn](1.7-week7/) |
| 8 | 02–06/11 | [Rules engine, URL intel và tải media Telegram](1.8-week8/) |
| 9 | 09–13/11 | [RAG với e5-small, nối RAG vào pipeline, eval gate trên CI](1.9-week9/) |
| 10 | 16–20/11 | [Ba bộ đo chất lượng và môi trường AWS riêng cho Lambda `ai`](1.10-week10/) |
| 11 | 23–27/11 | [VietOCR, môi trường AWS chung của nhóm, blog và site báo cáo](1.11-week11/) |
| 12 | 30/11–04/12 | [Nộp workshop, demo và dọn tài nguyên (kế hoạch)](1.12-week12/) |
