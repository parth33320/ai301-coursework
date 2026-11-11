# Toolkit Portfolio

Path: `beat-3-portfolio/toolkit-portfolio.md`

This file is Assignment 6, and it is graded at the path above; a copy kept anywhere else in
the repository is not read.

**Student:** Parth Patel

## Featured Tools

I fixed a mistake in a software (Python MCP server) where a database connection stayed open when it should have closed. This mistake caused the automated testing system to fail randomly. I chose to show three tools because they helped me find (issue-scout), verify (repro-check), and fix (pr-precheck) that specific mistake. These three tools work together like a step-by-step checklist to guide a person from picking a software problem to submitting the final fix.

**issue-scout**

**What it is, and the judgment hole I wrote:** A triage playbook and rubric I configured to evaluate candidate open-source issues, scoring them on maintainer activity, scope feasibility, and dependency risk. The judgment hole is the rubric: I wrote the checks that decide whether an issue is a reasonable first-contribution target given my profile.

**How it evolved, dated:** In October 2026, my initial rubric had a scope check that was too strict — it rejected most real-world issues because their scope descriptions were vaguer than the sandbox packages. I replaced it with the `scope-feasible` row from the Unit 6 patch kit, which adds a difficulty-note output instead of a binary reject. I also added a note flagging the project for missing test dependencies, after the pytest-asyncio gap in the repowise environment cost me an hour during issue reproduction.

**Evidence:**
```json
{
  "item": "https://github.com/repowise-dev/repowise/issues/4092",
  "checks": [
    {"name": "repo_alive",           "grade": "pass",
     "evidence": "repo not archived; pushed 2026-11-11; labels applied recently — well within 180-day window"},
    {"name": "maintainer_responsive","grade": "pass",
     "evidence": "good-first-issue label applied (requires Owner/Member/Collaborator action); active commit cadence satisfies condition (c)"},
    {"name": "scope_bounded",        "grade": "pass",
     "evidence": "issue asks for one call (call_resolution_bases) moved inside the async-with block; single file, one function, no megaissue structure"},
    {"name": "unclaimed",            "grade": "pass",
     "evidence": "assignees: none; no open linked PR; no prior claim comment in thread before 2026-10-18"},
    {"name": "ai_policy_allows",     "grade": "pass",
     "evidence": "CONTRIBUTING.md has no AI prohibition; standard code review requirements only"},
    {"name": "fit_signals",          "grade": "pass",
     "evidence": "labels: bug, good first issue; issue body names the exact line and expected behavior (zero leak warnings after fix)"}
  ],
  "verdict": "accept"
}
```
The scout's difficulty note flagged the async session boundary as the risk surface and named `tool_dead_code.py` as the affected file, which matched the actual root cause exactly.

---

**repro-check**

**What it is, and the judgment hole I wrote:** A reproduction harness and grading rubric that checks whether a reproduction report is complete enough to post upstream. My judgment hole is the rubric's evidence checks: I wrote the pass conditions for environment specificity, clear steps, and issue reproducibility.

**How it evolved, dated:** In October 2026 (Unit 6), I added the `gcplug.py` garbage-collection (GC) plugin to force GC sweeps during test execution. Before the plugin, issue reproduction was random due to environment-dependence and would have failed the `followable_and_public_steps` check. After adding it, every run reproduced the `PendingRollbackError` within the first GC sweep.

**Evidence:**
```
ERROR sqlalchemy.pool.impl.StaticPool:base.py:1070 The garbage collector is trying
to clean up non-checked-in connection <AdaptedConnection <aiosqlite.core.Connection
object>>, which will be terminated.
sqlalchemy.exc.PendingRollbackError: Can't reconnect until invalid transaction is
rolled back.
```
This output — captured from `PYTHONPATH=. pytest tests/unit/server/mcp/test_finding_registry_visibility.py -p gcplug` on my fork — is the issue's deterministically produced bug.

---

**pr-precheck**

**What it is, and the judgment hole I wrote:** A validation script and rules (`ship-manifest.md`) that enforces checks before a branch is submitted as a PR. I wrote the rows that decide which checks must pass, with reasons for excluded checks.

**How it evolved, dated:** In October 2026 (Unit 7), I created the initial manifest with four rows: MCP visibility tests, full pytest suite, ruff lint, and npm build. I ran the targeted test suite and ruff on the patched file; both passed. I then noticed the manifest file had no place in the upstream PR and added a removal commit before opening PR #4181 so the diff would show only the functional change.

**Evidence:**
```
$ PYTHONPATH=. pytest tests/unit/server/mcp/test_finding_registry_visibility.py -p gcplug -v
...
8 passed, 46 warnings in 8.40s

$ uv run ruff check packages/server/src/repowise/server/mcp_server/tool_dead_code.py
All checks passed!
```
Gate run on branch `fix/issue-4092`, November 2026. Zero connection leak warnings, zero `PendingRollbackError` exceptions, ruff clean.

## Inventory

- **plan-check:** Used to grade my implementation plan before posting the plan comment on Issue #4092; helped confirm that `call_resolution_bases` fix stayed within the correct block.
- **pr-monitor:** Deployed in October 2026 to check PR #4181 state; confirmed no maintainer feedback had arrived and that the upstream repo does not run GitHub Actions on fork pull requests; local gate checks (8 passed, ruff clean) are the confirmation the fix is sound.

## The Arc

**Beat 1**
Date: October 2026
Friction: My issue-scout scope check was too strict for real-world issues — it rejected Issue #4092 because the description said "investigate" rather than naming a specific line count, even though the issue was actually well-scoped.
Change: Applied the `scope-feasible` patch from Unit 6 materials, that changes the binary accept/reject decision with a helpful note about the issue's difficulty level.
Result: The scout accepted Issue #4092 with a moderate-difficulty note, and I was able to claim it.

**Beat 2**
Date: October 2026
Friction: The database connection error (session leak) in `get_dead_code` happened randomly in the project, making it hard to reproduce locally. Standard pytest runs passed most of the time.
Change: Added the `gcplug.py` garbage-collection plugin from the issue's repro guide, forcing GC sweeps between test blocks to expose unclosed sessions deterministically.
Result: Every local run reproduced the `PendingRollbackError`, giving me terminal output to attach to the PR description.

**Beat 3**
Date: October 2026
Friction: The initial ship-manifest included the course gate file (ship-manifest.md) in the patch, which had no place in an upstream pull request to repowise-dev/repowise.
Change: Added a removal commit to strip ship-manifest.md from the branch before opening PR #4181, keeping the diff to the single functional change in tool_dead_code.py.
Result: PR #4181 opened with a clean diff: one file changed, targeted test suite (8 passed) and ruff (all checks passed) confirmed the fix.

**Beat 4**
Date: October 2026
Friction: After opening PR #4181, the upstream repo showed no GitHub Actions CI results — repowise-dev/repowise does not run Actions on fork pull requests, so there was no automated signal on whether the patch was healthy.
Change: Ran the pr-monitor playbook to read PR state; local checks (8 tests passed, ruff clean) are the confirmation the fix is sound. Held back a maintainer nudge — silence at this stage is a normal open-source state per CONTRIBUTING.md.
Result: Documented that check evidence comes from the local gate, not upstream CI, and updated the story accordingly.

## Limitations and What's Next

The toolkit handles single-issue contribution task well, but not large tasks. The issue-scout has no signal for repositories where the test setup requires an additional plugin or environment variable — the pytest-asyncio gap I hit this term is a direct example. I would add a "test-setup complexity" signal that scores how much setup the repo's `CONTRIBUTING.md` describes before running tests.

The repro-check rubric is still manually driven; it cannot automatically compare my bug report against the issue's expected output. A structured output-diff check would save time spent on manually comparing bug reproduction terminal outputs.

The pr-precheck manifest is per-repo, so I must write a new checklist file for every new contribution. I would build a base manifest template with the most common check patterns (lint, type check, targeted test module) pre-filled.

**Tool limits.**
issue-scout: Cannot detect missing test dependencies or non-obvious environment setup requirements from issue descriptions alone.
repro-check: Cannot automatically diff candidate terminal output against the issue's expected failure output; comparison is manual.
pr-precheck: Manifest is per-repo and starts from scratch each contribution; no reusable base template exists yet.
