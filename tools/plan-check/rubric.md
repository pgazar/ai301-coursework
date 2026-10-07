# Rubric: is this plan ready to post and build from?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever
checks you define here (via your procedure.md). It ships empty on
purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four
   columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where in the package. Name
     the part (the plan's scope statement, the test plan read against
     the repro evidence's steps, the plan comment read against the
     thread highlights, the repo-facts block) or a location from your
     references/evidence-guide.md. "The plan" is not a source; "the
     plan's stated cause read against what the repro evidence shows"
     is.
   - Pass condition: a decision rule about the OUTCOME that someone
     else could apply and get your answer. Judge the thing itself (is
     this one bounded change? could a stranger start executing it?),
     never the write-up's shape (how many sections it has, how long it
     is, whether it uses headings). Structure-shaped checks are what
     make graders disagree with themselves.
   - Weight: `required` (a fail here holds the package) or `preferred`
     (never changes the verdict).

2. A verdict rule below the table: how the check grades combine into
   accept (ready) or reject (hold), including how `unclear` is
   treated. The verdict space is binary. If you write no rule for
   `unclear`, the skill treats it as fail.

Cover what actually gets bad plans posted. The lecture named the
failure families: the diagnosis ignores or contradicts the reproduced
evidence, the change is unbounded (scope creep), the plan targets the
symptom while the evidence points at the cause, a stranger could not
start executing it, the test plan proves nothing observable, the
unknowns are dressed up as certainty, and the comment ignores what the
thread or the repo's stated conventions ask. A rubric that ignores a
family will fail eval packages designed around that family.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| diagnosis | The plan's stated cause (may appear under a header like "Diagnosis," "Cause," or "Root Cause" — read for meaning, not exact header match), read against the repro evidence's steps and observed behavior | The stated cause directly explains the specific behavior the repro evidence shows, and is grounded in that evidence (a behavioral correlation across versions, a control test, a debug trace, or equivalent) — not a plausible-sounding but ungrounded guess, and not a cause that only explains the symptom while the evidence points further upstream. A plan may say the exact code location (specific functions or lines) will be pinned down during implementation without that counting against this check: diagnosis is about whether the causal mechanism is grounded in evidence, not whether the code location is already identified | required |
| scope | The plan's stated change or approach, read for what it says it will touch and what it says it won't | Names the specific file(s) or narrow area it will change, AND explicitly states what it will not touch (an "Out:" line, a "won't touch" statement, or equivalent). A change that touches areas beyond what the diagnosis requires, or states no boundary at all, fails | required |
| executability | The plan's stated approach, read for whether someone unfamiliar with the author's reasoning could start acting on it | Names where the change happens (file or area) and what will be done there, in concrete enough terms that a stranger could start without first asking the author what they meant | required |
| test | The plan's stated test plan | Gives concrete steps (not just "test it") that demonstrably fail before the fix and would pass after it, ideally tied to the repro evidence's own steps | required |
| honesty | The plan's overall confidence, read against what the repro evidence actually establishes; any stated risks or unknowns | The plan doesn't assert more certainty than its evidence supports. A causal mechanism confirmed by the repro evidence (a correlation across versions, a control test, a debug trace) is stated as settled even if the exact code location is still to be pinned during implementation — that sequencing is normal and not a honesty problem. What fails this check is asserting the cause itself with more confidence than the evidence supports, or staying silent about a genuine open question the plan's own text reveals awareness of (an alternate explanation not ruled out, an untested platform or condition) | required |
| comms | The candidate plan comment, read against the repo facts' contribution policy and the thread highlights | If the repo's contribution policy states something bearing on how a contribution should be framed (limited review bandwidth, a disclosure requirement, a stated preference), the comment responds to it rather than ignoring it. If the thread has existing maintainer or commenter input, the comment doesn't contradict or ignore it. A repo with no particular asks and a silent thread passes by default | required |
| auto-tests | The plan's stated test plan | The test plan includes or proposes an automated test suite entry, not only manual repro steps | preferred |

## Verdict rule

Ready if every required check (diagnosis, scope, executability, test, honesty, comms) passes. Hold otherwise, and name the check(s) that caused it. Preferred checks (auto-tests) never change the verdict. Treat "?" as a fail for any required check.
