Plan for #14, built from my reproduction above (commit `2f4e82f`, Windows NT 10.0.22621.0, PowerShell, Python 3.14.7; the issue does not state an OS or Python version, so there is no difference to note).

**Diagnosis:** `scripts/run_evals.py` is a stub. `main()` only prints two lines around a `# TODO: Implement eval runner` block. In my repro, `EvalSuite` has exactly one hit across the `.py` files, its definition at `rag/evaluator/eval_suite.py:22`, so nothing calls it. Running the script exits 0 and prints "Results written to eval_results.json", but `Test-Path eval_results.json` returned `False`. `make eval` and the RAG Evaluation workflow already run the script and read `eval_results.json`, so no change is needed there.

**Scope:** I plan to make the script score each profile in `tests/fixtures/sample_profiles/` with the existing `EvalSuite` and write `eval_results.json`. I won't change the scorers, `Makefile` or the workflow, call a live LLM, or add fixtures.

**Files:** `scripts/run_evals.py` and a new `tests/unit/test_run_evals.py`.

**Approach:** load each profile, build a query (repo languages and topics), chunks (repo descriptions and READMEs) and a deterministic feedback string from the same fields so the run stays offline, call `EvalSuite().run(...)`, and write per-profile and mean relevance, faithfulness and overall scores. It exits 1 if no profiles are found, and prints the "written" line only after the file is written.

**Test plan:** re-run my repro. Expect exit code 0, `eval_results.json` present and valid JSON with `profile_count` 1 and scores between 0.0 and 1.0 for `basic_profile`, an `EvalSuite` hit in `scripts/run_evals.py`, and the new unit test plus the existing relevance and faithfulness tests passing.

**Unknowns:** whether maintainers would rather the feedback come from the real generator with the mock provider (I chose a deterministic stand-in to stay offline), and I could not run `make eval` since `make` isn't installed on my Windows setup. I'm investigating and planning only so far; happy to adjust if you'd prefer a different shape.
