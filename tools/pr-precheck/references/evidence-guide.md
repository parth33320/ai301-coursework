# Evidence guide: where evidence lives in a PR package

This guide maps where evidence lives across eval bundles and live PR packages for each check family.

## Plan fidelity (harness category: silent-drift)

### Where it lives
- **Eval package**:
  - `Plan context`: Contains the accepted plan's scoped file list, boundary definitions, and repro requirements.
  - `Candidate PR Diff`: Contains the actual unified diff hunks and modified file paths.
  - `Candidate PR Description`: Contains the author's summary of changes.
- **Live mode**:
  - `beat-1-sandbox/unit-3/plan.md`: Read the files touched and `## Deviations` section.
  - Working tree diff (`git diff main...HEAD`): Shows the exact committed changes.
  - `beat-1-sandbox/unit-3/pr_draft.md`: Contains the PR title and description.

### What good looks like
Every modified file and functional change in the diff corresponds directly to a step in the accepted plan or an explicit deviation note. The PR description accurately reflects the diff without claiming fixes not present in the diff or omitting unannounced scope expansion (such as new flags, refactoring, or dependency bumps).

---

## Test evidence (harness category: not-tested)

### Where it lives
- **Eval package**:
  - `Candidate PR Test evidence`: Contains command output, before/after transcripts, or test suite run notes.
  - `Plan context`: Defines the expected reproduction steps and test verification commands.
- **Live mode**:
  - `beat-1-sandbox/unit-3/test_evidence.md`: Contains raw execution logs, before/after command outputs for the issue repro, and results from `make test-unit`, `make test-integration`, `make lint`, `make typecheck`.

### What good looks like
Test evidence shows explicit, observable before-and-after proof on the specific failing path described in the issue/plan (e.g., showing the bug failing before and passing after). Generic claims like "tests pass" or evidence exercising only an unchanged control path are insufficient.

---

## Diff quality (harness category: unreviewable)

### Where it lives
- **Eval package**:
  - `Candidate PR Diff` and `Candidate PR Commits`: Unified diff and commit messages.
- **Live mode**:
  - Working tree diff (`git diff main...HEAD`) and `git log main..HEAD`.

### What good looks like
The diff is concise, focused, and free of extraneous debris such as temporary debug prints (`console.log`, `eprintln!`), commented-out code experiments, unused helper functions (`allow(dead_code)`), formatting/indentation churn, or unrelated file changes. Commits have clear, descriptive titles.

---

## Standards and comms (harness category: standards-wall)

### Where it lives
- **Eval package**:
  - `Repo facts`: Details repository requirements (PR template checklists, contribution guidelines, AI-use disclosure policies).
  - `Candidate PR Description`: The candidate PR description text.
- **Live mode**:
  - `.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md` in the target repo.
  - `beat-1-sandbox/unit-3/pr_draft.md`: The draft PR description.

### What good looks like
All required sections of the repository's PR template are filled out with real, meaningful information (including issue link `Closes #XXXX` and checklist ticks). Any repository AI-use disclosure policy is explicitly satisfied (e.g. detailed AI assistance disclosure in 'Notes for Reviewers').
