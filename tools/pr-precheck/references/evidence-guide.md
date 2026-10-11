# Evidence guide: where evidence lives in a PR package

Every rubric check names a kind of evidence. This guide says where each
kind lives, in an eval bundle and in a live working copy, and what good
looks like when you find it. The rule that runs through all four
families: read the artifact itself (the hunks, the pasted output, the
stated asks) against the plan and the issue, never the description's
shape. A beautiful description with a perfect checklist can sit over a
hunk the plan never named; a one-paragraph description over a one-line
diff with its before and after shown can be ready.

Bundle anatomy, top to bottom: the **repo facts** block (repo line,
`pull requests` template line, `contribution policy` line), the
**issue** (title, opener, body with trigger, actual, expected), the
**thread highlights** (maintainer direction, linked PRs), the **plan
context** (the accepted plan this PR claims to implement, with the
repro evidence it built on: scope pair, named files, approach, test
plan, and any deviation sentences), and the **candidate PR** (title,
description, commits, diff, test evidence). Live, the same things are:
the repo's `.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md`;
the issue page and its thread; the student's `plan.md` with its
`## Deviations` section; the branch's diff from `git diff main...HEAD`
and commits from `git log main..HEAD`; the draft title and description
in `pr_draft.md`; and the captured output in `test_evidence.md`.

## Plan fidelity (harness category: silent-drift)

**Where it lives.** The plan side: the plan-context block's scope pair
("In scope" / "Not in scope"), its "Files" list, its numbered approach,
and any deviation sentence ("known limit, stated in the plan",
"deferred with a note"). Live: the "Scope", "Files", "Approach", and
"Deviations" sections of `plan.md`. The diff side: every file header
and every hunk in the candidate PR's diff, plus the commit list. Live:
the full output of `git diff main...HEAD`, not the description's
summary of it. The description side: every sentence in the PR
description and title that claims what the change does or does not do
("implements the posted plan exactly", "no functional changes outside
X", "now documents the limitation", "also adds"), read against the
hunks.

**What good looks like.** Every changed file and hunk falls inside the
plan's stated boundary or under a deviation note that names it and
gives a reason, and every change the plan committed to has a hunk, or
a note says why not. An honest shortfall re-ties a mismatch: the plan
defers `%VAR%` expansion with a note, the diff leaves it out, and the
description restates the deferral, so all three agree. The failure
shapes, in both directions: more than the plan (a one-line fallback fix
arriving with a `truncate()` rewrite and a new config option the plan
scoped out; a theming fix bundled with a renaming pass and a new
setting; a second hunk that silently rewrites a file the plan never
touches), and less than the plan with no note (the plan promises a
warning and a docs update, the diff has only the warning). The
description's claims live here too: "no changes beyond the plan" over
an uncovered hunk, or docs that "now document the limitation" with no
docs hunk, is silent drift, graded from the diff and never from the
description's confidence.

## Test evidence (harness category: not-tested)

**Where it lives.** The candidate PR's "Test evidence" section:
fenced blocks of commands with their output, before and after, and
any sentence reporting the repo's checks. Read it against two things
in the plan context: the test plan (which failure modes it names and
what it expects after) and the repro evidence's steps and trigger
(what the bug's own command is). Live: `test_evidence.md`, read
against the "Test plan" section of `plan.md` and the repro steps in
the student's posted repro comment; plus the checks the repo names in
its PR template and CONTRIBUTING file (for Path Review: `make
test-unit`, `make test-integration`, `make lint`, `make typecheck`).

**What good looks like.** The issue's trigger re-run on the change,
with the before and the after both visible as output or a transcript
and an observable outcome named: "before: `DuplicateOptionError`;
after: `parsed OK`", a before/after table, an exit code that moved
from 1 to 0, a named test that now passes. Every failure mode the
plan's test plan commits to is shown, or its omission is noted with a
reason. The repo's own checks appear with their outcome, including an
honestly reported failure with its cause ("no tests ran" for an empty
integration suite is an outcome). The failure shapes: prose standing
in for output ("tested locally, colors work now", "verified working
on my machine for a full day", "cargo test passes" with nothing
pasted); evidence that exercises only a control or the unchanged
path (a GET with header casing where the bug is a single-header POST;
the single-file case where the bug needs two files and `go build`);
one of two planned failure modes re-run while the other is silent;
or no evidence at all. An added test that covers a different case
than the fix is not a substitute for the re-run.

## Diff quality (harness category: unreviewable)

**Where it lives.** The candidate PR's diff, hunk by hunk, and its
commit list. Live: `git diff main...HEAD` and `git log main..HEAD
--oneline`. Read each hunk for what it adds, then ask whether the fix
needs it.

**What good looks like.** Every added line is the fix, its test, its
documentation, or a recorded deviation, and the commits say what they
change ("themes: remove shadowed file-decoration-style"). The debris
tells: a debug print, `eprintln!`, or logger call added for
investigation and left in; a commented-out line or block of code (a
first attempt, a disabled probe); a function or variable nothing calls,
including one hidden behind `allow(dead_code)` or a `noqa`; a `TODO` or
`FIXME` the change introduced; a stray file; commit messages like
"wip", "fix", "fmt + cleanup", "misc cleanups while debugging". The
churn tells: a re-indent or formatting hunk on logic the fix does not
touch; import reordering or restructuring; two identical re-printed
blocks; reworded help text or docs for an unrelated flag; a cleanup of
code the issue never mentions. One debris line or one churn hunk makes
a diff a reviewer must read twice, and the fix being right does not
buy it back.

## Standards and comms (harness category: standards-wall)

**Where it lives.** The repo side: the repo-facts block's `pull
requests` line (what the PR template requires: a `closes #N` line, a
checklist, named sections, a changelog or whatsnew entry in the diff,
a type-of-change selection) and its `contribution policy` line (the
contributing guide's asks and any AI-use statement: what it requires
and of what). Live: `.github/PULL_REQUEST_TEMPLATE.md` and
`docs/CONTRIBUTING.md` in the Path Review repo (the template's
Summary, Issue, Changes, Testing checklist, Screenshots, and Notes for
Reviewers sections; the contributing guide's branch and commit
conventions and its xfail-marker rule for seeded bugs). The PR side:
the description's sections and their content, the `closes #N` line,
which checklist items are ticked, the diff (for a required entry), and
any AI-use sentence. Live: `pr_draft.md`. Thread direction, when the
thread has it, is engaged the way the plan beat graded it: named and
followed, or named and declined with a reason.

**What good looks like.** Each required section holds real content
about this change (a Problem and a Solution that describe this fix,
not a placeholder); the issue is referenced the way the template asks;
checklist items are ticked only where the evidence supports them; a
required whatsnew or changelog entry is in the diff, not only promised;
and when the policy requires disclosing AI use for pull requests or
all usage in any form, the description says that AI assisted and
roughly how, in the author's own words. Course packages are AI-assisted
work, so silence under such a policy fails even when the fix is
excellent. A policy that asks for the contributor's own words, for the
contributor to understand and own the work, or that allows assistive
use while banning fully generated content, is a condition a
first-person, change-specific description already meets. The failure
shapes: the required checklist visibly ignored (no `closes #`, no
checklist, the stated whatsnew entry absent from the diff); a required
section left as the template's placeholder; a strict all-usage
disclosure policy with no disclosure in the description. Whether the
description's claims match the diff is plan fidelity, above, not a
standards question.

## Reading the bundle honestly

Every bundle is frozen on the capture date at its top, and the real PR
or issue has moved on since. Grade the snapshot: the gold labels
describe it, not today's GitHub. In eval mode the bundle is the whole
world; never fetch. In live mode the drafts, the committed diff, and
the evidence file are the candidate side and GitHub is the repo side;
uncommitted edits and other files on disk are not part of the package.
A live PR whose evidence file is missing is graded as if the evidence
were absent.
