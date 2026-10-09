# Procedure: how this tool grades a PR package

## Read order

To evaluate a PR package without bias, read the package components in the following strict order:

1. **Repo facts & Issue context**: Read the repository facts (PR template asks, contribution policy, AI disclosure requirements) and the issue description/thread highlights to understand the problem and requirements.
2. **Plan context**: Read the accepted plan, including its scoped file list, boundary definitions, test plan, and any explicit `## Deviations`.
3. **Candidate PR Diff & Commits**: Read the unified diff and commit history. Compare the touched files and code logic directly against the plan's scope before reading the candidate PR description.
4. **Candidate PR Description & Test evidence**: Read the candidate PR title, description, and test evidence section. Check whether description claims match the diff and whether test evidence satisfies the plan's test plan.

This read order ensures that the code changes (`diff`) are evaluated directly against the accepted specification (`plan`) before reading any self-reported claims in the PR description.

## Evidence gathering

Gather evidence for each check family as follows:

1. **Plan fidelity (`silent-drift`)**:
   - Extract the list of target files and scope boundaries from `Plan context`.
   - Extract all modified/added files from `Candidate PR Diff`.
   - Verify that every modified file is listed in the plan or explained in a deviation note.
   - Compare description feature claims against diff hunks to ensure no claimed changes are absent from diff, and no unannounced features/flags are added.
   - Record one line citing any unannounced file/feature or description contradiction.

2. **Test evidence (`not-tested`)**:
   - Extract the required test cases, repro commands, and expected results from `Plan context` and `Issue`.
   - Extract the output from `Candidate PR Test evidence`.
   - Verify that test output shows observable before-and-after results for the specific bug repro (not just unchanged controls or vague "tests pass" statements).
   - Record one line citing the before/after proof or naming the missing repro execution.

3. **Diff quality (`unreviewable`)**:
   - Inspect all hunks in `Candidate PR Diff`.
   - Scan for temporary debug prints (`eprintln!`, `console.log`, `print`), commented-out trial code, dead functions (`allow(dead_code)`), formatting/indent churn, or unrelated file changes.
   - Record one line citing any debris hunk or confirm clean diff.

4. **Standards and comms (`standards-wall`)**:
   - Extract required template sections and policies from `Repo facts`.
   - Check `Candidate PR Description` for missing template sections, unclosed issue references (`Closes #XXXX`), or missing AI-use disclosures required by the repo's stated policy.
   - Record one line citing the missing section or policy non-compliance.

## Check execution

Execute each check in the order defined in `rubric.md`:
1. Grade `plan_fidelity`.
2. Grade `test_decisive`.
3. Grade `diff_cleanliness`.
4. Grade `template_and_disclosure`.

If evidence for a check is missing or incomplete in the package, grade that check as `fail` (or `unclear`, which evaluates as `fail`). Do not re-read or infer unstated facts outside the provided package.

## Verdict assembly

Assemble the final output:
1. Apply the verdict rule from `rubric.md`: if every required check is `pass`, set `verdict` to `accept`. Otherwise, set `verdict` to `reject`.
2. For the output JSON block, format each check result with its check `name`, `grade` (`pass`, `fail`, or `unclear`), and a single-line `evidence` string citing the key quote or fact from the gathering stage.
3. If `reject`, ensure the human-readable summary preceding the JSON block explicitly highlights the first failing required check in rubric order and its evidence.
4. Output the final JSON block as the last block in the response.
