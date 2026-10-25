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

**Evidence:** The tool's verdict on Issue #4092 (accept, moderate difficulty) named `tool_dead_code.py` as the affected file, which matched the root cause. Before the scope patch, the same issue would have been rejected because the issue description said "investigate the session lifecycle" without further details.

---

**repro-check**

**What it is, and the judgment hole I wrote:** A reproduction harness and grading rubric that checks whether a reproduction report is complete enough to post upstream. My judgment hole is the rubric's evidence checks: I wrote the pass conditions for environment specificity, step followability, and honest claim-matching.

**How it evolved, dated:** In October 2026 (Unit 6), I added the `gcplug.py` garbage-collection plugin to force deterministic GC sweeps during test execution, because the session leak only surfaced non-deterministically without it. Before the plugin, the reproduction was environment-dependent and would have failed the `followable_and_public_steps` check. After adding it, every run reproduced the `PendingRollbackError` within the first GC sweep.

**Evidence:**
```
ERROR sqlalchemy.pool.impl.StaticPool:base.py:1070 The garbage collector is trying
to clean up non-checked-in connection <AdaptedConnection <aiosqlite.core.Connection
object>>, which will be terminated.
sqlalchemy.exc.PendingRollbackError: Can't reconnect until invalid transaction is
rolled back.
```
This output — captured from `PYTHONPATH=. pytest tests/unit/server/mcp/test_finding_registry_visibility.py -p gcplug` on my fork — is the exact failing behavior the issue reported, produced deterministically.

---

**pr-precheck**

**What it is, and the judgment hole I wrote:** A ship-gate validation script and manifest (`ship-manifest.md`) that enforces pre-flight checks before a branch is submitted as a PR. My judgment hole is the manifest: I wrote the rows that decide which checks must pass, with dated reasons for anything excluded.

**How it evolved, dated:** In October 2026 (Unit 7), I created the initial manifest with three rows: MCP visibility tests, ruff lint, and mypy type check. The ruff check initially flagged a line-length warning in my patch file; I fixed it and added a note to the manifest that line-length warnings are blocking, not advisory. The gate then passed cleanly.

**Evidence:**
```
PYTHONPATH=. pytest tests/unit/server/mcp/test_finding_registry_visibility.py -p gcplug
=================== 8 passed in 3.93s ===================
```
Gate ran on branch `fix/issue-4092`, October 2026. Zero connection leak warnings, zero `PendingRollbackError` exceptions.

## Inventory

- **plan-check:** Used to grade my implementation plan before posting the plan comment on Issue #4092; helped confirm that `call_resolution_bases` scope was correctly bounded to the session block.
- **pr-monitor:** Deployed in Unit 8 to check PR #4181 state; confirmed CI passing and no maintainer feedback within the first week; held back a nudge per my 7-day threshold rule.

## The Arc

**Beat 1**
Date: September 2026
Friction: My issue-scout scope check was too strict for real-world issues — it rejected Issue #4092 because the description said "investigate" rather than naming a specific line count, even though the issue was actually well-scoped.
Change: Applied the `scope-feasible` patch from Unit 6 materials, replacing the binary scope check with one that emits a difficulty note.
Result: The scout accepted Issue #4092 with a moderate-difficulty note, and I was able to claim it with confidence.

**Beat 2**
Date: October 2026
Friction: The session leak in `get_dead_code` only surfaced non-deterministically in CI, making it hard to reproduce locally without a forcing function. Standard pytest runs passed most of the time.
Change: Added the `gcplug.py` garbage-collection plugin from the issue's repro guide, forcing GC sweeps between test blocks to expose unclosed sessions deterministically.
Result: Every local run reproduced the `PendingRollbackError` within seconds, giving me concrete terminal output to attach to the PR description.

**Beat 3**
Date: October 2026
Friction: The pr-precheck gate flagged a ruff line-length warning in my patch file that I hadn't noticed during manual review.
Change: Fixed the line-length issue and added a manifest note that ruff warnings are blocking.
Result: Gate passed on the next run; PR #4181 opened with a clean gate log and all 8 tests green.

## Limitations and What's Next

The toolkit handles the single-issue contribution loop well, but it has gaps when the problem space is larger. The issue-scout has no signal for repositories where the test setup requires a non-obvious plugin or environment variable — the pytest-asyncio gap I hit this term is a direct example. I would add a "test-setup complexity" signal that scores how much setup the repo's `CONTRIBUTING.md` describes before running tests.

The repro-check rubric is still manually driven; it cannot automatically compare a candidate's terminal output against the issue's expected output. A structured output-diff check would cut the back-and-forth during calibration runs.

The pr-precheck manifest is per-repo, which means starting from scratch for every new contribution. I would build a base manifest template with the most common check patterns (lint, type check, targeted test module) pre-filled.

**Tool limits.**
issue-scout: Cannot detect missing test dependencies or non-obvious environment setup requirements from issue descriptions alone.
repro-check: Cannot automatically diff candidate terminal output against the issue's expected failure output; comparison is manual.
pr-precheck: Manifest is per-repo and starts from scratch each contribution; no reusable base template exists yet.
