# Evidence guide: where proof lives in a reproduction package

<!--
THIS IS THE PART YOU WRITE (new this week: Unit 1 handed you this file
finished; the scaffolding fades). The skill uses this guide as its map:
for every kind of proof a rubric check names, this file says WHERE to
find it in a package and WHAT GOOD LOOKS LIKE when you do.

Under each family heading below, write:

- Where it lives: the exact places to look. In an eval bundle (which
  section of the package: the issue context, the repo-facts block, the
  claim comment, the repro report and its parts). In live mode (where
  on GitHub or in the draft: the issue thread, the repo's docs, the
  student's draft comment).
- What good looks like: one or two sentences someone else could apply.
  Prefer observable conditions ("the versions named match what the
  issue targets, or the difference is called out") over adjectives
  ("environment is thorough").

A rubric check whose evidence this guide cannot locate is a check
nobody else can execute; the rubric swap showed you what that feels
like. Write the map you wish your grader had.
-->

## Environment

<!-- Where the environment record lives, and what a sufficient one
looks like against the issue's stated target. -->

**Where it lives:** The `Environment:` line at the top of the repro report. Compare it with the issue's own stated version/environment line.

**What good looks like:** It names the version and OS the repo's bug template asks for, and matches the issue's version or says why it doesn't.

## Steps

<!-- Where the reproduction steps live, and what makes them followable
by a stranger, starting state to trigger. -->

**Where it lives:** The repro report's Steps section — the ordered list of commands from a stated starting point (for example, "fresh clone").

**What good looks like:** Someone else could follow them without guessing: each step is a concrete, runnable command, in order, starting from a state they can actually reach themselves. "Run the command" with no command named does not count.

## Behavior shown

<!-- Where the artifacts live (output excerpts, logs, screenshots),
and what it means for an artifact to show the issue's behavior rather
than an adjacent one. -->

**Where it lives:** The artifact block in the repro report (pasted output or log excerpt), plus the report's own expected-vs-actual lines.

**What good looks like:** The artifact shows the issue's specific symptom (matching error type, exit code, or message), not a different failure that happens to also occur. The report states expected vs. actual explicitly rather than leaving the reader to infer it.

## Honesty

<!-- Where claims and their backing meet: how to tell a report that
says exactly what happened (including an honest cannot-reproduce) from
one that claims more than its evidence shows. -->

**Where it lives:** The gap between the repro report's stated conclusion (reproduced / could not reproduce) and what its own artifact actually demonstrates.

**What good looks like:** The report's confidence matches its evidence. It says exactly what happened, including an honest "could not reproduce" when that's true, rather than asserting success the artifact doesn't back up.

## Comms

<!-- Where the words meet the repo: the claim comment against the
issue, the comments against the repo's stated templates and
contribution policy (including AI-use disclosure requirements), and
what specific-and-honest looks like next to boilerplate. -->

**Where it lives:** The claim comment and repro comment, read against the issue thread and the repo's `CONTRIBUTING.md` / AI-use disclosure line or templates.

**What good looks like:** Specific and honest, not boilerplate: names the version and behavior instead of "this bug," makes no promised timeline, discloses AI assistance when the repo's policy asks for it, and reads like something a person would actually type.
