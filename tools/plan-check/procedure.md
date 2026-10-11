# Procedure: how this skill grades a plan package

These are the operating steps. `rubric.md` says what to decide,
`references/evidence-guide.md` says where each kind of evidence lives,
and this file says in what order to read, how to gather, how to grade,
and how to assemble the verdict. Follow the steps as written; where a
step is silent, report the gap in the summary rather than inventing
one.

## Read order

1. In live mode only: read `scope.md`. Confirm the issue URL is in the
   scoped repo; if the `Repo:` line still holds a placeholder, stop
   and say so. Note the house rules (a classmate's plan does not block,
   never piggyback, branch on the fork). In eval mode skip this step.
2. Read `rubric.md` and list its checks in table order with their
   weights, then read `references/evidence-guide.md` so each check's
   evidence family has a known location.
3. Read the repo-facts block (eval) or the repo's CONTRIBUTING.md, AI
   policy, and templates (live). Write down two facts: (a) whether the
   contribution policy makes any statement about AI use and, if so,
   what it covers (comments, PRs, "all usage in any form", a ban); (b)
   any other ask that applies to a plan (small PRs, review bandwidth,
   vouch flow, tests expected).
4. Read the issue title and body. Write down the one reported
   behavior in a sentence: the trigger, the actual result, the
   expected result. Every scope and test judgment later is measured
   against this sentence.
5. Read the thread highlights (eval) or the live issue thread. Write
   down every item of explicit direction from a maintainer, owner,
   collaborator, or linked PR: a named culprit, a proposed or rejected
   mechanism, a patch or binary posted for testing, a works-as-intended
   note, prior-art PRs. If there is none, write "no direction".
6. Read the repro evidence block (eval) or the student's posted repro
   comment or house repro pack (live) BEFORE the plan. For each step
   and each control run, write down what it pins: the artifact on the
   trigger, and for every control, what input changed and what came
   out. The controls are the facts a diagnosis must survive, so they
   are recorded before any diagnosis is read.
7. Only now read the candidate plan, start to finish, then the
   candidate plan comment. While reading, mark the sentences that
   state the cause, the in-scope and not-in-scope lines, every named
   file or area, the chosen approach, the test plan, every risk or
   open question, and every sentence of certainty (confirmed, cheapest,
   no behavior change, will land, a date).

The repro evidence is read before the plan because cause-grounded is
decided by whether the plan's cause survives the controls; reading the
plan first makes the controls look like confirmation of whatever the
plan said.

## Evidence gathering

For each family, pull the fact from the place named and record it as a
quote or a one-line fact. In live mode use the locations the evidence
guide names for GitHub and the drafts; the package is what the drafts
contain and quote, never other files on disk.

1. Diagnosis: quote the plan's cause sentence. Beside it, list the
   repro's artifact and each control from step 6 of the read order.
   For each control, answer one question: if the plan's cause were
   true, would this control have behaved the way the evidence shows?
   Record yes or no per control. Also record whether the thread
   asserts a cause and whether the evidence agrees with that
   assertion.
2. Scope: quote the in-scope line, the not-in-scope line, and every
   change in the change list. Beside each change, record whether it is
   needed to stop the reported behavior from step 4, needed to verify
   the fix, a deferral with a reason, or extra work the issue did not
   ask for (migration, refactor, new option or setting, UI surface,
   harness change, second bug, campaign item).
3. Executability: list every file, function, module, or document the
   plan names. Quote the approach sentence. Record whether the
   approach is one chosen mechanism or a choice deferred to build time
   ("whichever is easier", "not sure which layer", "profile and see",
   "investigate first").
4. Test plan: quote the test plan. Record the observable outcome it
   names (expected output, exit code, color, a test name with its
   assertion, a fixture and result, a number with a threshold) and
   whether that outcome is tied to the issue's trigger. If the test
   plan is only a suite run, a feeling, or absent, record that.
5. Honesty: list every sentence of certainty marked in step 7 of the
   read order. For each, record whether the repro evidence or thread
   supports it, leaves it open, or contradicts it, and whether the
   plan marks it as a risk, open question, or deferral.
6. Thread engagement: take the direction list from step 5 of the read
   order. For each item, record where the plan or comment names it and
   whether the plan follows it or explains a divergence. If the plan
   is silent on an item, record "not addressed".
7. Conventions: take the AI-policy fact from step 3. Record whether
   the plan comment discloses AI assistance and how (a sentence naming
   the tool or the kind of help), and whether the comment reads
   first-person and specific to this issue. Record each non-AI ask
   and whether the plan meets or acknowledges it.

When evidence for a family is genuinely absent from the package (no
test plan, no cause, no files), record "absent" as the fact; absence is
evidence, not a reason to skip the check.

## Check execution

1. Execute the checks in rubric table order: cause-grounded,
   scope-bounded, executable, test-decisive, unknowns-honest,
   thread-engaged, conventions-respected, then repo-asks-followed.
   Grade every check, even after a required fail; the writer needs the
   whole picture.
2. For each check, apply the rubric's pass condition to the gathered
   facts only. Grade `pass` when the pass condition holds, `fail` when
   a fail clause holds, and `unclear` only when the evidence the check
   needs is genuinely not in the package and no fail clause covers its
   absence. Most absences are covered: no cause fails cause-grounded,
   no files or no chosen approach fails executable, no test plan
   fails test-decisive.
3. Write one line of evidence per check: the quote or fact that
   decided it. For cause-grounded, the line names the control that
   ruled the cause out or confirms every control is consistent. For
   scope-bounded, it names the extra item or states every change is
   needed. For executable, it names the file and the approach, or the
   deferred choice. For test-decisive, it quotes the observable
   outcome or the suite-only wording. For thread-engaged, it names the
   direction item and where the plan addresses it, or "owner's
   direction X not addressed". For conventions-respected, it quotes
   the policy scope and the disclosure sentence or its absence.
4. Judge the thing, not the write-up: never grade on section count,
   headings, length, or tone. A plan with no "Risks" heading passes
   unknowns-honest when it claims nothing beyond its evidence; a plan
   with a long risks section fails it when the body still declares an
   unmeasured cost settled. A polished plan that adopts the thread's
   diagnosis still fails cause-grounded when a control contradicts it.
5. A check may be graded from the gathered facts without re-reading
   the whole package when its evidence family was fully recorded in
   the gathering stage. Re-read the package only when a gathered fact
   is ambiguous, and then quote the resolving line.
6. The rubric's wording decides. If a check passes by its stated
   condition but feels wrong, it passes; note the tension in the
   summary so the rubric can be revised later.

## Verdict assembly

1. Collect the grades of the seven required checks. If every one is
   `pass`, the verdict is `accept`. If any one is `fail` or `unclear`,
   the verdict is `reject`; `unclear` on a required check is treated
   as `fail` exactly as the rubric's verdict rule says.
2. The preferred check (repo-asks-followed) is reported with its grade
   and evidence but never changes the verdict.
3. The deciding check is the first required check in table order that
   failed. Its evidence line is quoted in the summary as the reason
   for the hold. When more than one required check failed, name all
   of them in the summary so the writer fixes all of them before
   re-running.
4. In live mode, after the verdict, hold the plan comment against
   `voice-guide.md` and list any rule it breaks, quoting the rule.
   Voice notes never change the verdict.
5. Print the summary (one line per check, then the voice notes in live
   mode, then any procedure gap found during the run), followed by the
   fenced JSON block in the format SKILL.md gives, with nothing after
   it. The `item` field is the issue URL in live mode or the bundle id
   in eval mode; the `checks` array lists all eight checks in table
   order; `verdict` is `accept` or `reject`.
