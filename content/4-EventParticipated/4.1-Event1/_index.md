---
title: "Buildrathon Kickoff × CMC Global"
date: 2026-11-27
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---

# Buildrathon Kickoff: Code the Future with CMC Global

| Item | Details |
|---|---|
| Date and time | Saturday, 26/09/2026, 09:00–12:00 |
| Venue | AWS office, 26th floor, Bitexco Tower, Ho Chi Minh City |
| Organisers | AWS First Cloud AI Journey (FCAJ) with CMC Global |
| Speakers | Thien Lu, Phong Pham (Program Managers, FCAJ); Bui Nhat Truong (Technical Leader, CMC Global) |
| My role | Attendee |

![Poster of Buildrathon Kickoff: Code the Future with CMC Global](/images/4-EventParticipated/event1-poster.jpg)

### Programme

The morning had three parts: the Buildrathon kickoff (09:00–09:30), the talkshow "The Thinking Behind Building the Bot" (09:30–11:15) and mock interviews (11:15–12:00).

### What I took away

The event fell in the second week of my internship, when I was still reviewing AWS basics and had no concrete task on the project yet. I noted three ideas:

1. **Real input differs from demo input.** The talkshow spent a lot of time on users sending a bot things nobody anticipated. This later became very concrete for me: screenshots that elderly users send to Ask First have cropped text, messages with injected characters to dodge filters, and an OCR model that drops Vietnamese letters.
2. **You need a way to know whether the bot is right.** Without a test dataset, every fix is a guess. This is why I built a labelled image set before choosing an OCR model.
3. **Briefly presenting your own work.** The mock interview showed me I was not used to summarising a technical task in a few sentences.

### Connection to my work

In October, when I took the OCR spike (AI-20), I started from a 50-image labelled set and only then compared three local OCR options ([PR #97](https://github.com/ask-first-fcaj/ask-first/pull/97)). I then added an independent 16-image test set ([PR #99](https://github.com/ask-first-fcaj/ask-first/pull/99)) so I would not grade myself on data I had already looked at. That approach came from the second idea above.

### Lessons

- Test data should exist before the model.
- Practise explaining technical work in order: problem, how it was measured, result.

<!-- NHÁP: takeaways need Minh's confirmation -->

### Check-in photos

{{% notice info %}}
Check-in photo with a clear face: Minh will add it.
{{% /notice %}}
