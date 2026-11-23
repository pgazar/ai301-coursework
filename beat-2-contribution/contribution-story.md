# Contribution: [Issue Title]

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** [1]
**Student:** [Student name]
**GitHub Username:** [your GitHub username, exactly as it appears on your profile - no @, no
profile URL. Your comments upstream are identified by this name.]
**Issue:** [GitHub issue link]
**Pull Request:** [GitHub PR link, once opened]

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

**Unit 5: [one line on the tool work]**

[I installed the issue-scout and wrote four `gh search issues` queries from my fit profile (Python first, then data projects: pandas, SQLFluff, Great Expectations), ranked by fewest comments. My first scan rejected all five picks for nobody-has-it. I opened sqlfluff#9026 and saw two linked PRs (#9027, #9035), so the rubric was right, and my strategy was the problem: "fewest comments, newest first" chose brand-new issues whose reporters had already opened PRs. I added `-linked:pr` to every query, which removed all five, but I found it also hides issues with only abandoned PRs (arrow#39149) and misses issues already fixed without a link (beam#19712, fixed by #39079), so next I want a date filter in my queries and a rubric check that the bug still reproduces on main.]

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

[1-2 paragraphs in your own words: how you searched, what your scan
surfaced, what your scout's verdict said, why this issue fits your
profile, and what you hope to learn. If your scout's verdict on it
was reject and you chose it anyway, say so here and say why; choosing
against your own tool silently is what loses points. Close with an
honest read of the tool itself: where the scout fell short on a real
wild search and what you changed or would change, or what convinced
you it held up. **Record the link to your claim comment in this section too.**
Not claimed yet? Write `claim pending` and today's date where the link goes,
and add the link the moment your claim lands.]

**Toolkit**

- Tool: issue-scout (your scout: your search strategy, your rubric),
  and how you used it
- What it produced: [the scan and its verdict on this issue, described]
- Difficulties: [where it fell short, or "none met" if none did]
- What changed: [what you patched or would update, or "nothing"]

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

[Your analysis of the root cause: what is causing the issue, traced
to the files and functions involved.]

**Proposed Solution**

[High-level description of your fix approach, and what you are
deliberately NOT changing.]

**Implementation Plan**

1. [Modify file X to do Y]
2. [Add or change Z]
3. [Update tests]

[Record deviations here as they happen: what changed mid-build and
why. A recorded deviation is honest work; silent drift is not.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Testing Strategy

[What observable checks prove the fix: your reproduction steps re-run
against the built change (before and after), and the repo's own test
suite or checks with the outcome shown. "Tests pass" with nothing
observable named is not a strategy.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Implementation Notes

[A running log, added to as you work. Date each entry. What you
built, decisions you made when you made them, wrong turns included.]

**[Date]**

[Entry]

**[Date]**

[Entry]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Pull Request

**PR Link:** [GitHub PR URL when opened]

**PR Description:** [what the PR says; much of the content above
adapts into it]

**Maintainer Feedback:**

- [Date]: [feedback received, or "none yet"; silence is a normal
  state and costs you nothing]
- [Date]: [how you addressed it]

**Status:** [Awaiting review / Iterating / Merged / Closed. The
status is a fact you record, never a grade: the PR is graded as
opened.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Learnings and Reflections

**Technical Skills Gained**

[What you learned technically, named concretely.]

**Challenges Overcome**

[What was hard and how you solved it; point at the real events, the
Toolkit blocks above are full of them.]

**What I'd Do Differently Next Time**

[Reflection on your process.]

---

## Resources Used

[What actually helped, not a reading list. List things this story
already shows you using: the doc you followed to set the environment
up, the thread that explained the bug, the person who answered you.
One real entry beats ten plausible ones.]

- [Link to documentation that helped]
- [Discussion, issue, or post that helped]
