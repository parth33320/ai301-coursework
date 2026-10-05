# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/14

**Verdict output**

<!-- REPLACE THIS WHOLE CODE BLOCK with your skill's live-mode output for issue #14, pasted verbatim, ending with the fenced JSON block. It must record "accept". Command:
claude "issue-select: grade these candidate first issues: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/14 <URL2> <URL3>" -->

```
paste the output here, including the closing JSON block
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

---

Related paths: `eval-run.txt` in this directory; your skill's files in `tools/issue-select/`.
