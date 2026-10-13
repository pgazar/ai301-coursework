# Procedure: how this tool grades a PR package

<!--
THIS IS THE PART YOU WRITE (second week running for the procedure).
Week 3 you wrote these steps for a plan package; this week the graded
object is a PR package, and the read that matters most is a
side-by-side: the diff against the plan, the evidence against the test
plan, the description against both. Your week-3 procedure is the
pattern; do not paste it unchanged, because its read order was built
for a different object.

Your rotation is the design brief again, and this week friction routes
three ways: a stall on WHAT to decide is a rubric gap, a stall on
WHERE to look is a procedure gap (this file), and a stall on what the
tool even reads or outputs is a frame gap (your SKILL.md). A complete
procedure lets someone who has never seen a PR package before grade
one exactly the way you would.

Under each stage heading below, write the concrete steps for that
stage. The one-line note under each heading says what a complete
procedure must decide there. Write steps, not intentions: "read the
plan's scope pair before opening the diff, and list the files the plan
names" is a step; "understand the change" is a wish.
-->

## Read order

1. Read the plan first (`plan.md`, including any `## Deviations` notes, or the bundle's Plan context). This is the fixed reference everything else is compared against.
2. Read the issue and repo facts next — the thread, the repo's PR template, and its stated contribution/AI-use policy. These are needed for `standards-comms` and inform `diff-quality`.
3. Read the full diff (`git diff main...HEAD`, or the bundle's Diff section). List every changed file as you go.
4. Read the test evidence (`test_evidence.md`, or the bundle's Test evidence block) next, with the plan's stated test plan already in mind.
5. Read the PR title and description last of all, with everything above already gathered — the description is judged against all of it, not read in isolation.

## Evidence gathering

1. For `plan-fidelity`: list every file in the diff's changed-files stat; list every file or area named in the plan's Files/Scope section and any Deviations; compare the two lists directly, noting any diff file not covered by either. Separately, list every factual claim the description makes about what the change does ("exactly as planned," "no options added," etc.) and check each against the diff's actual content.
2. For `test-evidence`: list every test or check claim in the description or `test_evidence.md`; for each, find the matching pasted command-and-output pair. Record "found" or "missing" for each — and when missing, record whether it's honestly disclosed as skipped or failing, versus simply absent with no mention.
3. For `diff-quality`: scan the full diff for debris (debug prints, commented-out code) and for any hunk in a file not already covered by `plan-fidelity`'s file list. Record each finding with its file and line.
4. For `standards-comms`: list every section the repo's PR template requires; check the description for real, specific content under each (not boilerplate). Check whether an issue reference ("Closes #N" or equivalent) is present. Check the repo's stated AI-use disclosure policy and whether the description addresses it.

## Check execution

1. Grade `plan-fidelity`, `test-evidence`, `diff-quality`, and `standards-comms`, in that order, each using only the evidence gathered for it.
2. Output "pass" only if every condition in that check's pass condition is clearly met.
3. Output "fail" if any condition is clearly not met.
4. Output "unclear" only when the package's own text is genuinely ambiguous about whether a condition holds — not merely because confirming it took effort to find.
5. A check does not require re-reading the whole package if its evidence was already recorded during gathering; grade from those notes.

## Verdict assembly

1. Apply the verdict rule: accept only if all four required checks pass; reject otherwise.
2. An "unclear" grade on any required check counts as a fail for this rule.
3. When more than one check failed, quote the first failing check in rubric order (`plan-fidelity`, `test-evidence`, `diff-quality`, `standards-comms`) as the deciding evidence in the output — but still report every check's own grade and evidence line, not only the deciding one.
4. If the verdict is accept, quote the strongest supporting evidence for at least `plan-fidelity` and `standards-comms`.
