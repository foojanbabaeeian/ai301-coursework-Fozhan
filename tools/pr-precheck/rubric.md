# Rubric: is this pull request ready to submit?

<!--
Eight checks over the four evidence families in the evidence guide,
two per family: plan fidelity (diff-matches-plan, description-matches-
diff), test evidence (repro-rerun-shown, repo-checks-run), diff quality
(no-debris, no-unrelated-hunks), and standards and comms (template-
honored, disclosure-per-policy). Seven are required; repo-checks-run is
preferred because many repos' own checks are not shown in a terse but
ready PR, and "not tested" is caught by repro-rerun-shown. Every check
judges the artifact itself (the hunks, the pasted output, the stated
asks) against the plan and the issue, never the description's length
or the commit count. Calibration anchors from the class activity:
calib-01 (one-line deletion, terse description, before/after shown)
must pass every required check; calib-02 (a dependency bump and
formatting churn that never implements the plan, "fixed it!!", no
evidence) must fail diff-matches-plan and repro-rerun-shown; calib-03
(perfect checklist and evidence over a diff whose second hunk rewrites
a file the plan never names, under "no changes beyond the plan") must
fail diff-matches-plan and description-matches-diff on the hunk, not
the prose; calib-04 (solid fix, plan names two failure modes, evidence
re-runs only the first) must fail repro-rerun-shown and nothing else.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| diff-matches-plan | Every file and hunk in the diff, read against the plan's scope pair (in scope, not in scope), its named files, its approach, and its deviation notes (the plan's `## Deviations` section live, or deviation sentences in the plan-context block in eval) | Pass when every changed file and every hunk falls inside the plan's stated scope or is covered by a deviation note that names the change and its reason, AND every change the plan committed to is present in the diff or its absence is covered by a deviation note. A test file the plan names, or a regression test for the planned fix, is inside scope even when unlisted. A smaller delivery with a note that says what was left out and why passes. Fail when the diff touches a file or adds a behavior the plan never names and no note covers it (a new config option, flag, or setting the plan scoped out; a renaming pass; a rewrite of a second file; a dependency bump in place of the planned fix), or when a change the plan promised is missing from the diff with no note (the plan says warning and docs, the diff has only the warning). Silence in both directions is the fail; a recorded deviation is not. | required |
| description-matches-diff | Every claim in the PR description and title about what the change does, what it does not do, and what it leaves out ("implements the plan exactly", "no changes beyond", "now documents", "also adds"), read against the diff's actual files and hunks | Pass when each claim names something the diff contains and nothing the diff lacks: a fidelity claim is true of every hunk, a "now does X" claim has a hunk doing X, and a disclosed shortfall or deviation in the description matches what the diff leaves out. A terse description that claims only what one hunk does passes. Fail when the description claims fidelity the diff contradicts ("no functional changes outside the fix" over a hunk that adds a setting), claims a change the diff does not contain (docs "now document the limitation" with no docs hunk), or omits an extra behavior the diff adds while asserting the change is bounded. Grade this from the diff, never from the description's confidence. | required |
| repro-rerun-shown | The PR's test-evidence section (commands and pasted output, before and after), read against the plan's test plan and the repro evidence's steps and trigger in the plan-context block | Pass when the evidence shows the issue's trigger re-run on the change with an observable outcome named for each failure mode the plan's test plan commits to: the before (or the issue's known behavior) and the after (the output, exit code, rendered result, table, or test name with its result) are both visible as output or a transcript, not as a sentence. "Before: traceback; after: `parsed OK`" is enough. Fail when the evidence is prose only ("tested locally, works now", "verified for a full day", "cargo test passes" with no repro shown), when it exercises only a control or unchanged path and never the trigger (a GET where the bug is a POST; the single-file case where the bug needs two files), when a failure mode the plan's test plan names is never re-run and no note explains the gap, or when there is no test evidence at all. An added test that covers a different case than the fix does not substitute for the re-run. | required |
| repo-checks-run | The test-evidence section and description, read against the checks the repo-facts block or the PR template names (the suite, lint, typecheck, pre-commit, a CI checklist) | Pass when the repo's named checks are shown as run with their outcome visible (a pass count, a clean run, or an honestly reported failure with its reason), or when the repo names no checks. A check reported as not runnable with the reason stated passes. Fail when the repo names checks and the evidence shows none of them run, or asserts "all checks pass" with no output. This check never changes the verdict; it is a nudge to paste what the maintainers will ask for. | preferred |
| no-debris | Every hunk in the diff and every commit message in the commit list, read for leftovers that are not the fix: debug prints or logging added for investigation, commented-out code or first attempts, dead functions or variables (including ones behind an allow-dead-code or noqa marker), TODO or FIXME notes added by this change, stray files, and throwaway commit messages ("wip", "fix", "misc cleanups while debugging") | Pass when every added line is part of the fix, its test, or its documentation, and no commented-out code, debug output, dead definition, or new TODO is present. An explanatory code comment is not debris. Fail when any hunk carries a debug print or eprintln left in, a commented-out block or line of code, a definition nothing calls, a TODO the change introduced, or a stray file; or when the commit list is only throwaway messages over such hunks. One debris line fails; the fix being right does not offset it. | required |
| no-unrelated-hunks | Every hunk in the diff, read against the plan's change and the issue's one reported behavior | Pass when every hunk either implements the planned change, tests it, documents it, or is a deviation the plan records. Fail when the diff carries hunks the fix does not need: formatting or re-indent churn on untouched logic, import reordering or restructuring, duplicated or re-printed blocks, reworded help text or docs for unrelated flags, or a cleanup of code the issue does not touch. Churn counts even when the fix inside it is right and evidenced; a reviewer has to read every hunk. | required |
| template-honored | The repo-facts block's `pull requests` line (eval) or the repo's PR template and contributing guide (live), read against the PR description's sections and content | Pass when the repo has no PR template, OR each section or item the template requires is present with real content that applies to this change: the issue referenced the way the template asks (`closes #N` where it asks for it), checklist items ticked only where the evidence supports them, required sections (Problem, Solution, Summary, Testing, changelog or whatsnew entry) filled rather than left as placeholders. A required artifact the template names (a changelog or whatsnew entry) must appear in the diff, not only be mentioned. Fail when a required section is absent or still a placeholder, when the issue reference the template requires is missing, or when the template requires an entry in the diff (whatsnew, changelog) that the diff does not contain. Sections the template marks optional, or that do not apply, do not fail. | required |
| disclosure-per-policy | The repo-facts block's `contribution policy` line (CONTRIBUTING.md, AI policy files) for any AI-use statement, read against the PR description. Course packages count as AI-assisted work. | Pass when the policy reports no AI statement, OR states conditions the description meets: (a) if the policy requires disclosing AI use in pull requests, in all contributions, or "all AI usage in any form", the description discloses that AI assisted and roughly how; (b) if the policy only asks for the contributor's own words, for the contributor to understand and own the work, or for a human-reviewed contribution, a first-person description specific to this change passes (AI-assisted is not AI-generated); (c) a rule against fully AI-generated content with assistive use allowed is a condition a first-person description that owns its change meets. Fail when the policy requires disclosure that covers pull requests or all usage and the description does not disclose, or when the policy bans AI-assisted contributions outright. | required |

## Verdict rule

Accept only when every `required` check passes: diff-matches-plan,
description-matches-diff, repro-rerun-shown, no-debris,
no-unrelated-hunks, template-honored, and disclosure-per-policy. Any
single `required` fail rejects the package. `unclear` on a `required`
check counts as fail: a PR whose scope, claims, or evidence cannot be
verified from the package is a PR that is not ready to submit. The
`preferred` check (repo-checks-run) never changes the verdict; report
it so the writer can paste the missing output before opening the PR.

An honestly disclosed shortfall is not a fail: when the plan's
deviation note and the description both say what was left out and why,
diff-matches-plan and description-matches-diff pass on that point, and
repro-rerun-shown reads the test plan as amended by the note.

When two or more required checks fail, name all of them in the output;
the first in table order is the deciding check quoted in the summary.
