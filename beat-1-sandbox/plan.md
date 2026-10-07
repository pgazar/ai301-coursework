# Plan: output parser crashes on a top-level JSON array response (#69)

## Diagnosis

`_parse_json_output(data)` in `rag/generator/output_parser.py` unconditionally calls `data.items()` at line 68, assuming `data` is always a dict. The repro evidence (Unit 2) confirmed this directly: a direct call with a top-level JSON array raises `AttributeError: 'list' object has no attribute 'items'` at that exact line, while the identical parser called with a JSON object succeeds cleanly and returns a `FeedbackSection`. The repro also confirmed both call sites that reach this function — the fenced-` ```json ` branch (line 40) and the raw-JSON branch (line 47) — crash identically when the parsed value is a list, since both route through the same `_parse_json_output` function.

Quoting the repro evidence directly: *"The crash is isolated to `_parse_json_output`'s unconditional `data.items()` call at `output_parser.py:68`, which assumes the parsed data is always a dict."*

## Scope

**In scope:** `parse_review_output` in `rag/generator/output_parser.py` — guard both `json.loads` call sites so a non-dict result (an array, or any other non-dict JSON value) falls through to the existing plaintext fallback instead of reaching `_parse_json_output`.

**Out of scope:** `_parse_json_output` itself (no change — dict parsing is untouched), `_parse_plaintext_output` (reused as-is), and the code-fence vs. raw-JSON detection logic (unchanged; only what happens after a successful parse changes).

## Files

- `rag/generator/output_parser.py` (the fix)
- `tests/unit/test_output_parser.py` (remove the `xfail` marker on `test_json_array_fallback`, add a new test for the fenced-array path, which had no prior coverage)

## Approach

Add an `isinstance(data, dict)` guard immediately after each `json.loads` call in `parse_review_output`. If the parsed value is a dict, proceed to `_parse_json_output` exactly as before. If it isn't (an array, in this issue's case), skip `_parse_json_output` and let execution fall through to the existing `_parse_plaintext_output(raw)` fallback at the end of the function — the same path already used for malformed/unparseable JSON.

## Revision note

**This plan was revised from an earlier draft.** My first version added array-handling *inside* `_parse_json_output`, treating each array item as its own `FeedbackSection` — reasoning that this would "preserve the array's structure" better than discarding it via plaintext fallback. Checking that draft against the issue's actual caller (`review_generator.py:76`, `return sections[0]`) showed this reasoning was wrong: that caller keeps only the *first* section and discards the rest regardless of how many sections are produced, so a multi-item array would still silently lose everything past the first item — the exact problem per-item sections were supposed to solve. The repro report itself had left this as an open question ("fall back to plaintext, or treat each array item as its own section"), and I failed to flag it as a real unresolved risk in my first draft; I'd also stated incorrectly that I'd found no other callers of `parse_review_output`, when `review_generator.py` does call it. Separately, four classmates (jacho15, Mamadouba2004, ssmitpatel, SomeshRamakanth) had already posted plans on this thread, all converging on the same dict-guard-plus-plaintext-fallback approach — one explicitly ruling out per-item sections as "new behavior, not the fallback." This plan now follows that converged approach, which is also what the issue's own wording describes ("the fallback path should handle array responses" — i.e., the *existing* plaintext fallback, not a new code path).

## Test plan

Re-running the Unit 2 repro steps against the fix:

1. Direct call: `parse_review_output(json.dumps(["First feedback item","Second feedback item"]))` — before: `AttributeError`; after: falls back to plaintext, returning one `FeedbackSection` (`section_name="general_feedback"`), no crash.
2. Fenced-`json` variant (the other call site): same array wrapped in a ` ```json ` fence — before: same `AttributeError`; after: same plaintext fallback. Note: since the fallback receives the *whole raw string*, the fenced case's `general_feedback` content includes the surrounding ` ``` ` fence markers, not just the array text — a visible but accepted side effect of the same fallback path.
3. Control (must stay unchanged): `parse_review_output(json.dumps({"summary":"Works"}))` — still returns a single structured `FeedbackSection` via `_parse_json_output`, confirming the dict path is untouched.
4. The covering test: `pytest tests/unit/test_output_parser.py::TestOutputParser::test_json_array_fallback -v` — before (with `--runxfail`): `FAILED` with the `AttributeError`; after the `xfail` marker is removed: `PASSED`, now asserting the specific fallback behavior (one `general_feedback` section) rather than only `isinstance(result, list)`.
5. New test added for the previously-uncovered fenced-array path: `test_json_array_in_fence_fallback`.
6. Full existing suite: `pytest tests/unit/test_output_parser.py -v` — all 20 tests pass, confirming no regression to dict parsing, plaintext fallback, or malformed-JSON handling.

## Risks and unknowns

- I have not traced every consumer of `parse_review_output`'s return value across the full codebase beyond `review_generator.py` and the test file — there may be other callers whose behavior under the fallback path I haven't checked.
- The plaintext fallback's `general_feedback` section will contain the raw JSON text as a string rather than the individual items — for the direct-array case this is just the array's own text (e.g. `'["First feedback item", "Second feedback item"]'`); for the fenced case it also includes the surrounding ` ``` ` fence markers, since the fallback receives the whole raw string. This is a real loss of structure for this specific input shape, which I'm accepting because it matches both the issue's wording and the thread's converged approach, not because it's lossless.

## Deviations

Nothing changed between this plan and the build. The `isinstance(data, dict)` guard landed at both `json.loads` call sites exactly as described, `_parse_json_output` and `_parse_plaintext_output` were left untouched, the `xfail` marker was removed from `test_json_array_fallback`, and `test_json_array_in_fence_fallback` was added as planned. The full test suite (20 tests) passes.