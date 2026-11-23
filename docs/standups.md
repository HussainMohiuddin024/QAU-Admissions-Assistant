# Standup notes

## Week 9 (22 November 2026)

**Done**
- Set up Kaggle notebook with a reliable `chat()` (retries, exponential backoff with jitter, token logging); key kept in Kaggle Secrets.
- Computed temperature and top-p by hand (logits [5, 3, 1]) and ran 5 calls at T = 0 and T = 1 to count distinct answers.
- Measured tokens for the same question in English, Urdu and Roman Urdu (10, 13 and 14 tokens), and estimated monthly cost.
- Chose the product: admissions assistant for QAU Self Support BS (Fall 2026), with facts from the QAU admission notice.
- Wrote prompt v1 (one line) and v2 (structured: grounding, scope rules, injection rule, JSON output, 3 examples, canary).
- Built a frozen eval set of 22 questions and ran both prompts: v2 22/22 valid JSON, 19/19 facts, 22/22 scope.
- Ran 4 attacks with canary check: 4/4 resisted, 0 canary leaks.
- Uploaded prompts, eval files and notebook to GitHub; wrote README.

**Next**
- Week 10: retrieval (RAG), so answers cite real QAU documents.
- Grow the eval set with more real questions (target 50+ by Week 12).

**Blocked (and how it was fixed)**
- Wrong model name (400 error), then a model that is no longer available to new users (404). Fixed by choosing gemini-3.5-flash-lite from the model list.
- Free-tier rate limit (429, 15 requests/minute) during the eval. Fixed by adding a 4.5 s gap between calls and a 60 s wait on 429.
- Kaggle session restart lost variables (NameError). Fixed by rerunning the cells from the top.
