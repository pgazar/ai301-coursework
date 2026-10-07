# Evidence guide: where evidence lives in a plan package

<!--
THIS IS THE PART YOU WRITE (second week running: the judgment files
stay in your hands). The skill uses this guide as its map: for every
kind of evidence a rubric check names, this file says WHERE to find it
in a plan package and WHAT GOOD LOOKS LIKE when you do.

Under each family heading below, write:

- Where it lives: the exact places to look. In an eval bundle (which
  section of the package: the issue context, the repro-evidence block,
  the candidate plan's scope statement or test plan, the plan comment,
  the repo-facts block). In live mode (where on GitHub or in the
  draft: the issue thread, the student's posted repro comment, the
  repo's docs, the draft plan and comment).
- What good looks like: one or two sentences someone else could apply.
  Prefer observable conditions ("the stated cause cites behavior the
  repro evidence actually shows") over adjectives ("diagnosis is
  solid").

A rubric check whose evidence this guide cannot locate is a check
nobody else can execute, and this week that cuts twice: your
procedure.md tells the skill WHEN to gather each family, and this
guide tells it WHERE. Write the map you wish your executor had.
-->

## Diagnosis and grounding

<!-- Where the plan states its cause, and where the repro evidence
pins down the behavior that cause must explain. What it means for a
diagnosis to follow from the evidence rather than contradict or
ignore it. -->

**Where it lives:** The plan's stated cause — the header varies ("Diagnosis," "Cause," "Root Cause") — read against the Repro evidence section's steps and expected-vs-actual.

**What good looks like:** The cause explains the specific behavior the repro evidence shows, not a generic, plausible-sounding theory. It doesn't stop at a symptom the evidence merely confirms while an upstream cause goes unaddressed. Example: "after a push, that view's model is not refreshed" directly explains why a specific repro step shows a stale state — nothing is left unexplained.

## Scope

<!-- Where the plan bounds itself: the in-scope statement, the
not-in-scope line, the files or areas named. What one bounded change
looks like next to a drive-by rewrite. -->

**Where it lives:** The plan's stated change or approach — often under "Change," "Scope," or an explicit "In:"/"Out:" pair.

**What good looks like:** Names the specific file(s) or narrow area to change, AND explicitly states what it will not touch. Example: "In: the push completion callback in `sync_controller.go`... Out: any change to how push status is computed, or to other views' refresh behavior" — a one-line boundary a stranger can't easily misread.

## Executability

<!-- Where the plan says what will actually be done: files or areas,
approach, order of work. What it means for a stranger to be able to
start executing without asking the author anything. -->

**Where it lives:** The same "Change"/"Approach" section as scope, read for whether the what and where are concrete enough to act on.

**What good looks like:** Names where the change happens and what will be done there in concrete terms — not "fix the refresh logic" but "add the commits context to the post-push refresh scope." A stranger reading it shouldn't need to ask the author which file or what exactly changes.

## Test plan

<!-- Where the plan says how success will be observed, and how that
maps onto the repro evidence's steps and artifacts. What a decisive
test plan names that a vague one does not. -->

**Where it lives:** The plan's stated test section — "Test," "Test(s)," or "Test plan."

**What good looks like:** Concrete steps that demonstrably fail before the fix and pass after, ideally reusing the repro evidence's own steps rather than inventing an unrelated check. Example: "repro steps above; at step 3 the color must flip without leaving the view. Also check... after a force push" — reuses the repro's steps and names exactly what must change.

## Honesty

<!-- Where claims meet uncertainty: risks, unknowns, and deviations.
How to tell stated unknowns from false confidence, and where an
honest mid-build deviation gets recorded. -->

**Where it lives:** The gap between what the cause/change/test sections assert and what the Repro evidence section actually establishes; any explicit risk or unknown the plan names.

**What good looks like:** The plan doesn't claim more than its evidence supports. A cause confirmed by the repro evidence is stated as settled; a cause that's a reasonable guess but not fully confirmed is marked as such, rather than written with the same confidence as a verified one.

## Comms

<!-- Where the words meet the thread and the repo: the plan comment
read against the issue's maintainer signals (thread highlights, or
the live thread) and against the repo-facts block's stated templates,
contributing asks, and contribution policy (including AI-use
disclosure requirements). What thread-aware looks like next to
boilerplate. -->

**Where it lives:** The candidate plan comment, read against the repo facts' contribution policy and the issue's thread highlights.

**What good looks like:** The comment responds to anything the contribution policy or thread actually raises (limited review bandwidth, an AI-disclosure ask, a maintainer's prior suggestion) rather than reading like a template pasted onto any issue. Example: "keeping it minimal given the review-bandwidth note in CONTRIBUTING" directly answers a stated worry about reviewing AI-generated PRs. A repo with no particular asks and a silent thread needs nothing special — silence doesn't require inventing a response.
