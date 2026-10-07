# Procedure: how this skill grades a plan package

<!--
THIS IS THE PART YOU WRITE, and it is a new kind of part. Weeks 1 and
2, SKILL.md carried a numbered workflow and you only wrote judgment
files. This week the workflow is gone from the frame: SKILL.md says
"execute procedure.md", and these are the operating steps you author.
The machinery is in your hands now.

Your operator swap is the design brief. When your executor stalled
because your rubric said WHAT to decide but not HOW to find the
evidence, that was a procedure gap. This file is where those gaps get
closed: a complete procedure lets someone who has never seen a plan
package before (a groupmate, or the skill itself) grade one exactly the
way you would.

Under each stage heading below, write the concrete steps for that
stage. The one-line note under each heading says what a complete
procedure must decide there. Write steps, not intentions: "read the
repro evidence before the plan, and note what behavior it pins down"
is a step; "understand the context" is a wish.
-->

## Read order

1. Read the repro evidence first (environment, steps, expected vs. actual). This is the fixed ground truth every other check is read against.
2. Read the issue body, the repo facts block (including the contribution policy), and the thread highlights next. These are needed for `comms` and inform `honesty`.
3. Read the candidate plan (its stated cause, change, and test — whatever headers it actually uses).
4. Read the candidate plan comment last, with the repo facts and thread already in mind.

## Evidence gathering

1. For `diagnosis`: find the plan's stated cause under any header meaning cause/diagnosis/root cause (allow header-wording leeway — "Root Cause" counts the same as "Diagnosis" or "Cause"). Record it, then compare directly against the repro evidence's steps and expected-vs-actual.
2. For `scope` and `executability`: find the plan's stated change or approach (headers like "Change," "Scope," "Approach," or an "In:"/"Out:" pair). Record what is named as in-scope (specific files or areas) and what is named as out-of-scope or explicitly untouched.
3. For `test` and `auto-tests`: find the plan's stated test plan (headers like "Test," "Test(s)," "Test plan"). Record whether it gives concrete steps, whether it states a before/after expectation, and whether it mentions an automated test.
4. For `honesty`: re-read the cause, change, and test sections already gathered. Note any claim that goes further than what the repro evidence section actually shows, and note any explicit statement of risk or uncertainty.
5. For `comms`: read the contribution policy and thread highlights already gathered from the repo facts and issue sections. Read the candidate plan comment. Note whether the comment responds to anything the contribution policy or thread raises, or ignores it.

## Check execution

1. Grade each check P (pass), F (fail), or ? (uncertain), using only the evidence gathered for that check.
2. Output "pass" only if every condition in that check's pass condition is clearly met.
3. Output "fail" if any condition is clearly not met.
4. Output "?" only when the package's own wording is genuinely ambiguous about whether a condition holds — not merely because the answer took effort to find.
5. A check does not require re-reading the whole package if its evidence was already recorded during evidence gathering; grade from those notes.

## Verdict assembly

1. Apply the verdict rule: ready only if diagnosis, scope, executability, test, honesty, and comms all grade pass; hold otherwise.
2. Preferred checks (auto-tests) are reported in the output but never change the verdict.
3. A "?" on any required check counts as a fail for the verdict rule.
4. If the verdict is hold, name which required check(s) caused it and quote the specific evidence that decided each one. If the verdict is ready, quote the strongest supporting evidence for at least the diagnosis and comms checks.
