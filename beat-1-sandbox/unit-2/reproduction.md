# Unit 2 — Claim and Reproduce

## Your identity upstream
*GitHub username*
parth33320

## Posted upstream
*Claim comment*
[Claim Comment Link](https://github.com/codepath/pathreview-ai301-fa26-s3/issues/14#issuecomment-5964737016)
> I'll investigate issue #14. The scripts/run_evals.py script claims to write eval_results.json but I need to verify whether EvalSuite is being called and if the output file is actually created. I'll run the script, check the exit code and file existence, and document my findings.

*Reproduction comment*
[Reproduction Comment Link](https://github.com/codepath/pathreview-ai301-fa26-s3/issues/14#issuecomment-5964754456)
> # Reproduction Report for Issue #14

## Environment
- OS: Windows NT 10.0.22621.0 (PowerShell)
- Python: 3.14.7
- Repository: fork of codepath/pathreview-ai301-fa26-s3, https://github.com/parth33320/pathreview-ai301-fa26-s3.git
- Commit: 2f4e82f52efbcfcc57d65b3fa5348672163ca088 (main)
- The issue states no OS or Python version, so there is no version difference to note.

## Steps
1. Clone and enter the repo:
   ```
   git clone https://github.com/parth33320/pathreview-ai301-fa26-s3.git
   cd pathreview-ai301-fa26-s3
   ```
2. Read the script: `Get-Content scripts\run_evals.py`. It defines `main()`, which prints a line, has a `# TODO: Implement eval runner` comment block, then prints a second line.
3. Search the repo's `.py` files for `EvalSuite`:
   ```
   Get-ChildItem -Recurse -Filter *.py | Select-String -Pattern "EvalSuite"
   ```
   One hit: `rag\evaluator\eval_suite.py`, line 22. No other `.py` file refers to it, including `scripts\run_evals.py`.
4. Run the script:
   ```
   python scripts/run_evals.py
   ```
   Output:
   ```
   Running RAG evaluation suite...
   Evaluation complete. Results written to eval_results.json
   ```
   Exit code: 0
5. Check for the output file: `Test-Path eval_results.json` returned `False`.
6. `make eval` was not run, because `make` is not installed on this Windows setup.

## Findings
- `scripts/run_evals.py` prints the two lines the issue describes and exits with code 0.
- No other `.py` file refers to `EvalSuite`, which is only defined in `rag/evaluator/eval_suite.py`.
- `eval_results.json` is not created, although the script's second line says it was written.
- I did not check the Makefile or the RAG Evaluation workflow.

## Run History
1. Full run on the revised rubric: 20/20 agreed, all five categories matched (clear-accept 8/8, disclosure 1/1, no-evidence 4/4, unfollowable-comms 3/3, wrong-target 4/4).
2. Confirming full run with --save-run: 20/20 again. Saved as eval-run.txt (header hashes: rubric e964e573..., evidence-guide cd623108..., SKILL f1b0abce...).
No revisions were needed after the first full run.

## Package Analysis (`pkg-07`)
Package: pkg-07
My rubric's verdict: accept    Gold verdict: accept (gold category: clear-accept)
Reasoning: All seven required checks passed on specific evidence in the package. environment_recorded passed because the report names p5.js 1.11.7, Chrome 139.0 on macOS 14.6, and explicitly notes the version delta ("The issue was filed against 1.9.4/1.10.0; it is still present on 1.11.7"). followable_and_public_steps and exact_trigger_syntax passed because the report gives a complete sketch loaded from the public CDN and the exact Japanese-first language steps, plus an English-first control run. matching_artifacts_shown passed because the console output shows "TypeError: Cannot read properties of undefined (reading 'replaceAll')", the error the issue describes. repo_ai_disclosure passed because p5.js's policy requires disclosure and the claim comment states that an AI assistant helped organize the report. Since every required check passed, my verdict rule gave accept, matching the gold label. I chose it to show why a package earns accept.

## Check Rationale
Check: specific_modest_claim_comment
Pass condition as it now reads in rubric.md: "The claim comment is human-voiced, refers to the issue's specifics, and promises only an investigation, not a fix, a date, or "assign me" boilerplate"
Why it reads this way: [one or two sentences, e.g. it keeps a claim to a promise of investigation, because the house rules ask for a claim that names the issue and doesn't over-promise.]

## Trade-offs
I worded repo_ai_disclosure to pass when the repo's stated policy requires nothing, and to fail only when the policy requires disclosure and the comments don't disclose. That keeps the check from rejecting good packages on repos with no AI policy, so it doesn't cost any clear-accept packages. The cost is that it can only see a policy stated in the repo-facts block. A repo that expects disclosure only by custom, or in a place the block doesn't quote, would pass when a stricter reviewer would reject. Because all seven checks are required, a single failed check rejects the whole package, so I also accept that one wrong grade on any check flips the verdict.
