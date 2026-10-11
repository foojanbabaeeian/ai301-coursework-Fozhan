# Procedure: how this tool grades a PR package

These are the operating steps. `rubric.md` says what to decide,
`references/evidence-guide.md` says where each kind of evidence lives,
`SKILL.md` says what the tool reads in each mode, and this file says in
what order to read, how to gather, how to grade, and how to assemble
the verdict. The reads that matter this week are side by side: the
diff against the plan, the evidence against the test plan, the
description against the diff. Follow the steps as written; where a
step is silent, report the gap in the summary rather than inventing
one.

## Read order

1. In live mode only: read `scope.md`. Confirm the issue URL is in the
   scoped repo; if the `Repo:` line still holds a placeholder, stop and
   say so. Note the house rules (one PR per issue per student, branch
   on the fork, a classmate's PR does not block). In eval mode skip
   this step.
2. Read `rubric.md` and list its checks in table order with their
   weights, then read `references/evidence-guide.md` so each check's
   evidence family has a known location.
3. Read the repo-facts block (eval) or the repo's PR template and
   CONTRIBUTING file (live). Write down three facts: (a) every section
   or item the PR template requires, and whether any required artifact
   must appear in the diff (a changelog or whatsnew entry); (b) the
   checks the repo names (suite, lint, typecheck, pre-commit, CI jobs);
   (c) whether the contribution policy makes any statement about AI use
   and, if so, what it covers (pull requests, all contributions, "all
   usage in any form", a ban).
4. Read the issue title and body, then the thread highlights (eval) or
   the live thread. Write down the one reported behavior in a sentence
   (trigger, actual, expected) and any explicit maintainer direction.
5. Read the plan context (eval: the plan-context block, including the
   repro evidence it built on; live: `plan.md` whole, including
   `## Deviations`). Write down, as a list: every file the plan names;
   the in-scope and not-in-scope lines; each change the plan commits
   to; each failure mode or step the test plan names with its expected
   after; and every deviation note with its reason. This list is the
   ruler every later side-by-side is measured with, so it is written
   before the diff is opened.
6. Read the commit list, then the diff, hunk by hunk, BEFORE the
   description. For each hunk write down: the file, what the hunk does
   in one phrase, and whether it is the planned fix, its test, its
   docs, a recorded deviation, an unplanned addition, churn (formatting,
   re-indent, import reorder, duplicated lines, reworded unrelated
   text), or debris (debug output, commented-out code, dead definition,
   new TODO, stray file). Also note any planned change with no hunk.
7. Only now read the PR title and description. Mark every claim about
   what the change does, does not do, or leaves out, every ticked
   checklist item, every template section and whether it holds real
   content, and any AI-use sentence.
8. Read the test-evidence section last. For each block, write down
   what command or step it shows, whether it is the issue's trigger or
   a control, whether both a before and an after are visible as output,
   and which repo checks it shows with their outcome.

The diff is read before the description because description-matches-
diff and diff-matches-plan are decided by the hunks; reading the
description first makes the hunks look like confirmation of whatever
the description claimed.

## Evidence gathering

For each family, pull the fact from the place named and record it as a
quote or a one-line fact. In live mode use the locations the evidence
guide names for the working copy and GitHub; the package is the diff,
the drafts, and the evidence file, never uncommitted edits or other
files on disk.

1. Plan fidelity, diff side: take the hunk list from read-order step 6
   and the plan list from step 5. Pair them: for each hunk, record the
   plan line or deviation note that covers it, or "uncovered". For each
   planned change, record the hunk that delivers it, or "missing" and
   whether a deviation note explains it.
2. Plan fidelity, description side: take each claim marked in step 7.
   For each, record the hunk that makes it true, or "no hunk" when the
   diff lacks it, or "contradicted by <hunk>" when a hunk does what the
   claim denies.
3. Test evidence: take each failure mode or step from the plan's test
   plan (step 5). For each, record the evidence block that re-runs it
   with before and after visible, or "not shown". Record whether any
   shown block exercises only a control or unchanged path. Record each
   repo check from step 3(b) and the outcome the evidence shows for it,
   or "not shown".
4. Diff quality, debris: list every added line that is a debug print or
   log, a commented-out code line, a definition nothing calls, a TODO
   or FIXME, or a stray file, with its file and hunk. List the commit
   messages. Record "none" when the list is empty.
5. Diff quality, unrelated hunks: list every hunk tagged churn or
   unplanned in step 6 with its file and what it changes. Record
   "none" when the list is empty.
6. Standards: take the template requirements from step 3(a). For each,
   record where the description meets it (the section with its
   content, the `closes #N` line, the ticked item and the evidence
   behind it) or "missing" or "placeholder". For a required diff
   artifact, record the hunk or "absent from diff".
7. Disclosure: take the AI-policy fact from step 3(c). Record whether
   the description discloses AI assistance and how, and whether it
   reads first-person and specific to this change.

When evidence for a family is genuinely absent from the package (no
test evidence, no description, no commit list), record "absent" as the
fact; absence is evidence, not a reason to skip the check.

## Check execution

1. Execute the checks in rubric table order: diff-matches-plan,
   description-matches-diff, repro-rerun-shown, repo-checks-run,
   no-debris, no-unrelated-hunks, template-honored,
   disclosure-per-policy. Grade every check, even after a required
   fail; the writer needs the whole picture.
2. For each check, apply the rubric's pass condition to the gathered
   facts only. Grade `pass` when the pass condition holds, `fail` when
   a fail clause holds, and `unclear` only when the evidence the check
   needs is genuinely not in the package and no fail clause covers its
   absence. Most absences are covered: no test evidence fails
   repro-rerun-shown; a required template section missing fails
   template-honored; a planned change missing with no note fails
   diff-matches-plan.
3. Write one line of evidence per check naming both sides of the
   pairing: for diff-matches-plan, the uncovered hunk and the plan's
   not-in-scope line, or "every hunk covered, every planned change
   present"; for description-matches-diff, the claim and the hunk that
   contradicts it or the missing hunk; for repro-rerun-shown, the test-
   plan step and the evidence block, or the step that is "not shown";
   for no-debris, the file and the debris line; for no-unrelated-hunks,
   the churn hunk; for template-honored, the required item and where it
   is met or missing; for disclosure-per-policy, the policy scope and
   the disclosure sentence or its absence.
4. Judge the artifact, not the write-up: never grade on the
   description's length, the commit count, or the checklist's
   neatness. A perfect checklist over a hunk the plan never names
   fails diff-matches-plan; a one-paragraph description over a one-
   line diff with its before and after shown passes everything.
5. Apply the honest-shortfall rule: when a planned change is missing
   but the plan's deviation note and the description both say so with
   a reason, diff-matches-plan and description-matches-diff pass on
   that point, and repro-rerun-shown is measured against the test plan
   as amended by the note. Silence in the plan, in the description, or
   in both is what fails.
6. A check may be graded from the gathered facts without re-reading
   the package when its family was fully recorded in the gathering
   stage. Re-read only when a gathered fact is ambiguous, and then
   quote the resolving hunk or line.
7. The rubric's wording decides. If a check passes by its stated
   condition but feels wrong, it passes; note the tension in the
   summary so the rubric can be revised later.

## Verdict assembly

1. Collect the grades of the seven required checks. If every one is
   `pass`, the verdict is `accept`. If any one is `fail` or `unclear`,
   the verdict is `reject`; `unclear` on a required check is treated
   as `fail` exactly as the rubric's verdict rule says.
2. The preferred check (repo-checks-run) is reported with its grade
   and evidence but never changes the verdict.
3. The deciding check is the first required check in rubric table
   order that failed. Its evidence line is quoted in the summary as
   the reason for the hold. When more than one required check failed,
   name all of them in the summary so the writer fixes all of them
   before re-running.
4. In live mode, after the verdict, hold the PR title and description
   against `voice-guide.md` and list any rule they break, quoting the
   rule and the offending sentence. Voice notes never change the
   verdict.
5. Print the summary (the mode, one line per check, then the voice
   notes in live mode, then any procedure gap found during the run),
   followed by the fenced JSON block in the format SKILL.md gives, with
   nothing after it. The `item` field is the issue URL in live mode or
   the bundle id in eval mode; the `checks` array lists all eight
   checks in table order; `verdict` is `accept` or `reject`.
