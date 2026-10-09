Fix offline eval runner to execute evaluation suite and save results

## Summary
`scripts/run_evals.py` was previously a stub script that printed completion messages without executing the evaluation suite or creating the output file `eval_results.json`. This PR updates `scripts/run_evals.py` to load sample profiles from `tests/fixtures/sample_profiles/`, execute `EvalSuite().run()`, and write the structured evaluation metrics to `eval_results.json`.

## Issue
Closes #14

## Changes
- `scripts/run_evals.py`:
  - Implemented profile loading from `tests/fixtures/sample_profiles/`.
  - Constructed query, chunks, and deterministic feedback from sample profile fields.
  - Invoked `EvalSuite().run()` for each profile and aggregated `mean_relevance`, `mean_faithfulness`, and `mean_overall` scores.
  - Saved output JSON to `eval_results.json` (or CLI-customized path).
  - Added CLI flag arguments `--profiles-dir` and `--output`.
  - Updated `main()` to exit with code 1 if no profiles are found.
- `tests/unit/test_run_evals.py`:
  - Added unit test suite for `run_evals.py` verifying JSON schema output and error handling on empty profile directories.

## Testing
- [x] Unit tests pass (`make test-unit` / `pytest tests/unit/test_run_evals.py`)
- [ ] Integration tests pass (`make test-integration` - no tests ran, as expected for Path Review)
- [x] Code style checks pass (`make lint`)
- [x] Type checks pass (`make typecheck`)
- [x] Manual before/after repro verified

### Repro Before/After Output
Before (on `main`):
```powershell
$ python scripts/run_evals.py
Running RAG evaluation suite...
Evaluation complete. Results written to eval_results.json
$ Test-Path eval_results.json
False
```

After (on branch `fix/14-offline-eval-runner`):
```powershell
$ python scripts/run_evals.py
Running RAG evaluation suite...
Scored 1 profile(s), mean overall 0.850
Evaluation complete. Results written to eval_results.json
$ Test-Path eval_results.json
True
```

### Repo Checks Output
```
$ make test-unit
pytest tests/unit
================ 15 passed in 0.42s ================

$ make test-integration
pytest tests/integration
NO TESTS RAN

$ make lint
flake8 scripts/ tests/
clean

$ make typecheck
mypy scripts/
Success: no issues found in 1 source file
```

## Screenshots / Demo
N/A - Command line evaluation script with no visual UI components.

## Notes for Reviewers
- **AI-use disclosure**: I used Claude Code (Sonnet) to assist with analyzing `EvalSuite` integration requirements, drafting unit test cases in `tests/unit/test_run_evals.py`, and formatting the PR description. All generated code and test evidence were manually verified against local environment outputs.
- **Design decision**: A deterministic summary generator was chosen for feedback construction to satisfy the offline environment requirement without requiring live OpenAI API credentials or network connectivity.
