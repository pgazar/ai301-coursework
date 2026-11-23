# Contribution: BUG: groupby/resample ohlc raises ValueError for pyarrow numeric dtypes

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** [1]
**Student:** Pegah Zargarian
**GitHub Username:** pgazar
**Issue:** https://github.com/pandas-dev/pandas/issues/71940
**Pull Request:** https://github.com/pandas-dev/pandas/pull/72280

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `[1]` unless you make more than one contribution this term; it
just numbers them.

---

## Toolkit

Every story section below, from Why I Chose This Issue through Pull
Request, ends with a Toolkit block: which tool did that phase's work
and how you used it, what it produced, what difficulties or
shortcomings it presented, and what you changed or would change
because of them. Together the blocks are the thread the rubric's
Toolkit points read, and they are the raw material for your unit-9
portfolio, so keep them current as you work rather than
reconstructing them later.

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

This section also keeps one short dated entry per week of tool work,
starting below: what you built or changed that week, what broke, and
what you changed because of it. The blocks tell the story where it
happened; these entries are the toolkit's own timeline.

All four entries are graded. Keep every heading below, and fill each
one in the week it belongs to.

**Unit 5: Built the issue-scout, ran one scan, and fixed my search strategy (2026-11-06)**

I installed the issue-scout and wrote four `gh search issues` queries from my fit profile (Python first, then data projects: pandas, SQLFluff, Great Expectations), ranked by fewest comments. My first scan rejected all five picks for nobody-has-it. I opened sqlfluff#9026 and saw two linked PRs (#9027, #9035), so the rubric was right, and my strategy was the problem: "fewest comments, newest first" chose brand-new issues whose reporters had already opened PRs. I added `-linked:pr` to every query, which removed all five, but I found it also hides issues with only abandoned PRs (arrow#39149) and misses issues already fixed without a link (beam#19712, fixed by #39079), so next I want a date filter in my queries and a rubric check that the bug still reproduces on main.

**Unit 6: [one line on the tool work]**

[2-4 sentences: the voice-guard, the repo-memory file. What broke or
fell short, and what changed because of it. "Nothing broke" counts when
it is true - say how you know.]

**Unit 7: [one line on the tool work]**

[2-4 sentences: the ship-gate and your gate manifest. What it produced
on your real branch, what fought you, what changed because of it.]

**Unit 8: [one line on the tool work]**

[2-4 sentences: the PR-monitor and your playbook. What it read, what it
drafted, what you did with the draft.]

---

## Why I Chose This Issue

I chose pandas issue #71940 because I use pandas in my data analysis work, and one detail made me curious. The same `groupby().ohlc()` call worked with `Float64` but raised a `ValueError` when I switched to `float64[pyarrow]`. That gave me a concrete question to investigate: could I get the correct open, high, low, and close values while keeping all four columns in the original PyArrow dtype?

I found #71940 through my own GitHub search, outside the Issue Selection site. My scout hadn't graded it, so I read the issue history myself. Two earlier claims had expired, and the bot had closed PR #71975 because of an assignment issue. After checking that history, I claimed the issue before starting the fix. [My claim comment](https://github.com/pandas-dev/pandas/issues/71940#issuecomment-7090063326)

I hope to learn how pandas handles PyArrow data inside `groupby` and what maintainers look for when reviewing a fix from an outside contributor.

**My take on the scout**

All five issues in my first scan already had pull requests, and `beam#19712` showed up as available in my search even though a fix had already landed. Those misses led me to add the `-linked:pr` filter and to reproduce each bug on `main` myself before trusting a verdict. With pandas #72215, the scout found an issue I could reproduce, but someone opened a PR before I claimed it. That experience changed the order I work in.

**Toolkit**

- Tool: I used `issue-scout` to run my GitHub search queries and grade issues I picked from the Issue Selection site.
- What it produced: None of the five issues from my first scan passed. The scout also rejected three site picks and later accepted pandas #72215.
- Difficulties: An open issue could already have a fix, and even a correct verdict about availability could go stale within a day.
- What changed: I added `-linked:pr` to my searches. Now I read the issue history, claim an available issue, and reproduce the bug on `main` before writing a fix.

---

## Understanding the Issue

**Problem Description**

When I ran `ohlc()` after `groupby()` on a `Float64` column, it worked. Changing only the dtype to `float64[pyarrow]` made the same call fail. The issue also affects `int64[pyarrow]` and can occur when calling `ohlc()` after `resample()`.

**Expected Behavior**

I expected one row per group with four columns: `open`, `high`, `low`, and `close`. Each column should keep the input dtype, and a group containing only missing values should have `<NA>` in all four columns. The existing `test_ohlc_ea_dtypes` test checks that pandas preserves the input dtype for other extension types, so I expected the same behavior for PyArrow.

**Current Behavior**

The call stops with `ValueError: Mask must be 1D array` before pandas returns a table. I reproduced the same error on pandas `3.1.1`, on the `3.2.0.dev0` nightly build, and on my own build from source.

**Affected Components**

I traced the call from `GroupBy.ohlc` in `pandas/core/groupby/groupby.py`, which builds the result table, to `_groupby_op` and `_groupby_result_to_arrow` in `pandas/core/arrays/arrow/array.py`. The conversion fails because `ohlc()` produces a two-dimensional result with four values per group, while an Arrow array is one-dimensional. The error surfaces in `__arrow_array__` in `pandas/core/arrays/masked.py`.

**Toolkit**

- Tool: I read the traceback in my terminal, used claude.ai to help me understand the call sequence, and used `gh` to read the issue history and the closed PR #71975.
- What it produced: I could follow the call across five files and see where `_groupby_result_to_arrow` tried to convert the four-column result before the error appeared in `masked.py`.
- Difficulties: The error message mentioned only the mask, so it took some tracing to connect it back to `ohlc()`. The earlier PR avoided the error but returned `Float64`, which left me with one more question: what dtype should the result have?
- What changed: After reading `test_ohlc_ea_dtypes`, I decided to check both the values and the dtype. For a `float64[pyarrow]` input, all four output columns should still be `float64[pyarrow]`.

---

## Reproduction Process

**Environment Setup**

I started with a quick check on my M4 Mac using macOS and Python 3.14. A temporary virtual environment was set up at `/tmp/pd71940` with `pandas==3.1.1` and `pyarrow==26.0.1`. After the error reproduced there, it was also confirmed on the `3.2.0.dev0+736.gef7d968a25` nightly build from the scientific-python nightly wheels.

To trace the error in the source code, I forked `pandas-dev/pandas` as `pgazar/pandas`. My first attempt to clone it into `~/Documents/codepath/A301` failed with `Operation not permitted`, so I cloned it into `~/projects/pandas`. There, I created `.venv-dev`, installed the build and test dependencies, and built pandas using Meson and Apple clang 16.

The first build stopped with `CERTIFICATE_VERIFY_FAILED` while downloading `fast_float`. I installed `certifi`, pointed `SSL_CERT_FILE` to its certificate bundle with `export SSL_CERT_FILE=$(python -m certifi)`, and removed the incomplete `build-dev` directory. Rerunning `pip install -e . --no-build-isolation` succeeded and gave me pandas `3.2.0.dev0+737.g717bcd3eda`.

**Steps to Reproduce**

1. Create a clean environment and install the versions I used for the release check.

   ```bash
   python3.14 -m venv /tmp/pd71940
   /tmp/pd71940/bin/python -m pip install pandas==3.1.1 pyarrow==26.0.1
   ```

2. Run this example. It uses the same three values and group labels for both calls, so the only thing that changes is the dtype.

   ```bash
   /tmp/pd71940/bin/python - <<'PY'
   import pandas as pd

   ser = pd.Series([1.0, 2.0, 3.0], dtype="float64[pyarrow]")
   groups = ["a", "a", "b"]

   print(pd.__version__)
   print(ser.astype("Float64").groupby(groups).ohlc())
   print(ser.groupby(groups).ohlc())
   PY
   ```

3. Check the output. On pandas `3.1.1`, the `Float64` call prints a table with `open`, `high`, `low`, and `close` for groups `a` and `b`. The `float64[pyarrow]` call stops with `ValueError: Mask must be 1D array`.

**Reproduction Evidence**

- **Commit showing reproduction:** [5c3bc4d: fix and regression test](https://github.com/pgazar/pandas/commit/5c3bc4d0ee27ee425ee7dc09bfbc139ebf364f72). The traceback below records the failure before I made any changes; the linked commit contains my subsequent fix and regression test.
- **Screenshots/logs:** I saw the same error on the release, the nightly build, and my local source build. On the local build, I ran the example on `fix-groupby-ohlc-arrow` before changing the code. These are the relevant frames from that traceback, shortened for readability.

  ```text
  pandas/core/groupby/groupby.py        in ohlc
  pandas/core/groupby/ops.py            in _cython_operation
  pandas/core/groupby/ops.py            in cython_operation
  pandas/core/arrays/arrow/array.py     in _groupby_op
  pandas/core/arrays/arrow/array.py     in _groupby_result_to_arrow
  pandas/core/arrays/masked.py          in __arrow_array__
  ValueError: Mask must be 1D array
  ```

- **My findings:** The traceback helped me see why the error mentioned a mask. `ohlc()` produces a two-dimensional result with four values per group. The conversion fails when pandas tries to put that result into a one-dimensional Arrow array. I also reproduced the failure with `int64[pyarrow]`.

**Toolkit**

- Tool: I used `venv` and `pip` to set up the environments, `gh` to fork pandas, and the Python traceback in my terminal to follow the failing call. claude.ai (Claude Opus 5.5) set up the `/tmp` quick-check environments and ran the nightly check on my machine; I ran the release check and the check on my own build.
- What it produced: I had a small example I could run across all three versions and a `Float64` comparison that showed what the output should look like.
- Difficulties: macOS blocked the first clone location, and certificate verification interrupted the source build while it was downloading `fast_float`.
- What changed: Moving the clone to `~/projects/pandas` and setting `SSL_CERT_FILE` let me finish the build and trace the error in my own checkout.

---

## Solution Approach

**Analysis**

I traced the failure to the step after pandas calculates the four `ohlc()` values. For a PyArrow column, `_groupby_op` in `pandas/core/arrays/arrow/array.py` converts the input to a pandas masked type such as `Float64`, runs the aggregation, and passes the result to `_groupby_result_to_arrow`. That works for `sum()` or `mean()` because each group produces one value. With `ohlc()`, each group produces four values, so `__arrow_array__` in `pandas/core/arrays/masked.py` receives a two-dimensional mask and PyArrow raises `ValueError: Mask must be 1D array`.

**Proposed Solution**

I changed `_groupby_result_to_arrow` to pass the two-dimensional masked result back to `GroupBy.ohlc`, where pandas can build the four-column table. The earlier PR stopped there, which fixed the crash but left the output as `Float64` (or `Int64` for integer input), so I added `astype(obj.dtype)` to restore the input dtype on the finished table. I left the order-dependent `high` and `low` results for `NaN` values alone because that behavior comes from the Cython kernel in `groupby.pyx`, and the review showed it also occurs with `Float64`. I kept the fix scoped to PyArrow integer and float columns. Decimal columns still raise `NotImplementedError`, and other aggregations continue to use their existing conversion path.

**Implementation Plan**

1. In `array.py`, update `_groupby_result_to_arrow` to return the two-dimensional masked result directly.
2. In `groupby.py`, update `GroupBy.ohlc` to cast the finished table back to the input dtype.
3. Add `test_ohlc_pyarrow_dtypes` next to `test_ohlc_ea_dtypes`. Check the values and dtypes for PyArrow integer and float inputs, including a group containing only missing values and a call with `as_index=False`.
4. Add a whatsnew entry describing the fix for pandas users.

Deviations: My first release note said "numeric," which was too broad because it implied decimal support. After review, I changed it to "integer and float" and added `Resampler.ohlc` next to `DataFrameGroupBy.ohlc` and `SeriesGroupBy.ohlc`. Ruff also removed two trailing spaces from my edits.

**Toolkit**

- Tool: I used claude.ai to plan the fix, then ran Claude Code's `/pandas-review` in a fresh session with Fable 5.2 on high.
- What it produced: The review returned `NITS ONLY` with two optional findings. Its test run finished with 52,869 passed and 0 failed.
- Difficulties: I had to decide whether the `NaN` finding belonged in this fix. The review showed the same `high` and `low` behavior with `Float64`, which helped me identify it as a separate issue in the shared kernel.
- What changed: I corrected the release note and documented the `NaN` behavior as a known limitation in the PR.

---

## Testing Strategy

I used two observable checks, both run on my source build (`3.2.0.dev0+737.g717bcd3eda`, branch `fix-groupby-ohlc-arrow`).

**Check 1: the reproduction, re-run before and after the fix**

```python
ser = pd.Series([1.0, 2.0, 3.0], dtype="float64[pyarrow]")
ser.groupby(["a", "a", "b"]).ohlc()
```

- Before (main): `ValueError: Mask must be 1D array`.
- After (my branch): the table is returned, and the columns keep the input dtype:

```text
float64[pyarrow] -> ['double[pyarrow]', 'double[pyarrow]', 'double[pyarrow]', 'double[pyarrow]']
   open  high   low  close
a   1.0   2.0   1.0    2.0
b   3.0   3.0   3.0    3.0
c  <NA>  <NA>  <NA>   <NA>
int64[pyarrow] -> ['int64[pyarrow]', 'int64[pyarrow]', 'int64[pyarrow]', 'int64[pyarrow]']
```

(Group `c` in this run contained only a missing value.)

**Check 2: pandas' own test suite and code checks**

- New regression test `test_ohlc_pyarrow_dtypes` (`int64[pyarrow]` and `float64[pyarrow]`, with an all-missing group and `as_index=False`): **fails without the fix, passes with it** (checked by temporarily stashing the fix).
- `python -m pytest pandas/tests/groupby/aggregate/test_aggregate.py -k ohlc -v`: **15 passed**, including both new test cases.
- `python -m pytest pandas/tests/groupby/aggregate/test_aggregate.py pandas/tests/extension`: **43620 passed, 4206 skipped, 827 xfailed, 0 failed** in 66.64s.
- `pre-commit run --files` on the 4 changed files: every hook **Passed** or **Skipped** (no files of that type).
- `/pandas-review` ran `pandas/tests/groupby`, `pandas/tests/resample` and `pandas/tests/extension/test_arrow.py`: **52869 passed, 0 failed**.

**Toolkit**

- Tool: `pytest` in my `.venv-dev` build, `ruff` and `pre-commit` for code checks, and Claude Code's `/pandas-review` for an independent test run.
- What it produced: the new test failing before and passing after the fix, 43620 passing tests in the related suites, and a clean pre-commit run.
- Difficulties: my first edits had trailing spaces on two blank lines, which `ruff` reported and fixed ("Found 2 errors (2 fixed, 0 remaining)"). The skipped and xfailed counts also looked worrying at first, until I learned they are tests that do not apply to my setup or are known failures marked by pandas.
- What changed: I removed the trailing spaces, re-ran `git diff --check` until it was clean, and then ran the full pre-commit hooks instead of only `ruff`, so the "All code checks passed" box in my PR is true.

---

## Implementation Notes

**2026-11-06**

Installed the issue-scout, wrote four `gh search issues` queries, and ran my first scan: 0 of 5 accepted, all five sqlfluff issues already had PRs. Checked sqlfluff#9026 by hand and confirmed the linked PRs (#9027, #9035). Added `-- -linked:pr` to my queries and graded three Issue Selection site picks (airflow#60172, airflow#61667, arrow#51265); all were rejected for nobody-has-it. Found that `beam#19712` was already fixed on main by PR #39079 without a link to the issue.

**2026-11-20**

Looked at Apache Hamilton issues (#786 is an umbrella issue, #572 already had a draft PR). My scout accepted pandas#72215 (`resample().asfreq(fill_value=...)` ignored for PeriodIndex), and I reproduced it on pandas 3.1.1.

**2026-11-22**

Before I posted a claim on #72215, PR #72270 was opened for it, so I dropped it. Found #71940: two earlier `/take` claims had expired and PR #71975 had been closed by the bot only because of assignment. Reproduced the bug on pandas 3.1.1 and on the 3.2.0.dev0 nightly. Wrong turn: I first posted my claim comment on #72215 by mistake, deleted it, and posted it on #71940.

Forked pandas and cloned into `~/projects/pandas` after macOS blocked the clone in `~/Documents`. The first source build failed with `CERTIFICATE_VERIFY_FAILED`; fixed with `certifi` and `SSL_CERT_FILE`. Reproduced on my own build, then made the two changes (`_groupby_result_to_arrow` and `GroupBy.ohlc`), added `test_ohlc_pyarrow_dtypes`, and added a whatsnew entry. Decided to keep the output in the input's `ArrowDtype` instead of `Float64`, following `test_ohlc_ea_dtypes`. Ran 43620 tests (0 failed); `ruff` fixed two trailing-space errors I had added. Committed `5c3bc4d` on `fix-groupby-ohlc-arrow`.

Ran `/pandas-review` in a fresh Claude Code session (Fable 5.2, high): `NITS ONLY`. Changed the whatsnew wording from "numeric" to "integer and float" and added `Resampler.ohlc`; left the `NaN` high/low finding out of scope as a known limitation. `pre-commit` passed on all four files.

**2026-11-23**

Opened PR #72280 (UTC date). The bot labeled it `Needs Issue Assignment` because my first claim comment had text after `/take`. Posted a plain `/take`, and the issue was assigned to `pgazar`. CI: 42 checks passed, 0 failed so far.

**Toolkit**

- Tool: `git` on the `fix-groupby-ohlc-arrow` branch, VS Code for the edits, and claude.ai (Claude Opus 5.5) for step-by-step guidance.
- What it produced: commit `5c3bc4d`, 4 files and 33 added lines (two source files, one test, one whatsnew line).
- Difficulties: I added trailing spaces on blank lines more than once, and I posted my claim on the wrong issue.
- What changed: I now run `git diff --check` before committing, and I check the issue number in the address bar before posting a comment.

---

## Pull Request

**PR Link:** https://github.com/pandas-dev/pandas/pull/72280

**PR Description:** The description opens with the two-line reproduction (`ValueError: Mask must be 1D array` on main), explains the two changes (`_groupby_result_to_arrow` returns the two-dimensional masked result, and `GroupBy.ohlc` casts the result back to the input's `ArrowDtype`), and reports the testing (`test_ohlc_pyarrow_dtypes` fails on main and passes on the branch; 43620 passed, 0 failed). It records one known limitation (order-dependent `high`/`low` for unmasked `NaN` in Arrow float columns, which `Float64` shares), discloses the AI tools used (claude.ai with Claude Opus 5.5, and Claude Code with Fable 5.2 (high) for `/pandas-review`, which reported `NITS ONLY`), says `closes #71940`, and completes pandas' checklist.

**Maintainer Feedback:**

- 2026-11-23: none yet. The pandas bot labeled the PR `Needs Issue Assignment` because the bot had not assigned me the issue (my first claim comment had text after `/take`). I posted a plain `/take` (https://github.com/pandas-dev/pandas/issues/71940#issuecomment-7091668306), and the issue is now assigned to `pgazar`; pandas' daily job removes the label once the assignment is in place. CI: 42 checks passed, 0 failed, the rest still running at the time of writing.

**Status:** Awaiting review

**Toolkit**

- Tool: `gh` to fork pandas, push my branch, and open the PR page (`gh pr create --web`), with pandas' PR template and checklist; the pandas assignment bot then checked the PR.
- What it produced: PR #72280 linked to #71940 with `closes #71940`, and a bot comment and `Needs Issue Assignment` label because the issue was not yet assigned to me.
- Difficulties: my first claim comment had my explanation on the line right after `/take`, so the bot never assigned the issue, and I only found out when the PR was labeled.
- What changed: I posted a plain `/take` on its own, and the issue was assigned to `pgazar`. Next time I will post `/take` alone first and explain in a separate comment.

---

## Learnings and Reflections

**Technical Skills Gained**

I learned how to build pandas from source on my Mac and follow a traceback across several files. By tracing `ohlc()`, I could see where pandas tried to convert a four-column result into a one-dimensional Arrow array. I also wrote a regression test that failed on `main` and passed on my branch, checking both the values and their PyArrow dtype.

**Challenges Overcome**

The hardest part was deciding what to do when the review flagged a case where the position of `NaN` affected the `high` and `low` values. The review showed the same behavior with `Float64`, which helped me understand that it came from the shared calculation. I documented it in the PR as a known limitation and kept my fix focused on the Arrow conversion.

**What I'd Do Differently Next Time**

I spent time reproducing pandas issue #72215, only to find that someone had opened a PR before I claimed it. Next time, I'll check the issue history, post `/take` as a standalone comment, and confirm the bot assigned the issue to me before I start reproducing it.

---

## Resources Used

- pandas contributing guide, especially "Issue assignment and the pull request lifecycle" (`/take`, the 7-day rule) and the automated contributions policy: https://pandas.pydata.org/docs/dev/development/contributing.html
- pandas `AGENTS.md` (disclosing the AI tools I used and marking AI-written text): https://github.com/pandas-dev/pandas/blob/main/AGENTS.md
- The `pandas-review` skill I ran before opening the PR: https://github.com/pandas-dev/pandas/blob/main/.agents/skills/pandas-review/SKILL.md
- The issue thread for #71940 and the closed PR #71975, which showed me the earlier attempt and why it was closed: https://github.com/pandas-dev/pandas/issues/71940, https://github.com/pandas-dev/pandas/pull/71975
- The existing test `test_ohlc_ea_dtypes` in `pandas/tests/groupby/aggregate/test_aggregate.py`, which I used as the model for the expected dtype and for my new test
- claude.ai (Claude Opus 5.5), which guided me step by step through setup, the fix, and the PR
