---
title: "Nhật ký tuần 1"
date: 2026-09-14
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---

<!-- NHÁP: cả tuần 1 chưa có bằng chứng (trước khi có repo), Minh kiểm lại trước khi nộp -->

### Mục tiêu tuần 1

* Nắm cách chương trình FCAJ vận hành và lộ trình học trên trang Cloud Journey.
* Chuẩn bị máy làm việc: Git, Python, Docker, VS Code.
* Có một tài khoản AWS đăng nhập qua IAM Identity Center và AWS CLI dùng profile SSO, không dùng access key dài hạn.

### Công việc trong tuần

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Nhận thông tin chương trình, lịch sinh hoạt và cách nộp báo cáo <br> - Đọc lộ trình học và đánh dấu các module liên quan tới serverless và AI | 14/09/2026 | 14/09/2026 | [FCAJ Cloud Journey](https://cloudjourney.awsstudygroup.com/), [Rules FCAJ](https://hcm-rules.awsfcaj.com/) |
| 3 | - Cài Git, cấu hình SSH key cho GitHub <br> - Cài Python 3.12 và `uv`, tạo thử một project nhỏ có `pyproject.toml` | 15/09/2026 | 15/09/2026 | [uv](https://docs.astral.sh/uv/), [GitHub SSH](https://docs.github.com/en/authentication/connecting-to-github-with-ssh) |
| 4 | - Cài Docker Desktop, chạy container mẫu, đọc cách viết Dockerfile nhiều tầng | 16/09/2026 | 16/09/2026 | [Docker docs](https://docs.docker.com/get-started/) |
| 5 | - Bật IAM Identity Center, tạo user và permission set cho việc hằng ngày <br> - Cấu hình `aws configure sso`, đăng nhập bằng `aws sso login` | 17/09/2026 | 17/09/2026 | [IAM Identity Center](https://docs.aws.amazon.com/singlesignon/latest/userguide/what-is.html), [CLI SSO](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-sso.html) |
| 6 | - Mở Cost Explorer, xem chi phí theo service và theo ngày <br> - Thử AWS CloudShell và viết script boto3 in danh sách Region | 18/09/2026 | 18/09/2026 | [Cost Explorer](https://docs.aws.amazon.com/cost-management/latest/userguide/ce-what-is.html), [boto3](https://boto3.amazonaws.com/v1/documentation/api/latest/index.html) |

### Kết quả đạt được

* Máy cá nhân chạy được Git qua SSH, Python với `uv` và Docker.
* Đăng nhập AWS CLI bằng profile SSO, phiên tự hết hạn nên không phải giữ access key trên máy.
* Biết chỗ xem chi phí theo service trong Cost Explorer, dùng lại ở tuần 10 khi đo chi phí môi trường test.

<!-- NHÁP: kết quả tuần 1 tự điền, Minh kiểm lại -->

### Bài học rút ra

* Chuẩn bị công cụ sớm giúp các tuần sau chỉ tập trung vào dịch vụ AWS.
* Đăng nhập bằng phiên ngắn hạn an toàn hơn so với lưu access key trong file cấu hình.
