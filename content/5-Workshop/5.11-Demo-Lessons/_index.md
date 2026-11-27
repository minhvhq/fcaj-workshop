---
title: "Demo and Lessons Learned"
date: 2026-11-27
weight: 11
chapter: false
pre: " <b> 5.11. </b> "
---

#### 5.11.1 Demo

- **Live bot:** [@askfirst_ai2_test_bot](https://t.me/askfirst_ai2_test_bot) on Telegram, running on exactly the environment built in this workshop. Send `/start`, tap consent, then forward a suspicious message or screenshot.
- **Result screenshots:** text message and screenshot in [5.6](../5.6-connect-telegram/).

{{% notice info %}}
Demo video: Minh will add the link (screen recording: sending a text message, sending an image, viewing logs in CloudWatch).
{{% /notice %}}

The demo script I use, with a place to verify each step on AWS:

| Step | On Telegram | Cross-check on AWS |
|---|---|---|
| 1 | Send a fake police message demanding a transfer to a "safe account" | `worker` log: `ai_invoke op=analyze ... outcome=ok`; a new `CHECK#` appears in the DynamoDB table |
| 2 | Send the exact same message again | `ai` log: `"cache_hit": true`, near-instant reply |
| 3 | Send a screenshot of a fake bank SMS containing a link | The `ai` log shows a longer duration (OCR); the result names the fake domain correctly |
| 4 | Send a greeting | Goes through the `fast` path, no Bedrock call |

#### 5.11.2 Lessons learned

**1. Deploy each component first, then connect them.** I followed the order S3 → DynamoDB → SSM + Telegram image download → `ai` Lambda → test-invoke with sample requests → connect to the backend stack. Each step had pass criteria and a negative test (wrong SHA-256 file, wrong `file_id`, wrong token). When the full pipeline got stuck at "⏳", I knew right away the AI part was not at fault because it had been tested separately, and found the bug in the contract between `webhook` and `worker`: `webhook` pushed the raw message into SQS, while `worker` expected an `AnalyzeJob`. This bug only surfaced when running on real AWS, because each side's tests were green.

**2. Choose memory from a table of measurements.** Lambda limits the INIT phase to 10 seconds. At 1769 MB, preloading the models made INIT hit the limit; Lambda cut INIT short and reran it during the first invocation, and the first message took 35 seconds. Three cold starts at three memory settings (section 5.7.2) were enough to choose 3008 MB. The number to keep watching is `maxInitMs` (9.55 seconds over the last 7 days): every new model preloaded during INIT pushes it closer to the limit.

**3. Quantization must be tested on the exact CPU it will run on.** The int8 version of VietOCR correctly read 82% of words with diacritics on a laptop, but only 49% on Lambda x86_64. The fp32 version produced identical results on the laptop and on Lambda across all 66 images. Since then, I rerun every model change on a temporary Lambda function before promoting it to the shared environment, then delete that function.

**4. Read OCR symptoms correctly.** The original OCR model was missing 88 Vietnamese characters (ả ạ ấ ộ ữ…), so it dropped whole vowels: "chuyển tiền" ("transfer money") became "chuyn tin". Stripping all diacritics from the reference text and running the rule set had no effect, but losing letters caused 15/33 rule signals to be missed. The fix was to have VietOCR re-read only the Vietnamese lines and merge word by word: correctly read words with diacritics rose from 37% to 89%, missed signals dropped to 6/33, and URLs and phone numbers stayed intact.

**5. Test failures by breaking things on purpose.** Setting the reserved concurrency of `ai` to 0 is the cheapest way to simulate the AI part going down. The first attempt showed the user receiving **two** "busy" messages, and another message could be mistakenly edited into "busy". No local test caught either of these bugs.

**6. An alarm with no recipient might as well not exist.** The DLQ alarm transitioned to `ALARM` at the right time, but `AlarmActions` was empty, so nobody knew. Now the alarm sends email through SNS, and I test it with `set-alarm-state` instead of waiting for a real failure.

**7. Secrets leak through unexpected places.** The bot token once leaked in a terminal screenshot. Since then, the token lives only in SSM `SecureString`, is read into a shell variable when needed and removed right after, and every screenshot is redacted before being shared.

**8. Packaging containers for Lambda has its own pitfalls.** The image must preserve the repo's directory tree because the code locates `prompts/` via a relative path; recent Docker versions must build with `--provenance=false` for Lambda to accept the manifest.

#### 5.11.3 Future improvements

- Add a `dynamodb:LeadingKeys` condition to the `ai` role so it can only read/write `CACHE#` and `URL#` keys and cannot touch user data.
- Move the `ai` Lambda into `infra/template.yaml` (the `AiImageUri` parameter) so the whole system deploys with one command, and set `RetentionInDays` for logs directly in the template.
- Move some work out of the INIT phase (for example, lazily loading VietOCR on the first image) so `maxInitMs` keeps a comfortable safety margin below the 10-second limit.
- Try arm64 (Graviton) Lambda with an embedder that does not depend on the x86 instruction set, since arm64 is cheaper per GB-second.
- Re-measure OCR accuracy on a fresh, unseen set of images, run exactly once, without tuning the code to the results.
