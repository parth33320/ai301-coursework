# Rubric: is this reproduction package ready to post?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| environment_recorded | The repro report's environment section or header, compared with the version and platform in the issue description and thread | The report names the operating system, tool or package version, and runtime; and if the tested version or platform differs from the issue's, the report says so explicitly | required |
| followable_and_public_steps | The repro report's setup and reproduction steps, config files, and repository links | The steps are exact and complete, a stranger could follow them in order from a fresh setup, and they rely only on public repos and shared configs | required |
| exact_trigger_syntax | The commands, CLI arguments, and inputs in the repro report, compared with the trigger described in the issue | The commands and inputs match the issue's trigger (or an explicit control run) and are not altered in a way that causes unrelated errors | required |
| matching_artifacts_shown | The terminal output, logs, and stack traces in the repro report, compared with the expected and actual behavior in the issue | The shown output demonstrates the specific behavior the issue reports (or a systematic attempt, for an honest cannot-reproduce), and unrelated output such as a clean validation exit or a normal banner is not presented as the reported failure | required |
| honest_and_evidenced_claims | The claims in the claim comment and repro report, compared with the artifacts shown | Every claim of reproduction, non-reproduction, or root cause is backed by a shown artifact, with no bare "+1 / me too" and no unsupported root-cause assertions | required |
| specific_modest_claim_comment | The claim comment text, compared with the issue topic and repo context | The claim comment is human-voiced, refers to the issue's specifics, and promises only an investigation, not a fix, a date, or "assign me" boilerplate | required |
| repo_ai_disclosure | The repo's generative-AI policy in the repo-facts block, and the claim comment and repro report text | If the repo's stated policy requires disclosing AI assistance, the comments explicitly disclose it; if the policy requires nothing, this check passes | required |

## Verdict rule

Accept the reproduction package if every required check receives a `pass` grade. If any required check receives a `fail` or `unclear` grade, reject the reproduction package. Preferred checks (if any) never change the verdict.
