# Contribution: get_dead_code queries a closed session, leaking a pooled connection and making test_finding_registry_visibility.py flaky

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** [1]
**Student:** Parth Patel
**GitHub Username:** parth33320
**Issue:** https://github.com/repowise-dev/repowise/issues/4092
**Pull Request:** [GitHub PR link, once opened]

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `[1]` unless you make more than one contribution this term; it
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

-Tool: Issue scout skill overlay (~/.claude/skills/issue-scout/) and GitHub CLI (gh search issues)   
-What it produced: A configured real-world issue scout setup with custom gh search issues strategies targeting Python and CLI issues, complete with local candidate scans and upstream claim comments.   Difficulties: Overlay installation cleared out required dependency files (rubric.md and evidence-guide.md) in the global skill directory, causing initial scan evaluation errors.   
-What changed: Restored the missing rubric and evidence files by pulling them from the coursework repository's tools/issue-select/ history and successfully re-ran scans to identify valid open-source candidate issues.   
