# Week 7 — Issue selection

**Issue link:** Link: https://github.com/ascherj/pathreview/issues/151

**Issue title:** Bias detector patterns are too narrow to match common phrasings

**Tier:** [x] Tier 1 &nbsp;&nbsp; [ ] Tier 2 &nbsp;&nbsp; [ ] Tier 3

**Problem summary:**

> [In 3–5 sentences, in your own words: what the issue is (not a copy-paste of
> the title), what is currently broken or missing, and what a successful fix
> would accomplish. Naming the part of the codebase it affects is helpful context.]

In the system, the bias detector isn't able to detect phrases that contain bias
without using the near-exact phrases such as "bootcamp graduates lacks rigors".
When phrases with similar meaning are provided, the system fails to detect the
bias, such as "The candidate only attended a bootcamp, so this project lacks the
rigor of a formal CS education". This issue is related to the `detect_bias`
function in the `bias_detector.py` in the `safety` directory. 9 test cases in
`tests/unit/test_bias_detector.py` failed as well.

**Branch name:** `fix/151-bias-detector-patterns-too-narrow-to-match-common-phrasings`

**Setup confirmation:** [x] App runs locally at localhost:5173

**Cohort ledger:** [x] Issue added to cohort ledger

**Work Progress**
Was able to reproduce the issue by running the following command: 
(.venv) cxu@MacBookAir pathreview % python3 -c "                                                                               
from safety.bias_detector import BiasDetector
print(BiasDetector.detect_bias('The candidate only attended a bootcamp, so this project lacks the rigor of a formal CS education'))
"
(False, '')
- not much risks because - Looks like only the test_bias_detector and the actual bias_detector will be impacted - no other files will be impacted

Test Cases:
Phrases with similar meaning don't get detected, it has to be nearly the exact phrase

(.venv) cxu@MacBookAir pathreview % cd /Users/cxu/PycharmProjects/AI_Projects/pathreview
./.venv/bin/python -m pytest tests/unit/test_bias_detector.py -v

The 9 failing tests, grouped by what breaks
Test	Input	Why the current regex misses it
test_dismissive_bootcamp_language_detected	"bootcamp graduates can't write production code"	patterns only allow lack/missing, not can't
test_coding_bootcamp_variant	"coding bootcamp graduates can't write..."	same — no can't verb
test_developer_vs_programmer_distinction	"bootcamp developers can't...", "bootcamp programmers lack..."	developers not in the subject group; programmers not listed at all
test_bootcamp_lacks_rigor_detected	"bootcamp education lacks fundamentals"	pattern requires is insufficient/lacks — the word is is mandatory
test_negative_educational_claim	"self-taught developers are not equal to university graduates"	pattern requires self-taught IS not equal — no room for developers ... are
test_assumption_vs_observation	"bootcamp attendance means inadequate training"	no pattern for means inadequate
test_demographic_assumption_age_detected	"young developers can't..."	developer is singular-only; also reason must contain "demographic"
test_rich_poor_assumption	"developers from poor backgrounds can't afford..."	pattern is person from poor, doesn't allow developers from poor
test_multiple_bias_indicators	combines the above	fails because its parts fail

Solution Plan:
Change the design a little bit
It's doing the exact pettern check to to see if the word sequence contains any keywords in the pattern phrase, not an efficient approach to do it
- Switch design decision - I'd create a list of subjects and negative words from the failing tests so that by using the two lists, it helps to detect a subject signal with a negative predicate. 

First List: Subjects - Ex. bootcamp, self-taught, online courses, etc.
second List: Negative Predicates - Ex. cannot, lack/missing/insufficient, etc.

Then use the failed tests as guardrails so they don't create false positives - meaning make sure the nagative phrase do get flagged


## Week 8 — Reproduction & solution planning

**Reproduction commit link:** [JOURNAL.md](JOURNAL.md)

**Reproduction summary:**
[1–2 sentences: How did you reproduce the issue? What did you observe?]
I was able to reproduce the issue running the following command: python3 -c "from safety.bias_detector import BiasDetector
print(BiasDetector.detect_bias('The candidate only attended a bootcamp, so this project lacks the rigor of a formal CS education'))". I noticed when the phrase "The candidate only attended a bootcamp, so this project lacks the rigor of a formal CS education" is passed into the function, the bias detector is not able to detect the bias, and return "False" as the result. Only when an explicit exact phrase is passed into the function, such as "bootcamp graduates lack rigor", then the bias detector is able to detect the bias by returning a warning with a reason "Dismissive language about educational background".

**PLAN.md link:** [link to PLAN.md in your fork]

**Walkthrough video (recommended):** [link to your Loom video, ≤2 min — recommended, not graded]

**Blockers or open questions:**
[Anything you're still uncertain about going into Week 9, or leave blank]



