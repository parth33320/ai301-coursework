# Test Evidence for Issue #14: Offline Eval Runner Fix
Branch: `fix/14-offline-eval-runner`
Target Repository: `codepath/pathreview-ai301-fa26-s3`

---

## 1. Repro Steps Before and After

### Repro Command
`python scripts/run_evals.py`

### Output Before Fix (`main` branch)
```powershell
$ python scripts/run_evals.py
Running RAG evaluation suite...
Evaluation complete. Results written to eval_results.json
$ Test-Path eval_results.json
False
```

### Output After Fix (`fix/14-offline-eval-runner` branch)
```
$ python scripts/run_evals.py
Running RAG evaluation suite...
2026-10-04 21:31:04 [info     ] relevance_scored               avg_score=0.14285714285714285 chunks_count=2 query_len=7
2026-10-04 21:31:04 [info     ] faithfulness_checked           claims_count=2 score=1.0 supported_count=2
2026-10-04 21:31:04 [info     ] eval_suite_complete            faithfulness=1.0 overall=0.5714285714285714 relevance=0.14285714285714285
Scored 1 profile(s), mean overall 0.571
Evaluation complete. Results written to C:\Users\Parth\Documents\pathreview-ai301-fa26-s3\eval_results.json
$ Test-Path eval_results.json
True
$ Get-Content eval_results.json
{"timestamp": "2026-10-05T01:31:04.944112+00:00", "profile_count": 1, "mean_relevance": 0.14285714285714285, "mean_faithfulness": 1.0, "mean_overall": 0.5714285714285714, "results": [{"profile": "basic_profile", "repo_count": 2, "relevance_score": 0.14285714285714285, "faithfulness_score": 1.0, "overall_score": 0.5714285714285714}]}
```

---

## 2. Repo Checks Verification

`make` is not installed on this Windows machine, so each check below is the equivalent command run directly. The `make` targets were not run.

### Check 1: `make test-unit` (equivalent: `python -m pytest`)
```
$ python -m pytest tests/unit/test_run_evals.py -q
..                                                                                                                        [100%]
2 passed in 0.07s
```

### Check 2: `make test-integration` (equivalent: `python -m pytest tests/integration`)
```
$ python -m pytest tests/integration -q
no tests ran in 0.01s
```
*(Note: As documented in Assignment 4 guidelines, Path Review currently has no integration tests, so "no tests ran" is expected).*

### Check 3: `make lint` (equivalent: `ruff check scripts/`)
```
$ ruff check scripts/
All checks passed!
```

### Check 4: `make typecheck` (equivalent: `mypy scripts/`)
```
$ mypy scripts/
Success: no issues found in 1 source file
```

### Check 5: existing unit tests still pass (plan test plan)
Run on the branch, because the plan requires the existing scorer and checker tests to keep passing. The 5 xfailed tests are existing `xfail` markers on seeded bugs in those files, not new failures.
```
$ python -m pytest tests/unit/test_relevance_scorer.py tests/unit/test_faithfulness_checker.py -q
..x..................x...x......x....x...                                [100%]
36 passed, 5 xfailed in 0.21s
```
