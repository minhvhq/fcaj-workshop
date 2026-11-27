---
title: "Blog 2"
date: 2026-11-27
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# Chạy model OCR trên AWS Lambda: tối ưu cold start bằng pha INIT và cái bẫy giới hạn 10 giây

**Tác giả:** Nguyễn Nho Chí Thiện (AI1, nhóm Ask First) · Blog nhóm, dùng chung theo quy định của FCAJ

{{% notice info %}}
Bài đang được Nguyễn Nho Chí Thiện hoàn thiện. Link bài đăng AWS Study Group, ngày đăng và nội dung tóm tắt sẽ được cập nhật theo bản chính thức của tác giả.
{{% /notice %}}
<!-- NHÁP: site AI1 mới có tiêu đề, chưa có nội dung. Minh đồng bộ lại tóm tắt + link khi Thiện đăng bài -->

### Chủ đề

Bài viết về việc nạp model OCR trong AWS Lambda: dùng pha INIT để giảm thời gian chờ ở lần gọi đầu (cold start), và giới hạn 10 giây của pha INIT cần lưu ý khi model lớn.
