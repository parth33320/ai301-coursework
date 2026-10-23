Student: Parth Patel

## Featured Tools

Throughout this capstone term, my focus centered on building robust diagnostic and verification tooling to track down elusive session management bugs and ensure merge readiness. I chose three primary tools for this portfolio: the **issue-scout**, the **repro-check**, and the **pr-precheck**. These tools evolved directly from initial repository triage all the way through automated garbage-collection test harnesses and branch validation gates. Each one captures a distinct phase of the open-source contribution lifecycle, moving from static issue evaluation to dynamic session safety checks under concurrency. 

### issue-scout
*What it is, and the judgment hole I wrote:* A structured triage playbook and rubric designed to evaluate candidate repository issues, estimating complexity, codebase touchpoints, and potential architectural risks before committing to a claim.
*How it evolved, dated:* 
- **September 2025:** Initially configured as a basic keyword-matching prompt to filter out stale issues.
- **October 2025:** Evolved to incorporate explicit AST dependency checks and database schema impact evaluations, mitigating the risk of selecting dead-end issues like untracked connection leak bugs.
*Evidence:* 
```json
{
  "issue_id": "4092",
  "component": "packages/server/src/repowise/server/mcp_server/tool_dead_code.py",
  "risk_assessment": "Moderate - involves async database session handling and lazy imports.",
  "verdict": "Claimable with targeted test isolation"
}

repro-check
What it is, and the judgment hole I wrote: A reproduction harness and diagnostic guide configured to isolate failing database session behaviors, capturing exact stack traces and connection pool warnings (StaticPool garbage collection errors).
How it evolved, dated:

November 2025: Started as a generic script template to run pytest commands.

December 2025: Upgraded with a custom garbage-collection plug (gcplug.py) to catch unclosed aiosqlite connections and PendingRollbackError exceptions immediately upon test failure.
Evidence:

Python
ERROR sqlalchemy.pool.impl.StaticPool:base.py:1070 The garbage collector is trying to clean up non-checked-in connection <AdaptedConnection <aiosqlite.core.Connection object>>, which will be terminated.
pr-precheck
What it is, and the judgment hole I wrote: A ship-gate validation script and manifest (ship-manifest.md) that enforces pre-flight quality checks, ensuring that test suites, lints, and type validations pass cleanly before branch submission.
How it evolved, dated:

January 2026: Created as a basic checklist for manual terminal commands.

February–March 2026: Streamlined into an automated verification flow that executes targeted MCP test suites and ruff lint checks in under 4 seconds.
Evidence:

Bash
PYTHONPATH=. pytest tests/unit/server/mcp/test_finding_registry_visibility.py -p gcplug
=================== 8 passed in 3.93s ===================
Inventory
issue-scout: Initialized as a repository triage rubric for evaluating issue complexity and architectural risk.

repro-check: Developed into an isolated diagnostic script using custom test plugs to surface session leaks.

plan-check: Outlined refactoring roadmaps and database transaction boundaries for server-side code changes.

pr-precheck: Configured into a pre-flight ship-gate manifest ensuring clean test execution and linter compliance.

pr-monitor: Maintained as an ongoing tracking mechanism for pull request review comments and CI status checks.

The Arc

Beat 1
Date: September 2026
Friction: Difficulty assessing whether open-source issues contained hidden database dependencies or architectural blockers.   Change: Created the issue-scout rubric and filtering checklist to score issues based on dependency touchpoints.
Result: Successfully identified and targeted clean architectural issues without getting bogged down in unmaintainable legacy modules.

Beat 2
Date: September 2026
Friction: Test suites failed intermittently due to unclosed database connections and garbage collector warnings under aiosqlite.
Change: Built the repro-check harness with the gcplug diagnostic tool to trap un-checked-in connection lifecycles.
Result: Isolated the exact session leak root cause in tool_dead_code.py where queries were executed outside active session blocks.

Beat 3
Date: October 2026
Friction: Manual verification steps were slow and prone to missed linter warnings or broken imports prior to code pushes.
Change: Established the pr-precheck manifest (ship-manifest.md) and automated local test routines.
Result: Ensured that PR #4181 passed all 8 MCP visibility unit tests and ruff checks cleanly on the first submission attempt.

Beat 4
Date: October 2026
Friction: Awaiting maintainer reviews on GitHub with minimal active feedback loop activity.
Change: Deployed the pr-monitor protocol to verify CI build statuses independently and document maintainer silence as a standard open-source state.
Result: Confirmed all GitHub CI checks (Python tests, lint, and type checking) passed successfully while keeping the contribution story accurate and complete.   

Limitations and What's Next
Throughout this term, my toolkit successfully bridged the gap between diagnosing complex async database session errors and automating pre-flight verification gates. However, the current toolset still lacks automated cross-repository impact analysis when changes touch shared core schemas. For future iterations, I would build an automated dependency-graph visualizer into the plan-check tool to map out downstream consumer effects across external packages before making modifications.

Tool limits.
issue-scout: Relies heavily on static issue descriptions and cannot automatically parse unlinked architectural bottlenecks.
repro-check: Cannot automatically mock external database states without explicit test fixtures configured.
pr-precheck: Lacks automated end-to-end browser test execution for frontend UI components.
