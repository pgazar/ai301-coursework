# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

---

## Posted upstream

**GitHub username**

pgazar

**Plan comment**

Link: https://github.com/codepath/pathreview-ai301-fa26-s1/issues/69#issuecomment-6030359088

Text:

> Plan for #69, on commit `f89c06f`.
>
> **Diagnosis:** confirmed directly in my repro (posted above) — `_parse_json_output`'s unconditional `data.items()` call at `output_parser.py:68` crashes on a top-level JSON array, at both call sites that reach it (the fenced-`json` branch and the raw-JSON branch).
>
> I see jacho15, Mamadouba2004, ssmitpatel, and SomeshRamakanth have already posted plans here, converging on a dict guard at both `json.loads` sites that falls through to the existing plaintext parser. My own first draft went a different direction (treating each array item as its own section), but checking it against `review_generator.py`'s actual caller showed that caller keeps only `sections[0]` regardless — so per-item sections wouldn't have preserved anything past the first item anyway. I'm going with the same approach the thread has converged on: guard both call sites with `isinstance(data, dict)`, and let a non-dict result fall through to the existing plaintext fallback.
>
> **Test plan:** re-running my repro's steps against the change (both call sites, plus the full existing test suite to confirm no regression to dict parsing), removing the `xfail` marker on `test_json_array_fallback`, and adding a test for the fenced-array path, which had no prior coverage.
>
> **Known limitation:** the plaintext fallback keeps the raw array text as one section's content rather than the individual items — a real loss of structure for this input shape, but it matches the issue's wording and what the thread has already settled on.

---

## Your branch

**Branch**

fix/69-json-array-output-parser

**Evidence**

Before (Unit 2 repro, unchanged code):

```
$ .venv/bin/python -c 'import json; from rag.generator.output_parser import parse_review_output; print(parse_review_output(json.dumps(["First feedback item","Second feedback item"])))'
Traceback (most recent call last):
  File "<string>", line 1, in <module>
  File ".../rag/generator/output_parser.py", line 48, in parse_review_output
    return _parse_json_output(data)
  File ".../rag/generator/output_parser.py", line 68, in _parse_json_output
    for key, value in data.items():
AttributeError: 'list' object has no attribute 'items'
```

```
$ .venv/bin/python -m pytest tests/unit/test_output_parser.py::TestOutputParser::test_json_array_fallback --runxfail -v --tb=short
FAILED tests/unit/test_output_parser.py::TestOutputParser::test_json_array_fallback - AttributeError: 'list' object has no attribute 'items'
```

After (built change, branch `fix/69-json-array-output-parser`):

```
$ .venv/bin/python -c 'import json; from rag.generator.output_parser import parse_review_output; print(parse_review_output(json.dumps(["First feedback item","Second feedback item"])))'
2026-10-06 20:07:37 [warning  ] raw_json_not_a_dict            data_type=list
2026-10-06 20:07:37 [info     ] plaintext_output_parsed        content_length=47
[FeedbackSection(section_name='general_feedback', content='["First feedback item", "Second feedback item"]', confidence=0.7, suggestions=[])]

$ .venv/bin/python -c 'import json; from rag.generator.output_parser import parse_review_output; raw = "```json\n" + json.dumps(["First feedback item","Second feedback item"]) + "\n```"; print(parse_review_output(raw))'
2026-10-06 20:07:37 [warning  ] json_fence_not_a_dict          data_type=list
2026-10-06 20:07:37 [warning  ] raw_json_parsing_failed
2026-10-06 20:07:37 [info     ] plaintext_output_parsed        content_length=59
[FeedbackSection(section_name='general_feedback', content='```json\n["First feedback item", "Second feedback item"]\n```', confidence=0.7, suggestions=[])]

$ .venv/bin/python -c 'import json; from rag.generator.output_parser import parse_review_output; print(parse_review_output(json.dumps({"summary":"Works"})))'
2026-10-06 20:07:37 [info     ] json_output_parsed             section_count=1
[FeedbackSection(section_name='summary', content='Works', confidence=0.85, suggestions=[])]

$ .venv/bin/python -m pytest tests/unit/test_output_parser.py -v
============================== 20 passed in 0.13s ==============================
```

## Eval iterations

**Run history**

1. Initial rubric (your group's "Our rubric" from the activity worksheet — diagnosis, test required; scope, auto-tests preferred — plus 3 new checks added to cover the remaining named failure families: executability, honesty, comms) — smoke test with `--limit 3`: 3/3 agreement.
2. First full run: 19/20. The one miss, pkg-14, failed on `diagnosis` and `honesty` — the plan explicitly said "exact functions to be pinned in the PR after tracing," which my wording misread as unconfirmed/overconfident, when it was actually normal, honest sequencing (the causal mechanism was grounded via a debug trace; only the exact function names were deferred to implementation).
3. Fixed `diagnosis` and `honesty` to explicitly distinguish "the causal mechanism is grounded in evidence" from "the exact code location is already identified" — a plan may defer the latter without that counting against either check. Verified with `--only pkg-14,pkg-01,pkg-02` (the fix plus a `wrong-cause` and a `clear-accept` canary): 3/3 agreement, both canaries held.
4. Confirming full run: 20/20, PASS, every category at 100% (clear-accept 7/7, scope-creep 4/4, thread-convention 2/2, unbuildable 3/3, wrong-cause 4/4). `eval-run.txt` committed matches this run.

**Package analysis**

Scored package: **pkg-14** (zellij-org/zellij#5174, OSC color-sequence leak into the terminal on SSH session reattach). Gold label: accept. My rubric's first full run graded it: reject, on `diagnosis` and `honesty`.

The candidate plan grounds its diagnosis in multiple pieces of repro evidence: a clean fresh-attach, a leak only on reattach, a clean 0.44.1 (predating the reattach-path change), and a cache-clear control that produces exactly one more clean attach. It also honestly flags real risk (a possible dropped keystroke, with a stated mitigation) and an explicitly untested platform (Windows). But it also says "exact functions to be pinned in the PR after tracing... which I have working (the leak's origin is visible in `zellij --debug` output)." My original wording for `diagnosis` and `honesty` read that admission as a sign the cause wasn't actually confirmed, when the debug trace had already confirmed the *mechanism* — only the specific function names were left for implementation, which is normal sequencing, not an honesty problem. After revising both checks to make that distinction explicit, this package correctly flips to accept.

**Check rationale**

Quoting `diagnosis` as it currently reads in my uploaded `rubric.md`:

> "The stated cause directly explains the specific behavior the repro evidence shows, and is grounded in that evidence (a behavioral correlation across versions, a control test, a debug trace, or equivalent) — not a plausible-sounding but ungrounded guess, and not a cause that only explains the symptom while the evidence points further upstream. A plan may say the exact code location (specific functions or lines) will be pinned down during implementation without that counting against this check: diagnosis is about whether the causal mechanism is grounded in evidence, not whether the code location is already identified"

I revised this after pkg-14 failed for conflating two different things: whether the *cause* is grounded in evidence, and whether the *exact code location* has been pinned down. The original wording didn't separate them, so a plan honestly admitting it hadn't yet traced exact function names read as if its cause were unconfirmed. The current wording keeps the bar on evidence-grounding (a correlation, a control, a debug trace) while explicitly not penalizing a plan for deferring code-location specifics to implementation.

**Trade-offs**

Loosening `diagnosis`/`honesty` to accept a mechanism grounded by a debug trace, even with exact function names deferred, trades away some protection against a plan that claims a trace confirmed something it didn't. I re-ran the `wrong-cause` canary (pkg-01, which rejects for a genuinely wrong diagnosis) both before and after this change, and it held correctly both times — `wrong-cause` packages assert an incorrect cause outright, which this change doesn't touch; it only stopped penalizing *honest sequencing* about implementation detail, not weak grounding.