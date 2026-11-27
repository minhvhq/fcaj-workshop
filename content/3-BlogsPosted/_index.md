---
title: "Blogs Posted"
date: 2026-11-27
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

This section collects the technical posts I wrote, or co-produced with the Ask First team, for the [AWS Study Group](https://www.facebook.com/groups/awsstudygroupfcj). Each one starts from a real problem the team hit while putting AI models on AWS Lambda.

### [Blog 1 - Vietnamese OCR on AWS Lambda: when the model reads "chuyển tiền" as "chuyn tin"](3.1-Blog1/)
My own post. It explains how I traced why the OCR model dropped Vietnamese letters, how VietOCR (ONNX) re-reads selected lines and merges word by word, how the model is loaded from S3 during the Lambda INIT phase, and what we measured on a real Lambda in ap-southeast-1.

### [Blog 2 - Running an OCR model on AWS Lambda: cold-start tuning with the INIT phase and the 10-second trap](3.2-Blog2/)
A team blog written by Nguyen Nho Chi Thien (AI1), shared under the FCAJ rules. The topic is cold start when loading an OCR model inside Lambda.

### [Blog 3 - Waiting for the team's third post](3.3-Blog3/)
The team's third post has not been published yet. This page will be updated once it is.
