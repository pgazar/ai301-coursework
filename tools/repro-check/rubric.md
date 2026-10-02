# Rubric: is this reproduction package ready to post?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever
checks you define here. It ships empty on purpose: the judgment is your
work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four
   columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where in the package. Name
     the part (the claim comment, the repro report's environment
     record, the artifacts read against the issue's description, the
     repo-facts block) or a location from your
     references/evidence-guide.md. "The report" is not a source; "the
     output excerpt read against the error the issue describes" is.
   - Pass condition: a decision rule about the OUTCOME that someone
     else could apply and get your answer. Judge the thing itself (does
     the artifact show the issue's behavior?), never the write-up's
     shape (how many steps it has, how long it is, whether it uses a
     template's headings). Structure-shaped checks are what make
     graders disagree with themselves.
   - Weight: `required` (a fail here holds the package) or `preferred`
     (never changes the verdict).

2. A verdict rule below the table: how the check grades combine into
   accept (ready) or reject (hold), including how `unclear` is
   treated. The verdict space is binary. If you write no rule for
   `unclear`, the skill treats it as fail.

Cover what actually gets bad packages posted. The lecture named the
proof families: the environment is recorded, the steps are complete
and followable, the behavior shown matches the issue (not an adjacent
one), the outcome is stated honestly (an evidenced cannot-reproduce is
a pass, a confident wrong-target is not), and the words respect the
repo's conventions. A rubric that ignores a family will fail eval
packages designed around that family.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| env-recorded | The repro report's `Environment:` line, compared against the issue's own stated version/environment | Names the tool version and OS the issue's repo asks for (per its bug template), and either matches the issue's target version or explicitly says why it doesn't | required |
| steps-followable | The repro report's Steps section | A stranger could re-run them without guessing: starts from a stated, reachable starting point (e.g. "fresh clone"), gives concrete commands in order. A step that says "run the command" without naming the command does not count | required |
| behavior-matches | The artifact (output/log excerpt) in the repro report, read against the issue's stated expected vs. actual behavior | When the report claims to have reproduced the issue's behavior, the artifact must show the *same* failure the issue describes (same error type, exit code, or message) — not a different, adjacent failure that happens to also be broken. When the report honestly concludes it could not reproduce the issue, backed by a genuine, detailed attempt, this check passes without needing a matching artifact; that scenario is graded by `honesty` instead, which checks the cannot-reproduce conclusion is itself backed by real evidence of the attempt | required |
| honesty | The repro report's stated conclusion, read against what its own artifact actually shows | The report claims only what its evidence supports. An honest, evidenced "could not reproduce" is a pass. A confident claim of reproduction whose artifact shows a different failure, or asserts behavior no artifact shows, is a fail | required |
| comms | The claim comment and repro comment text, read against the repo's stated contribution policy / AI-use disclosure requirement (repo-facts block or CONTRIBUTING.md/AI_POLICY.md), and against the issue itself | First, determine from the repo facts whether the policy requires disclosing AI assistance (a strict policy states this explicitly, e.g. "all AI usage must be disclosed"). If it does, search the full text of both the claim comment and the repro comment for an actual disclosure statement naming that AI was used. If the policy requires disclosure and no such statement appears anywhere in either comment, this check FAILS regardless of how strong the rest of the comment is — this is a hard, standalone failure condition, not one factor among several. If the policy has no disclosure requirement or is silent, grade only the remaining criteria: the comment names the specific version and behavior (not "this bug"), makes no promised timeline, and reads like something a person would actually say | required |

## Verdict rule

Accept (ready) only if every required check that applies passes. Reject (hold) if any applicable required check fails, or if any applicable required check is unclear. In claim-only mode, checks marked "not yet applicable: claim-only draft" are excluded from this rule entirely, per SKILL.md's claim-only workflow — the verdict there answers only whether the claim comment itself is ready.
