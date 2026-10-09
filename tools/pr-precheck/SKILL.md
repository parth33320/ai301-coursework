---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

This tool answers exactly one question: **is this pull request ready to submit?**

A PR package consists of a candidate pull request (title, description, commit list, unified diff, test evidence), read against the accepted plan it claims to implement (including any posted deviation notes) and the issue context that plan belongs to.

## Inputs and modes

The tool operates in two distinct modes:

1. **Live mode**: Used when checking a student's own submission before opening a PR.
   - **Inputs**: `plan.md` (with its `## Deviations` section), branch diff relative to default branch (`git diff main...HEAD`), draft PR title and description (`pr_draft.md`), test evidence (`test_evidence.md`), issue thread/context, repository PR template, and repository contribution policy (`CONTRIBUTING.md` / `AI_POLICY.md`).
   - House-chain students read the house plan, house repro pack, and house branch diff instead.

2. **Eval mode**: Used when evaluating frozen package bundles (e.g. `pkg-XX.md` / `pkg-XX.json`).
   - **Inputs**: The package bundle text is the whole world. Nothing outside the bundle is read or fetched over the network.
   - `scope.md` and `voice-guide.md` are completely ignored. Every check is evaluated and the full rubric verdict rule is applied.

## The scope seam (live mode only)

In live mode, read `scope.md` before performing any checks:
- Verify that the target repository matches the `Repo:` line in `scope.md`.
- If the `Repo:` line contains an unfilled placeholder (such as `<ORG>/<PATH-REVIEW-REPO>`), halt immediately without grading and instruct the user to update `scope.md` with their section's Path Review repository.
- Refuse to grade pull requests targeting any repository outside the scoped repository.
- In eval mode, ignore `scope.md` entirely.

## The voice seam (live mode only)

In live mode, read `voice-guide.md` to evaluate the draft PR title and description:
- Compare `pr_draft.md` against the rules in `voice-guide.md`.
- Report any violated voice rules in the human-readable output summary preceding the JSON block.
- Voice guide violations do not alter the final `accept`/`reject` verdict unless a specific check in `rubric.md` references voice guide compliance.
- In eval mode, ignore `voice-guide.md` entirely.

## Component reads

Connect the tool components as follows:
- `rubric.md` defines the list of checks, evidence sources, pass conditions, weights, and the verdict rule.
- `references/evidence-guide.md` maps where each evidence family lives in live and eval packages and defines what good evidence looks like.
- `procedure.md` defines the step-by-step operating procedure for reading artifacts, gathering evidence, grading checks, and assembling the verdict.
- **Refusal rule**: If `rubric.md` or `procedure.md` lacks filled-in content, or if `procedure.md` is missing a required step, refuse to grade and report the missing component. Do not invent steps or checks at runtime.

## Verdict and output

The verdict space is binary: `accept` (ready to submit) or `reject` (hold).

The tool reply must end with the exact fenced JSON block schema shown below, valid and last, with no text following it:

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

Follow these standing rules for all grading:
1. **Evidence first**: Every check grade must cite the specific fact or quote that decided it.
2. **Grade the thing, not the polish**: Evaluate the code changes and test proof against the plan and issue, not formatting or superficial style.
3. **The rubric decides, not the run**: Apply the pass conditions as written in `rubric.md`.
4. **The procedure decides how, not the run**: Follow `procedure.md` as written without improvising.
5. **Unclear defaults to fail**: Any claim that cannot be verified from the package evidence must be graded as `fail` (or `unclear`, which counts as `fail` under the verdict rule).
