# Rubric: is this pull request ready to submit?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| plan_fidelity | Diff hunks and description read against plan context (scope, files, and deviation notes) | Every changed file and functional diff hunk falls strictly within the plan's stated file list/scope or an explicit plan deviation note; the description does not claim changes that are missing from the diff, nor does the diff contain unannounced scope/feature drift | required |
| test_decisive | Candidate PR test evidence read against the plan's test plan and issue repro steps | Test evidence demonstrates observable before-and-after verification on the specific failure path/scenario named in the plan's test plan and issue report; repo checks/test suite run results are shown when required | required |
| diff_cleanliness | Candidate PR unified diff and commit list | The diff contains no debug print statements (`console.log`, `eprintln`, `klog` debug), dead code, commented-out code experiments, unneeded dependency bumps, or unrelated formatting/import churn | required |
| template_and_disclosure | Candidate PR description read against repo facts (PR template sections and AI policy) | Every required section in the repo's PR template is filled with meaningful context (e.g. Closes # issue link, checklist items ticked/addressed), and the repo's stated AI-use disclosure policy is fully met when applicable | required |

## Verdict rule

Accept if and only if all required checks (`plan_fidelity`, `test_decisive`, `diff_cleanliness`, `template_and_disclosure`) pass.
Preferred checks (if any) do not alter the verdict.
Any check with a grade of `unclear` or `fail` causes the verdict to be `reject`.
