---
title: "Blog 2"
date: 2026-11-27
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# Running an OCR model on AWS Lambda: cold-start tuning with the INIT phase and the 10-second trap

**Author:** Nguyen Nho Chi Thien (AI1, Ask First team) · Team blog, shared under the FCAJ rules

{{% notice info %}}
The post is still being finalised by Nguyen Nho Chi Thien. The AWS Study Group link, publish date and summary will be updated from the author's final version.
{{% /notice %}}
<!-- NHÁP: AI1's site only has the title so far. Minh syncs the summary + link once Thien publishes -->

### Topic

The post covers loading an OCR model inside AWS Lambda: using the INIT phase to cut the wait on the first invocation (cold start), and the 10-second INIT limit to watch out for with large models.
