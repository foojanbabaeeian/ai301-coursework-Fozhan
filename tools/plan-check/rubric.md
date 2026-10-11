# Rubric: is this plan ready to post and build from?

<!--
Eight checks over the six evidence families in the evidence guide:
one for the diagnosis (cause-grounded), one for scope (scope-bounded),
one for executability (executable), one for the test plan
(test-decisive), one for honesty (unknowns-honest), two for the words
(thread-engaged, conventions-respected), and one preferred nudge
(repo-asks-followed). Seven are required. Every check judges the
plan itself against the issue and its repro evidence, never the
write-up's length, headings, or confidence. Calibration anchors from
the class activity: calib-01 (four short paragraphs, one file, repro
re-run as the test) must pass every required check; calib-02 ("poke
around this weekend") must fail executable and test-decisive;
calib-03 (polished plan adopting the thread's diagnosis that the
repro's own timing matrix rules out) must fail cause-grounded on the
evidence, not the prose; calib-04 (solid bounded plan whose test is
"run the full suite") must fail test-decisive and nothing else.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| cause-grounded | The plan's stated cause (its diagnosis, root cause, or "as the issue states" line), read against every artifact, step, and control run in the repro-evidence block, and against any maintainer analysis in the thread highlights | Pass when the stated cause would produce the artifact the repro shows AND is consistent with every control run in the repro evidence: each control that passes or behaves differently is something the cause predicts. A cause that quotes the issue's or a maintainer's analysis passes when the repro evidence agrees with that analysis. Fail when any step or control in the repro evidence rules the cause out (the cause blames a component the control shows working on the same input; the evidence shows the damage already done before the stage the plan blames; the cost or failure appears with the blamed component removed from the loop), when the cause names a mechanism no artifact in the package shows, or when the plan names no cause at all and goes straight to changes. Read the controls before the diagnosis: a confident cause the evidence contradicts fails here even when the thread asserts it. | required |
| scope-bounded | The plan's in-scope and not-in-scope statements, its change list, and its files or areas, read against the one behavior the issue reports and the repro isolates | Pass when every listed change is needed to make the issue's reported behavior stop or to verify that it stopped, and the plan says what it leaves out. Deferring related work with a reason ("option 1 is a bigger rework than this bug needs", "the Windows variant is untestable here") is bounded and passes; a docs-only or narrow-case fix is bounded when it addresses the reported behavior. Fail when the plan bundles work the issue did not ask for: a library migration, a refactor or rewrite of the surrounding module, a new option, setting, or UI surface, a CI or test-harness migration, a second bug fixed "while in the area", or a multi-front campaign in which the fix for the reported behavior is one item among several. A plan that offers to split such work into a PR series still fails: the scope is what it proposes, not how it is packaged. | required |
| executable | The plan's files or areas, its approach, and its order of work, read as a stranger with the repo checked out who may not ask the author anything | Pass when the plan names at least one concrete file, function, module, or document to change AND commits to one approach a stranger could begin implementing (what is added, removed, or changed where). A short plan passes when both are present; a stated open question about a detail passes when the main approach is chosen. Fail when the plan has no named file or area, when the approach is a choice still to be made ("gocui? tcell? not sure", "upstream or vendored, whichever is easier", "profile and see"), when the first step is to investigate rather than to change something, or when the change is described only by its goal ("make it fast", "handle the error properly") with no mechanism. | required |
| test-decisive | The plan's test plan, read against the repro evidence's steps, trigger, and artifact | Pass when the test plan names at least one observable outcome tied to the issue's trigger that a reader could watch pass or fail: the repro steps re-run with the expected output, exit code, color, or absence named; a named test case with its assertion; a fixture with the expected result; or a measured number with a threshold. A regression test built from the issue's own case counts. Fail when the test plan is only "run the test suite" or "make sure nothing regresses" with no outcome for the fix itself, when the outcome is a feeling ("should feel fast", "nothing else should feel broken"), when it names an artifact nobody could check from the plan, or when there is no test plan. | required |
| unknowns-honest | Every sentence in the plan and comment that states certainty, cost, compatibility, or completeness ("confirmed", "the cheapest", "no behavior change", "covers the whole surface", "will land", a date), read against the repro evidence, the thread, and the plan's own risks or unknowns | Pass when the plan's claims stay within what the repro evidence and thread show, and anything it has not measured or tested is marked as such (a risk line, an open question, a stated deferral). A terse plan with no risks section passes when it makes no claim beyond its evidence. Fail when the plan presents as settled something the package leaves open or contradicts: a platform or variant declared fixed that the repro never ran on, a performance or compatibility claim with no measurement, a cause stated as certain that the evidence disputes, or a promised date or guarantee. | required |
| thread-engaged | The thread highlights, read for explicit maintainer or owner direction (a named culprit file or function, a proposed or rejected approach, a patched binary or draft PR with a request to test it, a works-as-intended note, a linked prior-art PR), then the candidate plan and plan comment read for where they address it | Pass when the thread contains no such direction, OR when the plan or comment names the direction and either follows it or says why it diverges (engaging a prior PR by rebasing onto it, adopting the maintainer's proposed mechanism, declining the deep fix the owner called hard with a reason). Fail when the thread carries explicit direction and the plan proposes something else without mentioning it: a workaround or docs change where the owner has already isolated the code culprit and posted a patch to test, a mechanism the thread already rejected, or a duplicate of an open PR the comment never names. | required |
| conventions-respected | The repo facts' `contribution policy` line (CONTRIBUTING.md, AI policy files, templates), read against the candidate plan comment. Course packages count as AI-assisted work. | Pass when the policy line reports no AI statement, OR states conditions the comment meets: (a) if the policy requires disclosing AI use in comments or issues, or "all AI usage in any form", the plan comment discloses that AI assisted and roughly how; (b) if the policy only asks that comments be in the contributor's own words, that the contributor understand and own the work, or limits its disclosure ask to pull requests or code, a first-person comment specific to this issue passes (AI-assisted is not AI-generated); (c) a rule against fully AI-generated content with assistive use allowed is a condition a first-person comment that owns its plan meets. Fail when the policy requires disclosure of AI usage that covers comments (or all usage in any form) and the plan comment does not disclose, or when the policy bans AI-assisted contributions outright. | required |
| repo-asks-followed | The repo facts' `bug reports` and `contribution policy` lines for non-AI asks (small focused PRs, review-bandwidth notes, vouch flows, a branch or test convention), read against the plan and comment | Pass when each stated ask that applies to a plan is acknowledged or met (a one-change plan where review time is scarce, a regression test where tests are asked for, a note about the vouch flow where one exists). Fail when a stated ask is contradicted without comment. This check never changes the verdict; it is a nudge to meet what the maintainers will ask for in review. | preferred |

## Verdict rule

Accept only when every `required` check passes: cause-grounded,
scope-bounded, executable, test-decisive, unknowns-honest,
thread-engaged, and conventions-respected. Any single `required` fail
rejects the package. `unclear` on a `required` check counts as fail: a
plan whose cause, scope, approach, or test cannot be verified from the
package is a plan that is not ready to build from. The `preferred`
check (repo-asks-followed) never changes the verdict; report it so the
writer can fix it before posting.

When two required checks fail, name both in the output; the first in
table order is the deciding check quoted in the summary.
