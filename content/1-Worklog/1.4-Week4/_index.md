---
title: "Week 4 Worklog"
date: 2026-10-05
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

<!-- NHÁP: cả tuần 4 chưa có bằng chứng (trước khi có repo), Minh kiểm lại trước khi nộp -->

### Week 4 objectives

* Join the project team, agree on the Ask First topic and take the AI2 role.
* Answer a team question: should Vietnamese text in screenshots be read by an AWS service or by open-source OCR running inside Lambda?
* Read ahead on multilingual embeddings for the alert lookup.

### Tasks this week

| Day | Task | Start date | Completion date | Reference |
| --- | --- | --- | --- | --- |
| Mon | - Team formation meeting with 4 people (BE1, BE2, AI1, AI2); I take AI2 <br> - Contribute ideas to the topic: a bot where older people send suspected scam messages and ask before acting | 05/10/2026 | 05/10/2026 | [research-concept.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/research-concept.md) |
| Tue | - Read the languages supported by Amazon Textract and Rekognition `DetectText`; note that neither reads Vietnamese diacritics well yet | 06/10/2026 | 06/10/2026 | [Amazon Textract](https://docs.aws.amazon.com/textract/latest/dg/what-is.html), [Rekognition text](https://docs.aws.amazon.com/rekognition/latest/dg/text-detection.html) |
| Wed | - Try Tesseract (`vie`), PaddleOCR and RapidOCR on 5 self-made message screenshots, note how they handle URLs and diacritics | 07/10/2026 | 07/10/2026 | [RapidOCR](https://github.com/RapidAI/RapidOCR), [PaddleOCR](https://github.com/PaddlePaddle/PaddleOCR), [Tesseract](https://github.com/tesseract-ocr/tesseract) |
| Thu | - Read about `intfloat/multilingual-e5-small` and ONNX Runtime for running embeddings on CPU | 08/10/2026 | 08/10/2026 | [multilingual-e5-small](https://huggingface.co/intfloat/multilingual-e5-small), [ONNX Runtime](https://onnxruntime.ai/docs/) |
| Fri | - Split AI1/AI2 roles with the team: I take the input side (images, OCR, PII masking, URLs) and measurement data; AI1 takes the Bedrock judge and eval | 09/10/2026 | 09/10/2026 | [implementation-plan.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/implementation-plan.md) |

### Results

* A clear topic and role within the team.
* Quick comparison notes between AWS OCR services and open-source OCR, which fed the OCR spike in week 6.

<!-- NHÁP: kết quả tuần 4 tự điền, Minh kiểm lại -->

### Lessons learned

* Before choosing a managed service, check the languages it supports, especially for Vietnamese.
* Trying a few real images gives a quick feel, but a decision needs a labeled image set and a clear scoring method.
