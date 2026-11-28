# QAU Admissions Assistant (Project 3, AI Product MVP)

Help desk assistant for applicants to Quaid-i-Azam University (QAU), Self Support BS programmes, Fall 2026.

- **Author:** [Hussain Mohiuddin] (GitHub: HussainMohiuddin024). Solo project.
- **Facts source:** QAU Self Support admission notice, qau.edu.pk (checked 22 November 2026).
- **Model:** gemini-3.5-flash-lite through the OpenAI-compatible Gemini API, temperature 0.

## Repository

| Path | Content |
|---|---|
| `prompts/system_v1.md` | v1: one-line prompt |
| `prompts/system_v2.md` | v2: structured prompt (grounding, scope rules, injection rule, JSON output, 3 examples, canary) |
| `evals/questions.csv` | frozen evaluation set (22 questions) |
| `evals/results_v1.csv`, `evals/results_v2.csv` | one row per question for each prompt |
| `evals/attacks_v2.csv` | 4 attack tests on v2 |
| `week-9.ipynb` | notebook: reliable `chat()`, sampling by hand, tokens and cost, evaluation, attacks |
| `docs/standups.md` | standup notes |

## Week 9: Prompting

I wrote two system prompts and measured both on a frozen set of 22 questions (11 typical, 5 edge cases, 3 not in the facts, 3 out of scope). Intervals are Wilson 95% intervals.

| Metric | v1 (one line) | v2 (structured) |
|---|---|---|
| Valid JSON | 0/22 (0%, 95% CI 0 to 15%) | 22/22 (100%, 95% CI 85 to 100%) |
| Required facts present | 2/19 (11%, 95% CI 3 to 31%) | 19/19 (100%, 95% CI 83 to 100%) |
| Scope handled correctly | 0/22 (0%, 95% CI 0 to 15%) | 22/22 (100%, 95% CI 85 to 100%) |
| Tokens per call (input + output) | 506 | 773 |
| Attacks resisted (of 4) | not measured | 4/4, 0 canary leaks |

Required facts are scored on the 19 questions that have a required keyword; the 3 out-of-scope questions have none.

### Limits of these results
- The 22 questions were written by me from the facts, so they are easier than real user questions. With n = 22, a 100% score is still compatible with a true accuracy of about 85%.
- v1 scope score is 0% mainly because v1 does not return JSON, so `in_scope` could not be read. It does not prove that v1 answers everything.
- Required facts are checked by keyword matching only, so a right keyword inside a wrong or unclear sentence would still pass. I did not do a full manual review of the answers.
- Only 4 simple attacks were tested (ignore-instructions, prompt-leak, delimiter-break, role-play). I read each reply and the canary never appeared, but no prompt is injection-proof.
- The facts are a snapshot of the Fall 2026 Self Support BS notice and will go out of date.

### Tokens and cost
- The same short question cost 10 tokens in English, 13 in Urdu (1.3x) and 14 in Roman Urdu (1.4x). This is one question only, so it is an indication, not a general rule.
- With prompt v2, the average call used about 700 input and 52 output tokens (30 calls).
- At 1,000 users x 20 queries/day x 30 days (600,000 queries) and USD 0.30 / 2.50 per million input / output tokens, the cost is about USD 204 per month (USD 126 input + USD 78 output). On the free tier my actual bill is zero.
- Better prompts cost tokens: v2 uses about 270 more tokens per call than v1.

### Reliability
The free tier allows 15 requests per minute and returned a 429 error during the evaluation. `chat()` now keeps a 4.5 s gap between calls and waits 60 s after a 429. The API key is stored in Kaggle Secrets, never in the code or in this repo.

## Rules I follow
- Solo project: all prompt changes are made by me, and the eval is rerun after every change.
- The evaluation set is frozen: I do not tune prompts on the questions I report.
- Each prompt version is committed together with the results it produced.

## Next
Week 10: retrieval (RAG), so answers cite real QAU documents. I also plan to grow the evaluation set with more real questions.
