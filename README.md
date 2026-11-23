# QAU Admissions Assistant (Project 3, AI Product MVP)

Help desk assistant for applicants to Quaid-i-Azam University (QAU), Self Support BS programmes, Fall 2026.
Facts source: QAU Self Support admission notice, qau.edu.pk (checked 22 November 2026).
Author: [Hussain Mohiuddin] (GitHub: HussainMohiuddin024). Solo project.

## Week 9: Prompting

I wrote two system prompts (`prompts/system_v1.md`, `prompts/system_v2.md`) and measured them on a frozen set of 22 questions (`evals/questions.csv`), temperature 0, model gemini-3.5-flash-lite. Intervals are Wilson 95% intervals.

| Metric | v1 (one line) | v2 (structured) |
|---|---|---|
| Valid JSON | 0/22 (0%, CI 0 to 15%) | 22/22 (100%, CI 85 to 100%) |
| Required facts present | 2/19 (11%, CI 3 to 31%) | 19/19 (100%, CI 83 to 100%) |
| Scope handled correctly | 0/22 (0%, CI 0 to 15%) | 22/22 (100%, CI 85 to 100%) |
| Tokens per call (input + output) | 506 | 773 |
| Attacks resisted (of 4) | not measured | 4/4, 0 canary leaks |

Limits:
- The 22 questions were written by me from the facts, so they are easier than real user questions. With n = 22, a 100% score is still compatible with a true accuracy of about 85%.
- v1 scope score is 0% mainly because v1 does not return JSON, so `in_scope` could not be read.
- Required facts are checked by keyword matching, so I also read every v2 answer by hand: [x]/22 were correct and clear.
- Only 4 simple attacks were tested; no prompt is injection-proof.

Tokens: the same question cost 10 tokens in English, 13 in Urdu (1.3x) and 14 in Roman Urdu (1.4x). This is one short question only.

Cost: about 700 input and 52 output tokens per call. At 1,000 users x 20 queries/day x 30 days (600,000 queries) and USD 0.30 / 2.50 per million input / output tokens, that is about USD 204 per month. On the free tier the actual bill is zero.

Reliability: the free tier allows 15 requests per minute and returned a 429 error, so `chat()` keeps a 4.5 s gap between calls and waits 60 s after a 429.

## Rules I follow
- Solo project: all prompt changes are made by me, and the eval is rerun after every change.
- The eval set is frozen: I never tune prompts on the questions I report.
- Each prompt version is committed together with the results it produced.
