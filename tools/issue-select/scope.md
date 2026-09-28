# Scope: where to look, and who is looking

<!--
This file is the skill's field of view. The rubric (rubric.md) decides
whether an issue is GOOD; the scope decides which issues are candidates
at all, and whose hands the issue would land in. It applies in live mode
only: in eval mode the bundle is the whole world and this file is
ignored.

Two parts. Staff wrote the first; you write the second.
-->

## Where candidates come from

Only issues in the course's Path Review repository are candidates:

- Repo: `codepath/pathreview-ai301-fa26-s1`
Do not search, fetch, or grade issues from any other repository, however
promising. The wider GitHub comes later in the course; for now the field
is Path Review.

**Path Review house rule.** Path Review is a classroom, and your
classmates are not strangers. Ignore the usual claim signals here: other
students' claim comments (and there may be several on one issue) do not
block an issue, and finding some on the issue you want is normal. Claim
anyway: course credit attaches to the pull request you open, not to
whether it merges, so a shared issue costs nobody anything. Everything
else in the rubric applies as written.

**Path Review repo-level house rule.** This repo is created and
maintained by the course for the class's own use, not an organic public
project. Two repo-level checks read it differently here:

- `maintainer-responds`: course staff maintain this repo by direct
  commits, not a pull-request review cycle. Absence of merged PRs or
  issue comments doesn't mean the maintainer is unresponsive — direct
  recent commits are the evidence to look at instead.
- `repo-is-in-use`: a near-zero star count and no published release are
  expected for a seeded classroom repo never meant for public adoption.
  Recent commit activity and an open issue queue built for students
  satisfy this check here, without needing stars or a release.

Everything else in the rubric applies as written.

## Your fit profile

<!-- YOU write this part: a few sentences about you. What languages and
tools you have actually used, what you want to get better at, anything
you want to avoid. The skill uses this only to RANK the issues your
rubric accepts, never to change a verdict: fit cannot rescue an issue
your rubric rejects, and cannot sink one it accepts. -->

Python is my language. I am comfortable reading and writing it, and it is
what I want to get better at, so rank Python issues above issues in other
languages. I have no tooling preference: any build system, test runner,
linter, or framework is fine, and an unfamiliar tool is not a reason to
rank an issue lower.

On size, I want issues that touch fewer than three files and sit between
simple and average in difficulty for a newcomer. Among accepted issues,
rank the smaller and more self-contained ones first. A one-file fix beats
a three-file one; a change whose test lives next to the code it changes
beats one that needs a new fixture or a new test file.

Rank lowest, without rejecting: issues in languages other than Python,
and issues that pass the rubric but sit at the upper edge of its size
limit.
