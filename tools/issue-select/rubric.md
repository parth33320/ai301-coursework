# Rubric: is this a good first issue?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| repo_alive | The repo line (archived flag), "last push to any branch", and the dates of the "last 5 default-branch commits" in the repo-facts block, measured against the capture date | The repo is not archived AND the newest of the last 5 default-branch commits is dated within 180 days of the capture date AND the last push to any branch is within 180 days of the capture date | required |
| maintainer_responsive | The "maintainer first-response sample" in the repo-facts block, the issue opener's role and the author_association (Owner, Member, Collaborator) of commenters in this issue's thread, and the dates of the "last 5 default-branch commits" | At least one of: (a) the sample shows a maintainer reply to some issue within 100 days; (b) the issue was opened by, or has a comment from, an Owner, Member, or Collaborator; (c) at least 3 of the last 5 default-branch commits are dated within 60 days of the capture date. A slow reply (for example 30 to 90 days) is not a fail by itself; failing means none of (a), (b), (c) holds | required |
| scope_bounded | The issue body and comment thread, read against the evidence guide's Family 3 | The issue asks for one bounded change someone can finish in one pull request. Fail only if the issue is an umbrella, tracking, or "megaissue" list meant to be split into separate work; a design debate with no maintainer-settled direction; a usage question; a one-line wish with no spec and a product decision inside; or it has 2 or more abandoned (closed, unmerged) PRs in its history. A short body, a maintainer-filed bug with few details, or a maintainer's list of named causes for one bug is still bounded and passes | required |
| unclaimed | "this issue: assignees" and "linked PRs" in the repo-facts block, plus any claim comments ("I'll take this", "working on this") in the thread, with their dates | Assignees is none AND there is no open linked PR AND no claim comment from the last 90 days before the capture date that is still unresolved (a stale claim older than 90 days with no PR does not count) | required |
| ai_policy_allows | The "contribution policy" line in the repo-facts block (CONTRIBUTING.md, AI policy files, templates) | The stated policy does not outright ban AI-generated or AI-assisted contributions; disclosure, review, or understanding requirements are terms to follow and still pass; no stated policy passes | required |
| fit_signals | The issue's labels (good first issue, documentation, bug), the issue opener's role, and whether the body states expected behavior or acceptance criteria | The issue carries a good-first-issue or documentation label, OR is maintainer-filed with a clear description of expected behavior | preferred |

## Verdict rule

Accept if every required check passes. Preferred checks never change the verdict; they only rank accepted issues. A required check graded unclear counts as fail.
