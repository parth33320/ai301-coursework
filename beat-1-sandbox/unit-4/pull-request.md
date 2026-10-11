# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request you opened against the Path Review repo, and of the evaluation
runs that produced `eval-run.txt`. This file is graded at the path above; a copy kept
anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s3/pull/115

**Branch**

fix/14-offline-eval-runner

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Full run (2026-10-09T07:40:10Z): 19/20 agree, every category matched (clear-accept 6/7, not-tested 4/4, silent-drift 4/4, standards-wall 2/2, unreviewable 3/3), above the 18/20 bar. This is the run saved in `eval-run.txt`, so the last score matches its agreement line (`agreement: 19/20 scored items (bar: 18/20: PASS)`).

**Package analysis**

`pkg-11` (BurntSushi/ripgrep#3477, category clear-accept). The gold label is `accept`; my rubric graded `reject`, on a failed `plan_fidelity` check.

The accepted plan named one file, `crates/ignore/src/gitignore.rs`, and described the fix as "trim trailing spaces only up to the first escaped space." The PR's diff touches exactly that one file and no other, and does exactly what the plan describes: it stops the existing trim at the first backslash-escaped space. What my check flagged is that the diff also introduces a new named helper function, `trim_unescaped_trailing_spaces`, which the plan's prose never mentions by name. My `plan_fidelity` check reads "strictly within the plan's stated... scope," and the grading model treated the unnamed helper as a piece of structure the plan didn't authorize, rather than as an implementation detail of the one change the plan did authorize. The plan described a behavior change in one function in one file; extracting that behavior into a private helper in the same file, with no new file, no new public surface, and no unrelated change, is still that behavior change. My rubric's wording does not yet separate "an unannounced new file, dependency, or public function" (real drift) from "a private helper that implements the one described change" (not drift), so it read the second as the first.

**Check rationale**

Check as it reads in `tools/pr-precheck/rubric.md`:

> `plan_fidelity` — Evidence: "Diff hunks and description read against plan context (scope, files, and deviation notes)" — Pass condition: "Every changed file and functional diff hunk falls strictly within the plan's stated file list/scope or an explicit plan deviation note; the description does not claim changes that are missing from the diff, nor does the diff contain unannounced scope/feature drift" — Weight: required

It reads this way because the activity worksheet's "Files" and "Claims" tables named silent scope drift (a PR that quietly does more, or less, than its plan) as one of the four ways a reject-gold PR fails, and the only way to catch that from the diff and the plan side by side is a check that reads every hunk against the plan's stated scope, not just its stated files. I wrote "strictly" and "unannounced... drift" so the check would fail a PR that touches a file or a behavior the plan never named, which is exactly what it does correctly on the `silent-drift` category (4/4).

**Trade-offs**

This phrasing buys real protection against the failure it targets, at the cost of occasionally rejecting a bounded, same-file refactor that introduces a private helper the plan's prose didn't name, as it did on `pkg-11`. I chose not to loosen the check to pass `pkg-11`, because the four `silent-drift` packages it already catches are the exact failure the activity flagged, and loosening "strictly" risks letting a genuine scope-creep PR read its own refactor as "an implementation detail" the same way. I accept the one false reject rather than risk the four true rejects it currently catches; nothing else in the run changed.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
