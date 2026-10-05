# Plan: implement the offline eval runner (issue #14)

Issue: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/14
Repo: codepath/pathreview-ai301-fa26-s3 (my fork: parth33320/pathreview-ai301-fa26-s3), base commit `2f4e82f52efbcfcc57d65b3fa5348672163ca088`

## Diagnosis

`scripts/run_evals.py` is a stub, not a broken runner. My unit 2 repro (Windows NT 10.0.22621.0, PowerShell, Python 3.14.7, commit `2f4e82f`) showed:

- `Get-Content scripts\run_evals.py`: `main()` prints a line, has a `# TODO: Implement eval runner` block, then prints a second line.
- `Select-String -Pattern "EvalSuite"` over all `.py` files: one hit, `rag\evaluator\eval_suite.py` line 22 (the class definition). Nothing, including `scripts\run_evals.py`, calls it.
- `python scripts/run_evals.py` printed `Running RAG evaluation suite...` and `Evaluation complete. Results written to eval_results.json`, exit code 0.
- `Test-Path eval_results.json` returned `False`.

So the cause is that the runner never loads a profile, never calls `EvalSuite.run`, and never writes the file, yet its last line claims it did. `make eval` (Makefile) and `.github/workflows/eval.yml` already call this script, and the workflow already reads `eval_results.json`, so those need no change. I did not run `make eval` in unit 2 because `make` is not installed on my Windows setup.

## Scope

In scope: make `scripts/run_evals.py` score the benchmark profiles in `tests/fixtures/sample_profiles/` with the existing `EvalSuite` and write `eval_results.json`, exit non-zero if there are no profiles, and cover it with a unit test.

Out of scope (non-goals):
- Changing `EvalSuite`, `RelevanceScorer` or `FaithfulnessChecker` scoring.
- Calling a real LLM or the network; the feedback is a deterministic stand-in so the runner stays offline.
- Changing `Makefile` or `.github/workflows/eval.yml`.
- Adding new fixtures or an "actionability" score (the stub's TODO lists it, but `EvalSuite` has no such score).
- Committing `eval_results.json` (it is a generated file).

## Files I will touch

- `scripts/run_evals.py` (implement the runner)
- `tests/unit/test_run_evals.py` (new unit test)

## Approach

1. `load_profiles(dir)` reads every `*.json` in `tests/fixtures/sample_profiles/`, sorted by name.
2. `build_case(profile)` builds `(query, chunks, feedback)` from the profile: the query is the repos' languages and topics, the chunks are each repo's description plus README text, and the feedback is a deterministic one-sentence-per-repo summary built from the same fields.
3. `run()` calls `EvalSuite().run(query, chunks, feedback)` for each profile and writes `eval_results.json` with `timestamp`, `profile_count`, `mean_relevance`, `mean_faithfulness`, `mean_overall` and a per-profile `results` list. A `--output` and `--profiles-dir` flag allow tests to avoid touching the repo root.
4. `main()` keeps the two existing print lines but prints the "written" line only after the file is written, and returns exit code 1 when no profile is found.

## Test plan

Re-run my unit 2 repro steps after the fix and expect:

- `python scripts/run_evals.py` exits 0 and prints `Scored 1 profile(s), mean overall <number>` before `Evaluation complete. Results written to ...eval_results.json`.
- `Test-Path eval_results.json` returns `True` (before the fix it returned `False`).
- `eval_results.json` is valid JSON with `profile_count` = 1 and one `basic_profile` result whose `relevance_score`, `faithfulness_score` and `overall_score` are each between 0.0 and 1.0.
- `grep -rn EvalSuite --include=*.py .` now also shows a hit in `scripts/run_evals.py`.
- `pytest tests/unit/test_run_evals.py` passes: one test runs `main()` on the fixture and checks the written JSON; the other passes an empty directory and expects exit code 1.
- Existing `tests/unit/test_relevance_scorer.py` and `test_faithfulness_checker.py` still pass.

## Risks and unknowns

- Unknown: whether the maintainers want the feedback stand-in to come from the real generator with a mock provider; I chose a deterministic stand-in because the generator needs `openai` and an API client, which would break the "offline" requirement. I will say so in the PR.
- Risk: with one fixture profile the scores are a smoke signal, not a quality benchmark; more profiles would make the means meaningful, but adding fixtures is out of scope.
- Risk: the CI workflow's comment step needs a writable token, which fork PRs do not have; the workflow already degrades to a log notice, so I am not changing it.
- Unknown: I have not run `make eval` on Windows (no `make`); I will run it on a machine that has it.

## Deviations

Filled in after the build. See below.

- Against the plan comment I first posted on the issue: that earlier comment described the course's eval harness (pkg-01 to pkg-20, gold labels, skill files), not this issue, so I replaced it with this plan, which is built from my unit 2 repro of `scripts/run_evals.py`.
- Against this plan: the build matched the approach and file list. Two small differences: `run()` skips writing the file when there are no profiles (so a failed run never leaves a stale `eval_results.json`), and the unit test also covers that empty-directory case. I did not run `make eval`, as planned.
