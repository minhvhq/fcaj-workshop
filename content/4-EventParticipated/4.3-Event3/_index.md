---
title: "AWS Student Community Day Vietnam 2026"
date: 2026-11-27
weight: 3
chapter: false
pre: " <b> 4.3. </b> "
---

# AWS Student Community Day Vietnam 2026

| Item | Details |
|---|---|
| Date and time | Saturday, 21/11/2026, 08:30–11:30 |
| Venue | AWS office, 26th floor, Bitexco Tower, Ho Chi Minh City |
| Organisers | Four AWS Student Builder Groups (HUFLIT, HCMUTE, PTIT HCM, SGU), as part of FCAJ |
| My role | Attendee |

### Programme

- 08:30–09:10: check-in and opening.
- 09:10–09:20: "Accompanying young developers".
- 09:20–09:40: "Securing enterprise cloud environments".
- 09:40–11:00: four Student Builder Groups (HCMUTE, SGU, HUFLIT, PTIT) presenting, 20 minutes each.
- 11:00–11:30: Quiz Quest and Word Quest mini games, then closing.

### What I took away

The event landed in the same week I was building the team's shared AWS environment on a separate account, so I paid close attention to the security session.

- **Least privilege per component.** Each function should have a role with only the permissions its job needs, and those permissions belong in the infrastructure template rather than being granted by hand.
- **Secrets do not belong in code or in plain environment variables.**
- **Know who did what on the account.** When several people share one environment, you need a trail to trace changes.
- From the student group presentations: the clearest teams showed measurements and the bugs they hit, not only an architecture diagram.

<!-- NHÁP: takeaways need Minh's confirmation -->

### Connection to my work

- When wiring the real pipeline to S3, DynamoDB and Telegram and preloading models at Lambda startup ([PR #173](https://github.com/ask-first-fcaj/ask-first/pull/173)), I had to give the `ai` function read access to exactly the model prefix on S3 and write access to the DynamoDB table, nothing broader.
- While testing each component on the environment I set up, I found and reported backend integration bugs so the team could fix them before the demo.
- What I want to do next: review the permissions still granted outside the template so that everything lives in IaC.

### Lesson

Running a shared environment is a team job, but whoever builds it must keep permissions and secrets tidy from day one; fixing that later costs much more.

### Check-in photos

{{% notice info %}}
Check-in photo with a clear face: Minh will add it.
{{% /notice %}}
