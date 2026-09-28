# Rubric: is this a good first issue?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| maintainer-responds | The "maintainer first-response sample" list of 5 recently-updated issues, and the "last 5 default-branch commits" under Repo facts | At least 2 of the 5 sampled issues show a first maintainer response of 30 days or less, OR at least one of the last 5 default-branch commits is a merged pull request dated within the last 30 days. Entries reading "no maintainer comment in thread" do not count toward the issue-response side. Subject to Note D in live mode | required |
| repo-is-active | "last 5 default-branch commits" and the `archived:` flag on the repo line | The most recent commit is under 60 days old AND the repo is not archived | required |
| repo-is-in-use | "latest release" and the star count on the repo line under Repo facts | The star count is not negligible (double digits or higher), OR a "Used by" counter is present (live mode). A repo that has never published a release is not disqualified by that alone — many actively used projects skip formal releases; a recent release is supporting evidence, not a requirement. Subject to Note D in live mode | required |
| issue-has-spec | The issue body/thread, the opener's role, and its labels | The issue includes a written spec, acceptance criteria, or otherwise concrete description of the expected change. A terse body still counts if it names specific, concrete items AND the issue was opened by a maintainer/collaborator or carries a "good first issue" label. A vague one-line body with neither of those signals does not count | required |
| issue-is-doable | The issue labels, the issue body, and the comment thread | No disqualifier in Note A applies, AND at least one of: a "good first issue" label; the whole task is documentation; the fix touches 1-3 files with no architectural change; OR the issue describes one specific, narrow, reproducible symptom — even if the reporter lists multiple possible causes or several candidate implementation approaches for that single symptom — with no genuine unresolved design debate and no separate deliverables meant to become their own issues, even without a stated file count or label | required |
| nobody-has-it | "assignees" and "linked PRs" in the repo-facts block, AND every comment in the thread | Assignee is none AND no linked PR is open AND no counting claim comment exists (Note B) AND no maintainer directed the work at a named person | required |
| ai-work-allowed | The "contribution policy" line in the repo-facts block | Fails only on an outright ban on AI-generated or AI-assisted contributions. Conditions and silence both pass (Note C) | required |
| issue-is-fresh | The issue's open date, measured against the recency baseline below | The issue was opened within the last 365 days and has no closed, unmerged PRs in its history | preferred |

## Verdict rule

Accept if every **required** check passes. Reject if any required check fails. If there is not enough information to decide a required check, treat it as a fail and reject the issue.

Preferred checks never change the verdict. Report their grades, and use them to rank issues that were already accepted.

## Note A — scope disqualifiers

Fail `issue-is-doable` regardless of any label if the issue is an umbrella or tracking issue (a list of sub-items meant to be split up), if the thread shows the design is still being debated with no maintainer decision, if a maintainer says the fix touches core internals, or if it is a usage or support question rather than a contribution.

A bug report that names multiple possible causes or several candidate implementation approaches for one symptom is **not** an umbrella issue — it's still bounded, single-symptom work. An umbrella issue lists multiple separate deliverables meant to become their own issues, not alternate explanations or techniques aimed at the same problem.

A terse body, a bare acceptance-criteria checklist, or a bug report with no reproduction steps is **not** a disqualifier for `issue-is-doable` — grade the size of the work being asked for, not the polish of the writeup. `issue-has-spec` still requires the spec separately; the two checks measure different things.

## Note B — whose claims count

By default every claim comment counts. In live mode, apply any house rule in `scope.md` about whose claims count; in eval mode there is no scope, so all claims count.

Two clauses are never narrowed by a house rule: the assignee field and the maintainer-direction clause. A closed, unmerged PR is an abandoned attempt rather than a claim, and does not fail the check.

A claim comment with no follow-up activity afterward — for example, a stale-bot notice with no resulting PR, or the thread going quiet for a year or more — is an abandoned claim rather than a current one, and does not fail the check, the same way an abandoned PR doesn't.

A repeated pattern of claim-and-unassign cycles by multiple different people, especially alongside more than one closed-but-unmerged PR, is different from a single abandoned claim — it signals a known-difficult or contested issue, and does fail the check, even though any one cycle in isolation would look like ordinary abandonment.

## Note C — conditions are not bans

Requirements to disclose AI use, to test the change, to personally understand it, or to have a human review it are terms to follow, not reasons to reject the issue.

Silence passes. A missing or empty contribution-policy line is a **pass**, not unclear — most repositories state nothing, and the verdict rule's unclear-means-fail would otherwise reject nearly every candidate.

## Note D — repo-level house rules (live mode)

In live mode, apply any repo-level house rule stated in `scope.md` to `maintainer-responds` and `repo-is-in-use` when grading a scoped repo — for example, a seeded classroom repo where staff commit directly rather than merge PRs, or where a low star count and no release are expected because the repo was never meant for public adoption. In eval mode there is no scope, so both checks are read at face value from the bundle, unchanged.

## Recency baseline

All day counts in this rubric are measured against the repo-facts block's stated capture date in eval mode, and against today's date in live mode.