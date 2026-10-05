# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/14

**Verdict output**

```
Now I'll grade both issues against the rubric:

Grade Summary

Issue #14: Implement an offline eval runner

┌───────────────────────┬───────┬─────────────────────────────────────────────────────────────────────────────────┐
│         Check         │ Grade │                                    Evidence                                     │
├───────────────────────┼───────┼─────────────────────────────────────────────────────────────────────────────────┤
│ repo_alive            │ pass  │ Not archived; last push 2026-09-16 (18 days ago); newest of last 5 commits      │
│                       │       │ 2026-10-05 (within 60 days)                                                     │
├───────────────────────┼───────┼─────────────────────────────────────────────────────────────────────────────────┤
│ maintainer_responsive │ pass  │ Opened by Aburke225 with author_association COLLABORATOR                        │
├───────────────────────┼───────┼─────────────────────────────────────────────────────────────────────────────────┤
│                       │       │ Clear request: "score the benchmark profiles and write eval_results.json";      │
│ scope_bounded         │ pass  │ listed files (scripts/run_evals.py, rag/evaluator/eval_suite.py); estimated     │
│                       │       │ 7–10 hours                                                                      │
├───────────────────────┼───────┼─────────────────────────────────────────────────────────────────────────────────┤
│ unclaimed             │ pass  │ Assignees: none; all comments from parth33320 (user); per house rule, user's    │
│                       │       │ own comments don't count as claims                                              │
├───────────────────────┼───────┼─────────────────────────────────────────────────────────────────────────────────┤
│ ai_policy_allows      │ pass  │ CONTRIBUTING.md does not ban AI-generated or AI-assisted contributions; no      │
│                       │       │ stated policy to prohibit                                                       │
├───────────────────────┼───────┼─────────────────────────────────────────────────────────────────────────────────┤
│ fit_signals           │ pass  │ Opened by collaborator (Aburke225) with clear description of expected behavior  │
│ (preferred)           │       │ and acceptance criteria                                                         │
└───────────────────────┴───────┴─────────────────────────────────────────────────────────────────────────────────┘

Verdict: ACCEPT

---

Issue #64: Relevance scorer partial overlap test fixture bug

┌───────────────────────┬───────┬─────────────────────────────────────────────────────────────────────────────────┐
│         Check         │ Grade │                                    Evidence                                     │
├───────────────────────┼───────┼─────────────────────────────────────────────────────────────────────────────────┤
│ repo_alive            │ pass  │ Not archived; last push 2026-09-16 (18 days ago); newest of last 5 commits      │
│                       │       │ 2026-10-05 (within 60 days)                                                     │
├───────────────────────┼───────┼─────────────────────────────────────────────────────────────────────────────────┤
│ maintainer_responsive │ pass  │ Last 5 commits (5/5 within 60 days): 2026-10-05, 2026-09-16, 2026-09-16,        │
│                       │       │ 2026-09-16, 2026-08-24                                                          │
├───────────────────────┼───────┼─────────────────────────────────────────────────────────────────────────────────┤
│                       │       │ Clear problem: fixture has full keyword overlap when test expects partial;      │
│ scope_bounded         │ pass  │ clear fix: adjust fixture tokens to get genuine partial overlap; one test, one  │
│                       │       │ file                                                                            │
├───────────────────────┼───────┼─────────────────────────────────────────────────────────────────────────────────┤
│                       │       │ Per Path Review house rule: "other students' claim comments do not block an     │
│ unclaimed             │ pass  │ issue"; april-hpxd and jahnvisethjs claimed on 2026-09-27 and 2026-09-29, but   │
│                       │       │ classroom repo rule applies                                                     │
├───────────────────────┼───────┼─────────────────────────────────────────────────────────────────────────────────┤
│ ai_policy_allows      │ pass  │ No stated policy banning AI contributions                                       │
├───────────────────────┼───────┼─────────────────────────────────────────────────────────────────────────────────┤
│ fit_signals           │ pass  │ Labeled with "good first issue" and tier-1; clear expected behavior (0.3 <      │
│ (preferred)           │       │ score < 0.9)                                                                    │
└───────────────────────┴───────┴─────────────────────────────────────────────────────────────────────────────────┘

Verdict: ACCEPT

---

[
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/14",
    "checks": [
      {"name": "repo_alive", "grade": "pass", "evidence": "Not archived; last push 2026-09-16 (18 days); newest commit 2026-10-05 within 60 days"},
      {"name": "maintainer_responsive", "grade": "pass", "evidence": "Issue opened by Aburke225 with author_association COLLABORATOR"},
      {"name": "scope_bounded", "grade": "pass", "evidence": "Score benchmark profiles and write eval_results.json; files listed; 7–10 hour estimate"},
      {"name": "unclaimed", "grade": "pass", "evidence": "No assignees; all comments from parth33320 ignored per house rule"},
      {"name": "ai_policy_allows", "grade": "pass", "evidence": "CONTRIBUTING.md contains no AI ban; no stated policy"},
      {"name": "fit_signals", "grade": "pass", "evidence": "Opened by collaborator with clear spec; aligns with user goal to improve backend/evaluation tooling"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/64",
    "checks": [
      {"name": "repo_alive", "grade": "pass", "evidence": "Not archived; last push 2026-09-16 (18 days); newest commit 2026-10-05 within 60 days"},
      {"name": "maintainer_responsive", "grade": "pass", "evidence": "5 of last 5 commits within 60 days (active maintenance)"},
      {"name": "scope_bounded", "grade": "pass", "evidence": "Fix fixture overlap: change chunk to drop 'framework' and 'web' for genuine 2/4 partial overlap; remove xfail marker"},
      {"name": "unclaimed", "grade": "pass", "evidence": "Classroom repo house rule: classmates' claim comments don't block; april-hpxd (2026-09-27) and jahnvisethjs (2026-09-29) claims ignored"},
      {"name": "ai_policy_allows", "grade": "pass", "evidence": "No stated AI policy; PR #87 included AI-use disclosure, suggesting allowed with disclosure"},
      {"name": "fit_signals", "grade": "pass", "evidence": "Labeled 'good first issue' and 'tier-1'; test fixture bug with clear acceptance criteria"}
    ],
    "verdict": "accept"
  }
]

Ranking by fit

Both issues pass all required checks. Ranked by your fit profile:

1. #14 (higher fit) — Backend evaluation infrastructure (RAG pipeline work, test infrastructure). Aligns directly with your goal to "get better at backend and evaluation tooling"; 7–10 hours of meaningful work.
2. #64 (lower fit) — One-file test fixture fix. Tier-1 starter issue; simpler scope, less aligned with your stated learning goal (though good for unblocking other work).
```

---

## Eval iterations

**Run history**

1. Full run: 15/20 agree (clear-accept 3/8, claimed 4/4, dead-repo 3/3, policy 1/1, scope 4/4), below the bar. Five accept-gold issues were rejected: issue-01, issue-04, issue-14, issue-16 and issue-19.
2. Partial re-run with `--only` on those five plus eight canaries (issue-02, issue-05, issue-07, issue-10, issue-13, issue-15, issue-18, issue-20): 13/13 agree.
3. Full confirming run: 20/20 agree, every category matched (claimed 4/4, clear-accept 8/8, dead-repo 3/3, policy 1/1, scope 4/4). This is the run saved in `eval-run.txt` (`agreement: 20/20 scored items`).

**Issue analysis**

`issue-04` (zxcalc/zxlive#555, "Missing several basic rule previews"). My first rubric rejected it (failed `scope_bounded`); the gold label is `accept` (category clear-accept). My first `scope_bounded` condition asked for a bounded change a stranger could finish and listed things that fail, but it did not say that a terse body still passes. The issue is two short lines ("Including remove identity, fuse spiders, remove self loops, etc.") filed by a collaborator, so my rubric read the "etc." as an open-ended list and called it unbounded. The evidence guide says short is not the same as unscoped, and that a maintainer-filed bug can be a perfectly bounded first issue. I revised the check so that it fails only on an umbrella or tracking list, an unsettled design debate, a usage question, a one-line wish with no spec, or 2 or more abandoned PRs, and says explicitly that a short or maintainer-filed body still passes. After that, issue-04 graded accept.

**Check rationale**

Check as it reads in `tools/issue-select/rubric.md`:

> | scope_bounded | The issue body and comment thread, read against the evidence guide's Family 3 | The issue asks for one bounded change someone can finish in one pull request. Fail only if the issue is an umbrella, tracking, or "megaissue" list meant to be split into separate work; a design debate with no maintainer-settled direction; a usage question; a one-line wish with no spec and a product decision inside; or it has 2 or more abandoned (closed, unmerged) PRs in its history. A short body, a maintainer-filed bug with few details, or a maintainer's list of named causes for one bug is still bounded and passes | required |

It reads this way because my first version judged scope by how complete the write-up looked, which rejected five good issues (issue-01, 04, 14, 16 and 19) that were terse or maintainer-filed. I replaced the vague test with a list of specific failures taken from the evidence guide's Family 3, so that someone else can apply it and get my answer, and added the sentence that a short body or a maintainer's list of named causes still passes.

**Trade-offs**

Loosening `scope_bounded` could flip the scope-reject issues that agreed before, so I re-ran issue-05, issue-10, issue-15 and issue-20 (the scope rejects) with `--only` as canaries, together with issue-02, issue-07, issue-13 and issue-18 from the other categories. All stayed correct (13/13), and the confirming full run was 20/20. What the check gives up: it will pass a genuinely large issue that is written tersely, because it does not weigh effort, only the listed failure signals. I accept that, because the effort estimate lives in the issue text and I check it by hand in live mode.

---

## Selection rationale

**Selection rationale**

1. **Fit and time.** Issue #14 asks me to implement an offline eval runner in `scripts/run_evals.py` that scores benchmark profiles with the existing `EvalSuite` and writes `eval_results.json`. That is Python, test and tooling work, which matches what I use and what I want to get better at (evaluation tooling). The issue estimates 7 to 10 hours; I reduced it to one script plus one unit test, so it fits the time I have this term.
2. **What the verdict caught, and what I weighed myself.** The verdict correctly checked that the repo is active, that a maintainer opened the issue, that nobody was assigned and no PR was linked, and that the repo states no AI ban. The rubric cannot see the "tier-3 / advanced" label and the 7 to 10 hour estimate, or whether I could reproduce the bug. I weighed those myself and reproduced the stub behavior before choosing it.
3. **Anticipated difficulty in claiming it.** Classmates may claim the same issue, but the house rules say that does not block me, so I will post my own claim and my own reproduction. The harder part is that the issue is labelled advanced, so I will keep my claim to an investigation and not promise a fix or a date.

   I ran the skill after I had already started working on #14, so my first live run rejected it only because it counted my own comments as a claim. I added a line to `scope.md` telling the skill to ignore comments by `parth33320`, and the next run accepted #14.

---

Related paths: `eval-run.txt` in this directory; your skill's files in `tools/issue-select/`.
