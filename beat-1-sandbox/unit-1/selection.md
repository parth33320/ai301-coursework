# Unit 1 — Issue Selection

## Selected Issue
- **Issue Link:** https://github.com/codepath/path-review/issues/14

## Verdict Output
```json
{
  "issue_id": "issue-14",
  "verdict": "ACCEPT",
  "summary": "Repository is actively maintained, issue scope is bounded (offline eval runner portfolio implementation), and issue is unclaimed.",
  "checks": [
    { "check": "maintainer_alive", "pass": true },
    { "check": "scope_fits", "pass": true },
    { "check": "unclaimed", "pass": true }
  ]
}

Run History
Initial calibration run (calib-01 through calib-04) to test harness setup.

First full run with basic rubric (15/20 PASS).

Final rubric refinement yielding 18/20 PASS (saved via eval-run.txt).

Check Rationale
maintainer_alive: "The maintainer has pushed commits or responded to issues within the last 30 days."
Rationale: Ensures PRs opened on the repository will actually be reviewed and merged.

Trade-offs
We prioritized maintainer response recency over loose issue difficulty labels to avoid selecting dead repositories.
