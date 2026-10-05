# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

---

## Posted upstream

**GitHub username**

parth33320

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/14#issuecomment-5966383179

> Plan for #14, built from my reproduction above (commit `2f4e82f`, Windows NT 10.0.22621.0, PowerShell, Python 3.14.7; the issue does not state an OS or Python version, so there is no difference to note).
> 
> **Diagnosis:** `scripts/run_evals.py` is a stub. `main()` only prints two lines around a `# TODO: Implement eval runner` block. In my repro, `EvalSuite` has exactly one hit across the `.py` files, its definition at `rag/evaluator/eval_suite.py:22`, so nothing calls it. Running the script exits 0 and prints "Results written to eval_results.json", but `Test-Path eval_results.json` returned `False`. `make eval` and the RAG Evaluation workflow already run the script and read `eval_results.json`, so no change is needed there.
> 
> **Scope:** I plan to make the script score each profile in `tests/fixtures/sample_profiles/` with the existing `EvalSuite` and write `eval_results.json`. I won't change the scorers, `Makefile` or the workflow, call a live LLM, or add fixtures.
> 
> **Files:** `scripts/run_evals.py` and a new `tests/unit/test_run_evals.py`.
> 
> **Approach:** load each profile, build a query (repo languages and topics), chunks (repo descriptions and READMEs) and a deterministic feedback string from the same fields so the run stays offline, call `EvalSuite().run(...)`, and write per-profile and mean relevance, faithfulness and overall scores. It exits 1 if no profiles are found, and prints the "written" line only after the file is written.
> 
> **Test plan:** re-run my repro. Expect exit code 0, `eval_results.json` present and valid JSON with `profile_count` 1 and scores between 0.0 and 1.0 for `basic_profile`, an `EvalSuite` hit in `scripts/run_evals.py`, and the new unit test plus the existing relevance and faithfulness tests passing.
> 
> **Unknowns:** whether maintainers would rather the feedback come from the real generator with the mock provider (I chose a deterministic stand-in to stay offline), and I could not run `make eval` since `make` isn't installed on my Windows setup. I'm investigating and planning only so far; happy to adjust if you'd prefer a different shape.

---

## Your branch

**Branch**

fix/14-offline-eval-runner

**Evidence**

Unit 2 repro steps re-run against the change. The "before" is the output I posted in unit 2 (Windows 11 / PowerShell, Python 3.14.7, commit `2f4e82f`), plus the same commands on the base commit in a Linux container (Python 3.11.15). The "after" is the same on the branch, commit `03cb53d`.

Before (my unit 2 repro comment, Windows):

```
python scripts/run_evals.py
Running RAG evaluation suite...
Evaluation complete. Results written to eval_results.json
Exit code: 0

Test-Path eval_results.json   ->  False
Select-String -Pattern "EvalSuite" over *.py -> one hit: rag\evaluator\eval_suite.py, line 22
```

Before (Linux container, base commit):

```
$ git rev-parse HEAD
2f4e82f52efbcfcc57d65b3fa5348672163ca088
$ python scripts/run_evals.py
Running RAG evaluation suite...
Evaluation complete. Results written to eval_results.json
exit code: 0
$ ls eval_results.json
ls: cannot access 'eval_results.json': No such file or directory
$ grep -rn EvalSuite --include=*.py .
./rag/evaluator/eval_suite.py:22:class EvalSuite:
```

After (Windows 11 / PowerShell, Python 3.14.7, branch `fix/14-offline-eval-runner`, commit `03cb53d`):

```
> python --version
Python 3.14.7
> python scripts/run_evals.py; "exit code: $LASTEXITCODE"
Running RAG evaluation suite...
2026-10-04 21:31:04 [info     ] relevance_scored               avg_score=0.14285714285714285 chunks_count=2 query_len=7
2026-10-04 21:31:04 [info     ] faithfulness_checked           claims_count=2 score=1.0 supported_count=2
2026-10-04 21:31:04 [info     ] eval_suite_complete            faithfulness=1.0 overall=0.5714285714285714 relevance=0.14285714285714285
Scored 1 profile(s), mean overall 0.571
Evaluation complete. Results written to C:\Users\Parth\Documents\pathreview-ai301-fa26-s3\eval_results.json
exit code: 0
> Test-Path eval_results.json
True
> Get-Content eval_results.json
{
  "timestamp": "2026-10-05T01:31:04.944112+00:00",
  "profile_count": 1,
  "mean_relevance": 0.14285714285714285,
  "mean_faithfulness": 1.0,
  "mean_overall": 0.5714285714285714,
  "results": [
    {
      "profile": "basic_profile",
      "repo_count": 2,
      "relevance_score": 0.14285714285714285,
      "faithfulness_score": 1.0,
      "overall_score": 0.5714285714285714
    }
  ]
}
> python -m pytest tests/unit/test_run_evals.py -q
..                                                                                                                        [100%]
2 passed in 0.07s
```

Unit tests on the branch:

```
$ python -m pytest tests/unit/test_run_evals.py tests/unit/test_relevance_scorer.py tests/unit/test_faithfulness_checker.py -q
38 passed, 5 xfailed in 0.12s
```

(The 5 xfailed are existing strict xfails in the scorer tests. Nine other unit test files cannot be collected in the container because packages such as `jose`, `numpy` and `sqlalchemy` are not installed; they fail the same way on `main`.)

## Eval iterations

**Run history**

1. Full run (2026-10-03T05:43:29Z): 20/20 agree, every category matched (clear-accept 7/7, scope-creep 4/4, thread-convention 2/2, unbuildable 3/3, wrong-cause 4/4). This is the run saved in `eval-run.txt`, so the last score matches its agreement line (`agreement: 20/20 scored items`).

**Package analysis**

Package: `pkg-07` (processing/p5.js#8930, category wrong-cause). My rubric decided `reject`; the gold label is `reject`.

The candidate plan blames the Friendly Error System being tree-shaken out of the 2.x bundle. The package's repro evidence includes a control run in which an instance-method friendly error prints in the same build, so FES is present and the diagnosis is contradicted. My `grounded_diagnosis` check says the stated cause must not contradict any facts or control outputs in the repro evidence, so it grades `fail`, and my verdict rule (accept only if every required check passes) gives `reject`.

**Check rationale**

Check as it reads in `tools/plan-check/rubric.md`:

> | thread_and_repo_conventions | The candidate plan comment read against the issue thread highlights and repo-facts block (contributing policy / AI disclosure requirements) | The plan comment directly engages with any explicit maintainer direction in the thread AND complies with all repo policies, including mandatory AI-use disclosures if required by the repo-facts policy. | required |

It has two halves because the thread-convention category has two different kinds of miss: pkg-04 (the plan never engages the owner's direction in the thread) and pkg-20 (a good plan whose comment has no AI-use disclosure that the repo's policy requires). One check that reads the comment against both the thread highlights and the repo-facts block catches both, and the evidence column names those two sources so the grader reads the comment against them rather than judging it in isolation.

**Trade-offs**

This check gives up the ability to pass a plan it cannot verify. It needs the thread and the repo's policy as evidence, and the verdict rule treats `unclear` as a fail. I saw this when I ran the skill live on my #14 plan from a session that could not reach GitHub: the other three checks passed, but this one came back `unclear`, so the verdict was `reject` until the thread was readable. I accept that cost, because a plan I cannot check against the thread is not ready to post. It also only sees a disclosure policy that is stated in the repo-facts block, so a repo that expects disclosure only by custom would pass.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in `tools/plan-check/`.
