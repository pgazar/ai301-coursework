# Unit 1 — Issue Selection

## Chosen issue

Link: https://github.com/codepath/pathreview-ai301-fa26-s1/issues/69

Verdict output (from live-mode run):

```json
{
  "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/69",
  "checks": [
    {"name": "maintainer-responds", "grade": "pass", "evidence": "Note D house rule: 3 of last 5 default-branch commits are direct maintainer commits by Andrew Burke dated 2026-09-16, 11 days before today"},
    {"name": "repo-is-active", "grade": "pass", "evidence": "most recent commit 2026-09-16 (11 days old, < 60) and repo line shows isArchived: false"},
    {"name": "repo-is-in-use", "grade": "pass", "evidence": "Note D house rule: 3 stars and no release are expected for a seeded classroom repo; recent commits plus an open student issue queue (through #73) satisfy it"},
    {"name": "issue-has-spec", "grade": "pass", "evidence": "body names the exact error, both relevant files, the xfail test to unmark (manifest H-02) and a 2-4 hour effort estimate; opened by maintainer Aburke225 with 'good first issue'"},
    {"name": "issue-is-doable", "grade": "pass", "evidence": "'good first issue' label; one symptom, two named files, no umbrella list, no unresolved design debate, no core-internals statement"},
    {"name": "nobody-has-it", "grade": "pass", "evidence": "assignees empty; the only cross-referenced PR is charancherry0/ai301-coursework#1, a merged coursework write-up in another repo, not a fix; claims by jacho15/9dsn/wazimmerman are all author_association NONE, ignored per Path Review house rule"},
    {"name": "ai-work-allowed", "grade": "pass", "evidence": "docs/CONTRIBUTING.md contains no AI-contribution ban; silence passes per Note C"},
    {"name": "issue-is-fresh", "grade": "pass", "evidence": "opened 2026-09-10 (17 days ago) with no closed, unmerged PRs in its history"}
  ],
  "verdict": "accept"
}
```

## Selection rationale

All three candidates I ran (#53, #56, #69) were accepted once the rubric correctly read this repo as a seeded classroom repo rather than public OSS (see Run history, item 6-7). My skill's own fit-ranking placed **#69** (output parser crashes on a top-level JSON array) first: it names exactly two files (`rag/generator/output_parser.py`, `tests/unit/test_output_parser.py`), the test already lives beside the code, and the fix is fully bounded — guard two `json.loads` call sites and drop one xfail marker. #53 initially looked like the smaller edit, but the live run's evidence surfaced something neither of us knew ahead of time: its thread shows a fifth test entangled with an unrelated defect (street-address over-matching), adding real complexity #69 doesn't have. #56 was accepted too but sits at the upper edge of my size preference. I'm going with #69.

## Reflection

### Run history

1. Initial rubric draft (template checks plus a custom `scope-fits-me` check requiring Python language and a written spec). First `--limit 3` run failed issue-01 on `scope-fits-me`: the check cited a `language:` field under Repo facts that turned out not to exist in any bundle. Fixed by dropping the language condition (already correctly covered as a ranking-only preference in `scope.md`) and keeping only the spec requirement, renamed to `issue-has-spec`.
2. `--limit 5` run surfaced issue-04 failing `issue-has-spec`: a terse but collaborator-opened, "good first issue"-labeled report. Loosened the check to accept a terse body when it names concrete items and carries a maintainer/collaborator or label signal, matching the course's own `evidence-guide.md` guidance that terse bodies aren't automatically disqualifying.
3. First full 20-issue run: 16/20. Every category but `clear-accept` (4/8) was at 100%, so all four misses (issue-06, 09, 11, 19) were real gaps, not arguable calls. Fixed three checks: `repo-is-in-use` (dropped a hard release-recency requirement that penalized small, active, release-less projects), `nobody-has-it`/Note B (added a carve-out for one old, abandoned claim comment, as opposed to a currently claimed issue), and `issue-is-doable`/Note A (a bounded single-symptom bug report can pass without a "good first issue" label, and multiple candidate causes/approaches for one symptom isn't the same as an umbrella issue).
4. `--only` re-run on the four misses: 3 of 4 flipped. issue-19 still failed — its "two potential causes" and numbered "additional suggestions" were being read as separate deliverables. Refined Note A/`issue-is-doable` to explicitly distinguish "multiple causes/approaches for one symptom" from a true umbrella issue. Re-ran issue-19 alone: fixed.
5. Confirming full run: 18/20, PASS. The only two misses (issue-01, issue-04) were confirmed via isolated `--only` re-runs to be pure grading variance on a genuinely borderline case, not rubric gaps.
6. Live-mode run on real Path Review candidates surfaced a new, real gap: two repo-level checks (`maintainer-responds`, `repo-is-in-use`) are calibrated for public OSS and reject every candidate in a seeded classroom repo, regardless of issue quality. Added a `scope.md` house rule plus a new `rubric.md` note (Note D) linking those two checks to the house rule in live mode only — eval mode explicitly unaffected.
7. Final confirming full run after Note D: 19/20, PASS (same eval-mode grading held). `eval-run.txt` regenerated to match the current `rubric.md`.
### Issue analysis

Scored issue: **issue-04** (zxcalc/zxlive#555, "Missing several basic rule previews"). Gold label: accept. My rubric's confirming run graded it: reject, on `issue-is-doable`.

The issue body is terse ("Including remove identity, fuse spiders, remove self loops, etc.") but was opened by a COLLABORATOR and carries a "good first issue" label — signals my `issue-is-doable` check is designed to credit even without a formal spec. In most runs (including an isolated re-check), my rubric correctly accepts it. In this particular confirming run, the grader read the short list of named rule types as closer to several separate small deliverables rather than one cohesive addition, and rejected it. Since the underlying check logic is sound and the disagreement doesn't reproduce consistently, I read this as LLM grading variance at a genuinely fine line, not a rubric design flaw — the same conclusion I reached when this exact issue first surfaced the need to loosen `issue-has-spec` for terse-but-labeled reports.

### Check rationale

Quoting `issue-has-spec` as it currently reads in my uploaded `rubric.md`:

> "The issue includes a written spec, acceptance criteria, or otherwise concrete description of the expected change. A terse body still counts if it names specific, concrete items AND the issue was opened by a maintainer/collaborator or carries a "good first issue" label. A vague one-line body with neither of those signals does not count"

This check exists because my personal preference (wanting a written spec before committing to an issue) initially clashed with the course's own philosophy in `evidence-guide.md`: a terse body isn't automatically a red flag if it comes from someone credible or is pre-vetted with a "good first issue" label. The wording above is the compromise — it still requires something concrete, but no longer requires formal spec formatting.

### Trade-offs

Loosening `issue-is-doable`/Note A to accept a bug report listing multiple possible causes or implementation approaches (as long as they serve one symptom) makes the rubric more permissive at the boundary between "one bounded task" and "an umbrella issue." That's the right call for catching genuinely small, well-reasoned bug reports like issue-19 — but it means the rubric leans on a judgment call an LLM grader won't apply with perfect consistency, as the issue-01/issue-04 noise in the final run shows. I chose not to chase full determinism here, since tightening the wording further to eliminate that noise risks reintroducing the original false-reject problem on genuinely fine issues.