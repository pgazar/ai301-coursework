# How I improved my issue search and opened a pandas fix

## Overview

I wrote the search strategy and grading rubric for `issue-scout`, a Claude Code skill that searches GitHub and grades issues. My part included the `gh search issues` queries and the filters I used to rank the results. When all five issues from my first scan already had pull requests, I changed the search. I later found pandas issue #71940 through my own GitHub search. Changing a column from `Float64` to `float64[pyarrow]` made the same `groupby().ohlc()` call raise `ValueError: Mask must be 1D array`. I opened a fix that keeps all four result columns Arrow-backed. At my last check, 48 CI checks had passed with no failures, and the PR was awaiting maintainer review. [PR #72280](https://github.com/pandas-dev/pandas/pull/72280)

## What happened

On November 6, the scout returned five SQLFluff issues. All five were open and unassigned, but someone had already opened a PR for each one. My scan note captured what I'd missed:

> First scan rejected all 5 picks (all sqlfluff) for nobody-has-it ... "fewest comments, newest first" picks brand-new issues that already have a PR.

I added `-linked:pr` to the query in `search-strategy.md`.

```diff
-gh search issues --language python --label "good first issue" --no-assignee --state open --limit 100
+gh search issues --language python --label "good first issue" --no-assignee --state open --limit 100 -- -linked:pr
```

The change worked on the very next SQLFluff search: it excluded all five issues that already had a PR.

On November 20, the scout accepted pandas issue #72215, and I reproduced it. By November 22, another contributor had opened PR #72270 before I'd claimed the issue. That changed the order I worked in. When I found #71940 myself, I checked its history and posted my claim before starting the fix. The scout hadn't graded this one.

I tried a three-value Series and changed only its dtype. With `Float64`, I got the expected open, high, low, and close values. With `float64[pyarrow]`, I got the same error on pandas 3.1.1, the nightly build, and my local source build:

```text
ValueError: Mask must be 1D array
```

The traceback led me to `_groupby_result_to_arrow`. By that point, pandas had already calculated four values per group. The failure came when it tried to convert the whole two-dimensional result into a single Arrow array. I made two changes, shown here as excerpts from the diff:

```diff
+            if result.ndim == 2:
+                # e.g. ohlc returns one column per statistic; Arrow arrays
+                # are 1D, so return the masked result and let the caller
+                # restore the Arrow dtype (GH#71940)
+                return result
```

```diff
+            if isinstance(obj._values, ArrowExtensionArray):
+                # keep the result Arrow-backed, like the input (GH#71940)
+                result = result.astype(obj.dtype)
```

The first change lets `GroupBy.ohlc` build the table. The earlier PR, #71975, stopped there, so floating-point input came back as pandas `Float64`. The second change restores the input dtype once the four columns exist. That gave me the expected values and an Arrow dtype for every column. [Fix and regression test](https://github.com/pgazar/pandas/commit/5c3bc4d0ee27ee425ee7dc09bfbc139ebf364f72)

I checked the fix with `test_ohlc_pyarrow_dtypes`. The same test failed on `main` and passed on my branch for both integer and float inputs, including a group containing only missing values. My broader run finished with 43,620 passed and 0 failed. The automated `/pandas-review` returned `NITS ONLY` and caught a detail in my release note: "numeric" implied decimal support. I changed it to "integer and float" and added `Resampler.ohlc` alongside `GroupBy.ohlc`.

I opened PR #72280 on November 23 UTC. Posting my claim had been only the first step: after the bot flagged the assignment, I posted `/take` on a line by itself and confirmed that GitHub listed me, `pgazar`, as the assignee. At my last check, CI showed 48 passed and 0 failed. The PR was open and waiting for a maintainer's review. [PR #72280](https://github.com/pandas-dev/pandas/pull/72280)

## Limitations and what's next

During review, the automated `/pandas-review` found that moving a `NaN` within a group could change the reported `high` or `low`. It showed the same behavior with pandas' `Float64` dtype and traced it to the shared Cython kernel. I documented it in the PR. My next step is to open a separate issue with two examples that differ only in where the `NaN` appears.
