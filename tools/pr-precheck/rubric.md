# Rubric: is this pull request ready to submit?

<!--
THIS IS THE PART YOU WRITE (fourth week running; this is the rubric's
final form in the sandbox). Your frame in SKILL.md executes whatever
checks you define here, via your procedure.md. It ships empty on
purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four
   columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where in the package. Name
     the part (the diff read against the plan's scope, the test
     evidence read against the plan's test plan, the description read
     against the diff, the repo-facts block's template asks) or a
     location from your references/evidence-guide.md. "The PR" is not
     a source; "the diff's changed files read against the plan's
     stated boundary" is.
   - Pass condition: a decision rule about the OUTCOME that someone
     else could apply and get your answer. Judge the thing itself
     (does the diff fall inside the plan plus its deviation notes? is
     the claimed evidence observable?), never the write-up's shape
     (how long the description is, how many commits there are).
     Structure-shaped checks are what make graders disagree with
     themselves.
   - Weight: `required` (a fail here holds the package) or `preferred`
     (never changes the verdict).

2. A verdict rule below the table: how the check grades combine into
   accept (submit) or reject (hold), including how `unclear` is
   treated. The verdict space is binary. State the `unclear`
   treatment explicitly: the frame here is YOUR SKILL.md, so a rubric
   that stays silent is only covered if your frame's grading
   discipline says what happens (the contract's own default is that
   an unverifiable claim fails).

Cover what actually gets bad PRs submitted. The failure families the
lecture named ARE the harness's scoring categories, same names as the
eval README: silent drift (the diff silently does more or less than
the posted plan, or the description claims fidelity the diff
contradicts), not tested (the evidence proves nothing observable, or
the repo's own checks were never run), unreviewable (debris or
unrelated hunks bury the change), and standards wall (the repo's
stated template and disclosure asks are ignored). Your evidence
guide's four headings map onto these one to one (plan fidelity =
silent drift, test evidence = not tested, diff quality =
unreviewable, standards and comms = standards wall), and the category
floor is scored on exactly these names plus clear accept. A rubric
that ignores a category will fail the eval packages built around
that category. And remember the honest-outcome
rule, fourth week running: a PR that honestly discloses a shortfall
can be ready; a rubric that equates "less than everything" with
"hold" fails the set.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| plan-fidelity | The diff's changed files (`git diff main...HEAD --stat`), read against the plan's stated Files/Scope section and any `## Deviations` notes; the PR description's claims about what the change does, read against the diff's actual content | Every file the diff touches is named in the plan's Files/Scope, or disclosed under `## Deviations` with a reason. The description's claims match what the diff actually shows — a claim of "exactly as planned" or "no changes beyond it" does not survive an undisclosed difference. An honestly disclosed shortfall or deviation (named in both `plan.md`'s Deviations and the description) does not fail this check; an undisclosed one does | required |
| test-evidence | The PR description's/`test_evidence.md`'s Testing section, read for actual commands and their printed output, compared against the plan's stated test plan | Every claim that demonstrates the issue's specific behavior (a before/after repro, a byte- or value-level match) is backed by a pasted command with its real printed output, or a specific, quantified result (exact pass/fail/skip counts, an exact error message) — a vague sentence asserting success with no specifics does not count there. A supplementary claim that the project's broader existing test or lint suite also passes may be stated briefly, without pasted output or counts, as long as the specific repro evidence above is itself rigorous (not every test framework reports counts the way pytest does); if no specific repro evidence exists at all, a bare suite-passes claim does not substitute for it. An honestly reported failing check, shown with its output or specific failure detail and the stated reason, satisfies this check; a check silently skipped or asserted with no specifics anywhere in the package does not | required |
| diff-quality | The full diff (`git diff main...HEAD`), read for debris (debug lines, commented-out code) and for any hunk touching a file or area the issue doesn't need | No debug prints or commented-out code remain in the diff, and every changed file is either required by the plan's diagnosis/scope or disclosed as a deviation the issue's fix genuinely needs. A change unrelated to the issue (e.g. a drive-by cleanup) fails this check even when the description mentions it — disclosure prevents a silent-drift fail elsewhere, but does not make an unrelated change reviewable | required |
| standards-comms | The PR description, read against the repo's PR template (every section) and its stated contribution/AI-use disclosure policy (`docs/CONTRIBUTING.md` or equivalent); the description's issue reference | Every section of the repo's PR template has real, specific content, not placeholder or boilerplate text. The issue is linked (a "Closes #N" or equivalent). If the repo's policy asks for AI-use disclosure, the description discloses it; silence in the repo's policy is a pass on that sub-condition | required |

## Verdict rule

Accept (ready to submit) only if all four required checks pass. Reject (hold) if any required check fails or is unclear. Treat `unclear` as a fail: a PR element that cannot be verified from the package is not ready to submit.
