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





