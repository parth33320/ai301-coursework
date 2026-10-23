# Contribution: get_dead_code queries a closed session, leaking a pooled connection and making test_finding_registry_visibility.py flaky

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** 1
**Student:** Parth Patel
**GitHub Username:** parth33320
**Issue:** https://github.com/repowise-dev/repowise/issues/4092
**Pull Request:** https://github.com/repowise-dev/repowise/pull/4181

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `1` unless you make more than one contribution this term; it
just numbers them.

---

## Toolkit

Every story section below, from Why I Chose This Issue through Pull
Request, ends with a Toolkit block: which tool did that phase's work
and how you used it, what it produced, what difficulties or
shortcomings it presented, and what you changed or would change
because of them. Together the blocks are the thread the rubric's
Toolkit points read, and they are the raw material for your unit-9
portfolio, so keep them current as you work rather than
reconstructing them later.

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

This section also keeps one short dated entry per week of tool work,
starting below: what you built or changed that week, what broke, and
what you changed because of it. The blocks tell the story where it
happened; these entries are the toolkit's own timeline.

All four entries are graded. Keep every heading below, and fill each
one in the week it belongs to.

**Unit 5: Configured issue-scout with real-world Python search strategies**

Installed the issue-scout skill overlay and wrote custom `gh search issues` queries targeting Python backend and async database bugs. The overlay installation cleared out `rubric.md` and `evidence-guide.md` from the global skill directory, causing scan evaluation errors on the first run. I restored both files from the course repo's `tools/issue-select/` history and re-ran the scan successfully, surfacing Issue #4092 in repowise-dev/repowise as the top candidate.

**Unit 6: Established repo-memory file and executed first local reproduction run**

Configured `CLAUDE.local.md` in my fork and wrote the voice guard's upstream rules. Initial pytest runs failed because pytest-asyncio was not installed in the codespace environment. Installing pytest-asyncio and adding the `gcplug.py` garbage-collection plugin to the repo root allowed me to reproduce the connection leak deterministically on the first clean run.

**Unit 7: Built ship-gate manifest and ran pr-precheck on fix/issue-4092**

Created `ship-manifest.md` at the repository root and ran the pr-precheck gate against the branch. The gate passed on the first try: all 8 MCP visibility tests passed in 3.93s with zero connection leak warnings. No blockers arose during this run, confirming the session lifecycle fix was sound before opening the PR.

**Unit 8: Ran PR-monitor and reviewed open PR state**

Used the PR-monitor playbook to check the state of PR #4181. The monitor confirmed all GitHub CI checks (Python tests, lint, type checking) were passing and that no maintainer feedback had arrived yet. I held back a follow-up nudge because my own threshold rule says to wait at least 7 days before pinging — silence at submission is a normal open-source state.

---

## Why I Chose This Issue

Using the issue-scout search strategy I configured in Unit 5 — targeting Python backend repositories with async database or session-management bugs — I surfaced Issue #4092 in repowise-dev/repowise. The issue addresses database session lifecycle management: specifically, querying a closed `AsyncSession` inside `get_dead_code` causes pooled connection leaks and intermittent test failures under pytest-xdist. My scout graded it accept, rating it moderate difficulty because it involves async context managers and a shared StaticPool, but the affected file boundary was narrow and the reproduction path was spelled out clearly in the issue.

I chose it because it fits my background in Python backend architecture and SQLAlchemy-based database layers, and because I wanted to learn how async session finalization interacts with Python's garbage collector in a real production codebase — something I had read about but never debugged hands-on. Honest read of the scout: it correctly identified the issue as claimable, but its scope-feasibility check didn't flag the pytest-asyncio dependency gap, which cost me an hour during reproduction. I added a note in `scan-notes.md` to weight setup-dependency signals more heavily in future scans.

Claim comment: https://github.com/repowise-dev/repowise/issues/4092#issuecomment-6246896200

**Toolkit**

- Tool: issue-scout (custom `gh search issues` queries + rubric targeting Python async/database issues), run from `~/.claude/skills/issue-scout/`
- What it produced: A scan verdict of `accept` on Issue #4092 with a moderate difficulty note, flagging the async session boundary as the risk surface
- Difficulties: Overlay installation cleared `rubric.md` and `evidence-guide.md`; first scan failed with missing-file errors
- What changed: Restored both files from course repo history; added a check for missing skill dependencies to `scan-notes.md`

---

## Understanding the Issue

**Problem Description**

In `packages/server/src/repowise/server/mcp_server/tool_dead_code.py`, the `get_dead_code` function executes database queries inside an async session context manager, but later calls `call_resolution_bases` over 55 lines after the session block has already closed.

**Expected Behavior**

All database queries and persistence interactions for a given request should occur entirely while the database session is open and active within its context block, returning primitive data structures for subsequent logic and preventing resource leaks.

**Current Behavior**

Executing a query on a closed SQLAlchemy `AsyncSession` auto-begins a new transaction, checks out a database connection from the connection pool (`StaticPool`), and fails to return it. When Python's garbage collector reclaims the session object, SQLAlchemy terminates the underlying connection, causing intermittent failures like `sqlite3.OperationalError` or `PendingRollbackError` across tests.

**Affected Components**

- `packages/server/src/repowise/server/mcp_server/tool_dead_code.py` (`get_dead_code` function)
- `packages/server/src/repowise/server/mcp_server/_basis.py` (`call_resolution_bases` and `_query_grouping`)

**Toolkit**

- Tool: Repository code inspector and search tools
- What it produced: Exact localization of the session lifecycle mismatch between the context manager exit and the deferred query call
- Difficulties: Tracing deferred execution paths across asynchronous helper functions
- What changed: Nothing; the code path was clearly mapped by the issue description

---

## Reproduction Process

**Environment Setup**

OS: Linux (GitHub Codespaces / Ubuntu)

Python Version: 3.14.2

Package Manager: pip / pytest with pytest-asyncio and anyio

Setup: Cloned the fork of repowise, created a dedicated branch `fix/issue-4092`, installed local dependencies in editable mode, and added the custom `gcplug.py` script to force garbage collection passes between execution blocks.

**Steps to Reproduce**

1. Create a local file named `gcplug.py` in the repository root containing the garbage collection wrapper fixture as specified in Issue #4092.
2. Run the target test module with the garbage collection plugin enabled:
   ```bash
   PYTHONPATH=. pytest tests/unit/server/mcp/test_finding_registry_visibility.py -p gcplug
   ```
3. Observe the StaticPool warning and test errors:
   ```
   ERROR sqlalchemy.pool.impl.StaticPool:base.py:1070 The garbage collector is trying to clean up non-checked-in connection...
   sqlalchemy.exc.OperationalError: (sqlite3.OperationalError) no such table: repositories
   sqlalchemy.exc.PendingRollbackError: Can't reconnect until invalid transaction is rolled back.
   ```

**Reproduction Evidence**

- **Commit showing reproduction:** https://github.com/parth33320/repowise/commit/e2d5bae477eed2cd693bc680670a8676566f97fa
- **My findings:** The race condition reliably surfaces when Python's garbage collector sweeps unclosed sessions that performed post-closure queries, corrupting the shared StaticPool state in in-memory SQLite test suites.

**Toolkit**

- Tool: Pytest testing framework combined with the custom gcplug diagnostic plugin
- What it produced: Concrete reproduction logs and stack traces confirming the session leak
- Difficulties: Replicating asynchronous garbage collection timing without a dedicated plugin hook
- What changed: Integrated `gcplug.py` to force deterministic GC sweeps during test execution

---

## Solution Approach

**Analysis**

The root cause is querying a closed SQLAlchemy session. Because `AsyncSession.execute()` automatically begins a new transaction on closed sessions if not properly managed, calling `call_resolution_bases` after the `async with get_session(...) as session:` block checks out a connection that is never returned to the pool.

**Proposed Solution**

Move the invocation of `call_resolution_bases` directly inside the active `async with get_session(ctx.session_factory) as session:` block in `packages/server/src/repowise/server/mcp_server/tool_dead_code.py`. Assign the result to a local variable while the session is open, and ensure `session` is never referenced or queried after the context manager exits.

**Implementation Plan**

1. Open `packages/server/src/repowise/server/mcp_server/tool_dead_code.py`
2. Move the `call_resolution_bases` call inside lines 284–297 within the active session block
3. Update summary assignment to use the pre-fetched result variable
4. Run regression tests to verify that all tests pass cleanly

**Toolkit**

- Tool: Python editor and refactoring tools in VS Code Codespaces
- What it produced: A clean, localized code patch within `tool_dead_code.py`
- Difficulties: Ensuring asynchronous context variable scopes remained intact
- What changed: Relocated the query call entirely inside the existing context manager block

---

## Testing Strategy

To verify the fix, we re-run the exact reproduction command against the modified codebase:

```bash
PYTHONPATH=. pytest tests/unit/server/mcp/test_finding_registry_visibility.py -p gcplug
```

Outcome: All 8 tests passed successfully in 3.93s with zero connection leak warnings, zero operational errors, and zero pending rollback failures.

**Toolkit**

- Tool: Pytest runner with gcplug and pytest-asyncio
- What it produced: 100% test pass confirmation (8 passed in 3.93s)
- Difficulties: None encountered post-fix
- What changed: Validated that session lifecycle hygiene resolves test flakiness completely

---

## Implementation Notes

**October 19, 2026**

Investigated Issue #4092 and reproduced the pooled connection leak using the gcplug plugin. Identified that `call_resolution_bases` was being queried outside the active `async with get_session(...)` block in `tool_dead_code.py`. Implemented the fix by moving `call_resolution_bases` inside the active session block. Verified success with `PYTHONPATH=. pytest tests/unit/server/mcp/test_finding_registry_visibility.py -p gcplug`, achieving 8 passing tests.

**Toolkit**

- Tool: Git version control and Python test suite
- What it produced: Committed code fix and verified test suite execution logs
- Difficulties: None
- What changed: Applied the session context correction cleanly

---

## Pull Request

**PR Link:** https://github.com/repowise-dev/repowise/pull/4181

**PR Description:** Fixes Issue #4092 by ensuring `call_resolution_bases` is queried within the active `async with get_session(...)` block inside `get_dead_code` (`packages/server/src/repowise/server/mcp_server/tool_dead_code.py`). This prevents closed-session auto-begin behavior, eliminates pooled connection leaks during garbage collection, and resolves test flakiness in `test_finding_registry_visibility.py`. Reproduction confirmed using the `gcplug.py` garbage-collection plugin: before fix, `PendingRollbackError` and `StaticPool` connection termination warnings appear; after fix, all 8 MCP visibility tests pass in 3.93s with zero warnings.

**Maintainer Feedback:**

- October 2026: Awaiting review; none yet.

**Status:** Awaiting review

**Toolkit**

- Tool: GitHub CLI and web interface
- What it produced: Submitted pull request referencing Issue #4092
- Difficulties: None
- What changed: Linked PR description directly to reproduction logs

---

## Learnings and Reflections

**Technical Skills Gained**

Gained deep practical understanding of SQLAlchemy `AsyncSession` lifecycle management, particularly how closed sessions handle deferred `execute()` calls by auto-beginning transactions and checking out pooled connections (`StaticPool`), and how asynchronous garbage collection finalizers interact with unmanaged database handles.

**Challenges Overcome**

Successfully diagnosing a flaky test caused by asynchronous garbage collection timing, and utilizing a diagnostic pytest plugin (`gcplug`) to deterministically reproduce and verify the connection leak.

**What I'd Do Differently Next Time**

Establish database session boundary checks earlier during code reviews to catch deferred queries outside active context managers before they reach integration test suites.

---

## Resources Used

- [SQLAlchemy AsyncSession documentation](https://docs.sqlalchemy.org/en/20/orm/extensions/asyncio.html) — used to understand how `AsyncSession.execute()` auto-begins a transaction on a closed session
- [Issue #4092 discussion thread](https://github.com/repowise-dev/repowise/issues/4092) — the repro guide and `gcplug.py` snippet in the issue body
- [pytest-asyncio docs](https://pytest-asyncio.readthedocs.io/) — followed to install and configure async test mode in the codespace
