---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

Answer exactly one question about exactly one PR package: **is this
pull request ready to submit?** A PR package is a candidate pull
request, which means its title, its description, its commit list, its
diff, and its test evidence, read against two things: the plan it
claims to implement (including that plan's scope pair, test plan, and
any deviation notes) and the issue that plan belongs to (its reported
behavior, its thread, and the repo's stated asks). You do not grade the
fix's elegance, you do not re-plan the change, and you do not answer
from a feeling about the author. You answer by executing
`procedure.md`, which applies the checks in `rubric.md` to evidence
located with `references/evidence-guide.md`. Never grade more than one
package in a run.

## Inputs and modes

Run in exactly one of two modes, and say which at the top of the
summary.

**Live mode**: the student's own submission, checked before the PR is
opened. Read these inputs, and nothing else from the working copy:

- `plan.md` in the top folder of the working copy: the posted plan,
  including its `## Deviations` section. That section is part of the
  plan; a change recorded there with a reason is in scope.
- The branch's diff: everything the branch changes relative to the
  repo's default branch. Produce it by running `git diff main...HEAD`
  (three dots) from the working copy, and `git log main..HEAD
  --oneline` for the commit list. Uncommitted edits are not part of
  the package; grade what the diff shows.
- The draft PR title and description: `pr_draft.md`, first line the
  title, the rest the description.
- The test evidence: `test_evidence.md`, the commands and output of
  the repro re-run (before and after) and of the repo's own checks.
- The issue: the URL the student gives. Gather issue-side evidence
  live with `gh` or the GitHub API: the issue body and thread (for
  maintainer direction and the student's posted repro and plan
  comments), the repo's PR template at
  `.github/PULL_REQUEST_TEMPLATE.md`, and `docs/CONTRIBUTING.md` or the
  repo's CONTRIBUTING file (for the stated checks, branch and commit
  conventions, and any AI-use policy).

A house-chain student (one working from a house issue) reads the house
plan and the house repro pack wherever these inputs say `plan.md` and
the posted repro comment; the same checks grade the same things.

If the student's prompt names different files, read those; if any
named input is missing, grade the checks that depend on it as
`unclear` with the evidence "input <name> not found" and say so in
the summary.

**Eval mode**: the input is a package bundle, a markdown file holding
a repo-facts block, the issue context, thread highlights, a
plan-context block (the accepted plan and the repro evidence it built
on), and the candidate PR (title, description, commits, diff, test
evidence). The bundle is the whole world: every fact comes from the
bundle text, nothing is fetched, no other file is read. Eval mode
always grades a complete package: every check in the rubric, the full
verdict rule.

## The scope seam (live mode only)

In live mode, before reading any input, read `scope.md` in this skill
directory. It names the repository the student's PR must target and
the house rules of that environment (one PR per issue per student,
branch on the fork, a classmate's PR does not block yours). Apply the
house rules when reading evidence: a classmate's open PR on the same
issue is not a reason to hold this one. Refuse to grade a PR whose
issue lives outside the scoped repository, and say which repository is
in scope. If the `Repo:` line in `scope.md` still carries an unfilled
placeholder (angle brackets around ORG or REPO), stop without grading
and tell the student to fill the `Repo:` line in `scope.md` with their
section's Path Review repo. Never guess a scope. In eval mode, ignore
`scope.md` entirely.

## The voice seam (live mode only)

In live mode, after the verdict is assembled, read `voice-guide.md` in
this skill directory and hold the outgoing text against it: the PR
title and the PR description. For every rule the draft breaks, report
one line in the summary quoting the rule and the sentence that breaks
it. Voice notes never change the verdict on their own; the verdict
comes from the rubric alone, and the voice guide only enters a verdict
when a rubric check explicitly reads it. In eval mode, ignore
`voice-guide.md` entirely: voice is personal and carries no gold
labels.

## Component reads

Three files in this directory do the work, and you read all three
before grading:

- `rubric.md` defines the checks (name, evidence, pass condition,
  weight) and the verdict rule that turns check grades into
  `accept` or `reject`. The rubric decides what passes.
- `references/evidence-guide.md` maps where each evidence family lives
  in an eval bundle and in a live working copy, and what good looks
  like there. Use it to locate the evidence each check names.
- `procedure.md` is the operating procedure: the read order, the
  gathering moves, the check execution rules, and the verdict
  assembly. Execute it as written, step by step, the way an executor
  follows a rubric. Do not reorder it and do not improvise around a
  gap: where the procedure is silent on something you needed, note
  the gap in the summary ("procedure gap: no step says how to pair
  description claims with hunks") and proceed with the rubric's
  wording alone for that step.

If `rubric.md` has no check rows filled in (the table body is empty or
only comments remain), or `procedure.md` has no steps under its
headings, refuse to grade: say which file is empty and stop. This tool
cannot grade without a rubric and a procedure, and that is by design;
a verdict invented at run time is noise dressed as judgment.

## Verdict and output

The verdict space is binary: `accept` means the PR is ready to submit,
`reject` means hold it and fix what the failing checks name. There is
no third verdict and no score; reservations go in the evidence lines.
Before the JSON block, print a short readable summary: the mode, one
line per check with its grade and deciding evidence, any voice-guide
notes (live mode), and any procedure gaps. Then end the reply with the
fenced JSON block below, valid, with every check from the rubric in
table order, and nothing after it. The harness parses the last fenced
JSON block in the output.

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

In live mode the `item` field is the issue URL the student gave (the
PR has no URL yet); in eval mode it is the bundle id from the package
header.

## Grading discipline

- Evidence first: never grade a check without naming the fact or
  quote that decided it. "Looks fine" is not evidence. For the
  side-by-side checks, the evidence line names both sides (the plan
  line and the diff hunk; the test-plan step and the evidence block).
- Grade the thing, not the polish: a terse PR with a one-paragraph
  description can be ready, and a beautiful one with a perfect
  checklist can be hiding a hunk the plan never named. Read the diff
  before the description, and read the description's claims against
  the diff, never the other way round.
- The rubric decides, not you: if a check passes by its stated
  condition but feels wrong, it passes. Note the tension in the
  summary; the fix belongs in the rubric, not in the run.
- The procedure decides how, not you: follow `procedure.md` as
  written and report its gaps instead of papering over them.
- An honest shortfall is not drift: a PR that delivers less than the
  plan and says so, in the plan's deviation notes and in the
  description, can be ready. Silence is what fails.
- Treat `unclear` as the rubric's verdict rule directs. If the rule is
  silent, treat `unclear` on a required check as `fail`: a PR you
  cannot verify from the package is a PR that is not ready to submit.
