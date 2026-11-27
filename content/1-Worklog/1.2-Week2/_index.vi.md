---
title: "Nhật ký tuần 2"
date: 2026-09-21
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---


### Mục tiêu tuần 2

* Học kỹ hai nơi lưu dữ liệu sẽ dùng nhiều nhất trong serverless: Amazon S3 và Amazon DynamoDB.
* Biết cách giữ bí mật (token, khoá) bằng AWS KMS và SSM Parameter Store.
* Dự sự kiện Buildrathon Kickoff × CMC Global.

### Công việc trong tuần

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - S3: Block Public Access, mã hoá mặc định SSE-S3, versioning <br> - Tạo presigned URL để tải một file trong thời gian giới hạn | 21/09/2026 | 21/09/2026 | [Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html), [Presigned URL](https://docs.aws.amazon.com/AmazonS3/latest/userguide/using-presigned-url.html) |
| 3 | - Viết script boto3 upload file lên S3 kèm sha256, tải về rồi so lại checksum | 22/09/2026 | 22/09/2026 | [S3 checksums](https://docs.aws.amazon.com/AmazonS3/latest/userguide/checking-object-integrity.html) |
| 4 | - DynamoDB: bảng on-demand, khoá `PK`/`SK`, bật TTL để item tự hết hạn | 23/09/2026 | 23/09/2026 | [DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html), [TTL](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/TTL.html) |
| 5 | - Lưu chuỗi bí mật dạng `SecureString` trong Parameter Store, đọc bằng `get_parameter(WithDecryption=True)` <br> - Đọc cách KMS key mặc định `aws/ssm` cấp quyền giải mã | 24/09/2026 | 24/09/2026 | [Parameter Store](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html), [AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/overview.html) |
| 6 | - Viết IAM policy tối thiểu cho một ứng dụng chỉ đọc một bucket, một bảng và một tham số SSM <br> - Xoá tài nguyên thử trong tuần | 25/09/2026 | 25/09/2026 | [IAM policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html) |
| 7 | - Dự Buildrathon Kickoff × CMC Global | 26/09/2026 | 26/09/2026 | Mục [Sự kiện](../../4-eventparticipated/) |

### Kết quả đạt được

* Script upload/tải S3 có kiểm sha256, ý tưởng này sau dùng lại khi kiểm artifact model ở tuần 10.
* Hiểu cách TTL của DynamoDB xoá item cũ mà không cần job dọn.
* Có mẫu policy chỉ cấp `s3:GetObject`, `dynamodb:GetItem`/`PutItem` và `ssm:GetParameter`.


### Bài học rút ra

* TTL của DynamoDB không xoá ngay lúc hết hạn, nên code đọc vẫn phải tự kiểm thời hạn.
* Tách bí mật ra Parameter Store giúp code và repo không chứa token.
