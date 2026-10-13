# Evidence guide: where evidence lives in a PR package

<!--
THIS IS THE PART YOU WRITE (third week running: the map stays in your
hands). Your tool uses this guide as its map: for every kind of
evidence a rubric check names, this file says WHERE to find it in a PR
package and WHAT GOOD LOOKS LIKE when you do.

The four families below are the harness's failure categories under
the names the eval README uses: plan fidelity = silent-drift, test
evidence = not-tested, diff quality = unreviewable, standards and
comms = standards-wall. A package that fails none of them is a
clear-accept. Under each family heading below, write:

- Where it lives: the exact places to look. In an eval bundle (which
  section of the package: the plan-context block's scope pair and test
  plan, the candidate PR's diff, commits, description, or
  test-evidence section, the repo-facts block's template asks and
  stated policy). In live mode (where in your working copy and on
  GitHub: your plan.md and its deviation notes, your branch's diff,
  your draft title and description, your captured test output, the
  repo's PR template and CONTRIBUTING.md).
- What good looks like: one or two sentences someone else could apply.
  Prefer observable conditions ("every changed file falls inside the
  plan's stated boundary or a deviation note") over adjectives ("the
  diff is clean").

A rubric check whose evidence this guide cannot locate is a check
nobody else can execute, and this week that cuts three ways: your
procedure says WHEN to gather each family, this guide says WHERE, and
your SKILL.md says the tool reads both. Write the map you wish your
executor had.
-->

## Plan fidelity (harness category: silent-drift)

<!-- Where the plan states its scope, boundary, and deviation notes,
and where the diff shows what actually changed. What it means for a
diff to match the plan, for an honest deviation to re-tie a mismatch,
and what silent drift looks like in each direction (more than the
plan, or less with no note). The description's fidelity claims read
against the diff live here too: a description claiming more or less
than the diff delivers is silent drift, not a comms problem. -->

**Where it lives:** Eval bundle: the Plan context block's stated Files/Scope, read against the candidate PR's Diff section's changed files. Live: `plan.md`'s Files/Scope and any `## Deviations` notes, read against `git diff main...HEAD --stat`. The description's own fidelity claims, read against the diff's actual content, belong here too — not under comms.

**What good looks like:** Every changed file falls inside the plan's stated files, or a disclosed deviation. Example (calib-03): the plan names `src/language-js/print/statement.js` and a changelog entry, both present in the diff — those pass. But the diff also touches `src/language-js/print/property.js`, which the plan never named — that's silent drift. The description's claim "Implements the plan on #19727 exactly, with no changes beyond it" is directly contradicted by that extra file, making the overclaiming description a second instance of the same drift, not a separate problem.

## Test evidence (harness category: not-tested)

<!-- Where the PR shows its proof: the test-evidence section's
before/after against the plan's test plan and the reproduction's own
steps, and the outcome of the repo's own checks or suite. What
decisive looks like (an observable behavior named, the expected-after
stated, the checks' outcome visible) next to "tests pass". -->

**Where it lives:** Eval bundle: the candidate PR's Test evidence section, read against the Plan context's stated test plan. Live: `test_evidence.md` or the PR description's Testing section, read against `plan.md`'s test plan.

**What good looks like:** A decisive claim names an observable command AND shows its real printed output, or gives a specific, quantified result (exact pass/fail/skip counts, an exact error message) — not a vague sentence asserting success with no specifics. Example (calib-03): "Issue input re-run on the branch: `$ ./bin/prettier.js --parser babel repro.js` `# after: matches the issue's expected output byte for byte`" shows a command, but the actual output isn't pasted and the claim needs byte-level proof — only a `#` comment describing what it allegedly showed. That's not-tested. By contrast, a full suite run reported as "1462 passed, 12 skipped" is specific and falsifiable even without a literal terminal block, and does count — the bar is specificity, not strict format.

## Diff quality (harness category: unreviewable)

<!-- Where the change itself lives: the unified diff and the commit
list. What a reviewable change looks like (the fix visible, nothing
unrelated riding along) and the debris tells: debug leftovers, dead
code, commented-out blocks, formatting churn, drive-by edits. -->

**Where it lives:** Eval bundle: the Diff section itself. Live: `git diff main...HEAD`.

**What good looks like:** Every hunk is either the fix itself or a test/changelog entry the plan called for; nothing unrelated rides along. Example (calib-03): the touch to `property.js` isn't only a plan-fidelity problem — it's also an unexplained, unrelated hunk sitting in a diff that's supposed to be about the if-else printer. That's debris this family catches independent of whether it was ever disclosed: an unrelated change stays unreviewable even if the description mentions it.

## Standards and comms (harness category: standards-wall)

<!-- Where the repo states its asks (the repo-facts block's PR
template sections, contributing instructions, and stated policy,
including AI-use disclosure) and where the PR honors them: the
stated sections filled with real content, the disclosure present,
explicit maintainer direction in the thread engaged. What compliant
looks like next to boilerplate or a visibly ignored ask. (Whether
the description's claims match the diff is plan fidelity, above.) -->

**Where it lives:** Eval bundle: the repo-facts block's PR template sections and stated policy. Live: the repo's `.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md`, read against the PR description.

**What good looks like:** Every template section has real, specific content, not boilerplate; disclosure is addressed if the policy asks for it. Example (calib-03): the description fills in a Checklist section with specific, concrete claims (tests added, changelog added) — that's standards-comms behaving correctly on its own axis, the template's sections are genuinely filled, separate from whether those specific claims hold up under proof, which is test-evidence's job.
