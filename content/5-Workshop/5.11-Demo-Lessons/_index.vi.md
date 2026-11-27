---
title: "Demo và bài học"
date: 2026-11-27
weight: 11
chapter: false
pre: " <b> 5.11. </b> "
---

#### 5.11.1 Demo

- **Bot chạy thật:** [@askfirst_ai2_test_bot](https://t.me/askfirst_ai2_test_bot) trên Telegram, chạy trên đúng môi trường dựng trong workshop này. Gửi `/start`, bấm đồng ý, rồi chuyển tiếp một tin hoặc ảnh chụp màn hình đáng ngờ.
- **Ảnh chụp kết quả:** tin chữ và ảnh chụp màn hình ở [5.6](../5.6-connect-telegram/).

{{% notice info %}}
Video demo: Minh bổ sung link (quay màn hình: gửi tin chữ, gửi ảnh, xem log trên CloudWatch).
{{% /notice %}}

Kịch bản demo tôi dùng, mỗi bước kèm chỗ để chứng minh trên AWS:

| Bước | Trên Telegram | Đối chiếu trên AWS |
|---|---|---|
| 1 | Gửi tin giả công an đòi chuyển tiền vào "tài khoản an toàn" | Log `worker`: `ai_invoke op=analyze ... outcome=ok`; bảng DynamoDB có thêm `CHECK#` |
| 2 | Gửi lại đúng tin đó | Log `ai`: `"cache_hit": true`, trả lời gần như ngay |
| 3 | Gửi ảnh chụp SMS giả ngân hàng có link | Log `ai` có thời gian lớn hơn (OCR); kết quả nêu đúng tên miền giả |
| 4 | Gửi một câu chào hỏi | Đi `fast` path, không gọi Bedrock |

#### 5.11.2 Bài học

**1. Đưa từng thành phần lên rồi mới nối.** Tôi làm theo thứ tự S3 → DynamoDB → SSM + tải ảnh Telegram → Lambda `ai` → gọi thử bằng request mẫu → nối với stack backend. Mỗi bước có tiêu chí đạt và một phép thử âm tính (file sai SHA-256, `file_id` sai, token sai). Khi nối cả chuỗi bị kẹt ở "⏳", tôi biết ngay phần AI không có lỗi vì đã kiểm riêng từ trước, và tìm ra lỗi nằm ở hợp đồng giữa `webhook` và `worker`: `webhook` đẩy nguyên tin nhắn vào SQS, còn `worker` chờ một `AnalyzeJob`. Lỗi này chỉ lộ ra khi chạy trên AWS thật, vì test của từng bên đều xanh.

**2. Chọn memory bằng bảng số đo.** Lambda giới hạn pha INIT ở 10 giây. Ở 1769 MB, việc nạp sẵn model làm INIT chạm trần, Lambda cắt INIT rồi chạy lại trong lần gọi đầu, và tin đầu tiên mất 35 giây. Ba lần cold start ở ba mức memory (mục 5.7.2) đủ để chọn 3008 MB. Con số cần theo dõi tiếp là `maxInitMs` (9,55 giây trong 7 ngày gần nhất): mỗi model mới nạp ở INIT đều đẩy nó gần trần hơn.

**3. Lượng tử hoá phải kiểm trên đúng CPU sẽ chạy.** Bản int8 của VietOCR đọc đúng 82% từ có dấu trên laptop, nhưng trên Lambda x86_64 chỉ còn 49%. Bản fp32 cho kết quả trùng khớp giữa laptop và Lambda trên cả 66 ảnh. Từ đó, mọi thay đổi về model tôi đều chạy lại trên một function Lambda tạm trước khi đưa vào môi trường chung, rồi xoá function đó.

**4. Đọc đúng triệu chứng của OCR.** Model OCR ban đầu thiếu 88 chữ tiếng Việt (ả ạ ấ ộ ữ…) nên bỏ cả nguyên âm: "chuyển tiền" thành "chuyn tin". Bỏ hết dấu ở chữ đáp án rồi chạy bộ luật thì không ảnh hưởng gì, còn mất chữ cái thì 15/33 tín hiệu luật bị mất. Sửa bằng cách cho VietOCR đọc lại riêng các dòng tiếng Việt và ghép theo từng từ: từ có dấu đọc đúng tăng từ 37% lên 89%, tín hiệu bị mất còn 6/33, URL và số điện thoại giữ nguyên.

**5. Kiểm thử lỗi bằng cách cố tình làm hỏng.** Đặt reserved concurrency của `ai` bằng 0 là cách rẻ nhất để giả lập phần AI chết. Lần thử đầu cho thấy người dùng nhận **hai** tin "bận" và một tin khác có thể bị sửa nhầm thành "bận". Không có bài test nào trên máy phát hiện được hai lỗi này.

**6. Alarm không có người nhận thì như không có.** Alarm DLQ đã chuyển sang `ALARM` đúng lúc, nhưng `AlarmActions` rỗng nên không ai biết. Giờ alarm gửi email qua SNS, và tôi thử bằng `set-alarm-state` thay vì chờ lỗi thật.

**7. Bí mật lộ qua những chỗ không ngờ.** Token bot từng lộ trong một ảnh chụp màn hình terminal. Từ đó token chỉ nằm trong SSM `SecureString`, được đọc vào biến của cửa sổ lệnh khi cần rồi xoá ngay, và mọi ảnh chụp đều được che trước khi gửi.

**8. Đóng gói container cho Lambda có vài bẫy riêng.** Image phải giữ đúng cây thư mục của repo vì code tìm `prompts/` theo đường dẫn tương đối; Docker bản mới phải build với `--provenance=false` thì Lambda mới nhận manifest.

#### 5.11.3 Hướng phát triển

- Thêm điều kiện `dynamodb:LeadingKeys` vào role của `ai`, để nó chỉ đọc/ghi được các khoá `CACHE#` và `URL#`, không chạm được dữ liệu người dùng.
- Đưa Lambda `ai` vào `infra/template.yaml` (tham số `AiImageUri`) để cả hệ thống deploy bằng một lệnh, và đặt `RetentionInDays` cho log ngay trong template.
- Tách bớt việc ra khỏi pha INIT (ví dụ nạp VietOCR lười ở ảnh đầu tiên) để `maxInitMs` có khoảng an toàn xa trần 10 giây.
- Thử Lambda arm64 (Graviton) với một embedder không phụ thuộc tập lệnh x86, vì arm64 rẻ hơn trên mỗi GB-giây.
- Đo lại độ chính xác OCR trên một bộ ảnh mới chưa ai nhìn, chạy đúng một lần, không chỉnh code theo kết quả.
