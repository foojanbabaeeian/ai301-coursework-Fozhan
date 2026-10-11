# Evidence guide: where evidence lives in a plan package

Every rubric check names a kind of evidence. This guide says where each
kind lives, in an eval bundle and on GitHub, and what good looks like
when you find it. The rule that runs through all six families: read
the plan itself against the issue and its repro evidence, never the
write-up's shape. A long, confident plan with headings can blame the
wrong component; four short paragraphs can be ready to build from.

Bundle anatomy, top to bottom: the **repo facts** block (repo line,
`bug reports` template line, `contribution policy` line), the **issue**
(title, opener and their association, body excerpt with trigger,
actual, expected), the **thread highlights** (maintainer and owner
notes, proposed or rejected approaches, patches posted for testing,
linked PRs), the **repro evidence** (the accepted week-2 report the
plan builds on: environment, steps, artifact, control runs, expected,
actual), the **candidate plan**, and the **candidate plan comment**.
Live, the same six things are: the repo's CONTRIBUTING.md, AI policy,
and templates; the issue page; its comment thread; the student's
posted repro comment (or the house repro pack as quoted in the
drafts); and the student's `plan.md` and `comment.md`.

## Diagnosis and grounding

**Where it lives.** In the bundle: the plan's first section, usually
headed "Cause", "Diagnosis", or "Root cause", sometimes a single
sentence beginning "as the issue states" or "grounded in the owner's
analysis". The evidence it must survive lives in the repro-evidence
block: the artifact on the trigger (the traceback, the wrong output,
the panic line), and every line labeled "Control" or every numbered
step that changes one input and reports what came out. Also read the
thread highlights for a maintainer's own analysis, because a plan that
adopts it is only as grounded as the evidence that agrees with it.
Live: the "Diagnosis" section of `plan.md`, read against the output
blocks and control runs quoted in the student's posted repro comment,
and against the issue thread.

**What good looks like.** The stated cause would produce exactly the
artifact shown, and every control in the repro evidence behaves the way
that cause predicts. Example: a plan that blames a missing refresh of
one view's model, where the repro shows the color updates only after
re-entering that view, is grounded. The failure shapes: a control run
on the same input with the blamed component removed or bypassed still
fails (the cost appears with no pager in the loop, so the pager
bindings are not the cause); a control shows the blamed component
working (the same items parse fine without the flag, so the tokenizer
is not the cause; an instance-method friendly error prints in the same
build, so the module is not tree-shaken out); the damage is already
visible in an earlier step than the stage the plan blames (the zeros
are gone in the inferred table before any cast runs); or no cause is
stated at all and the plan goes straight to changes. Read the controls
before the diagnosis.

## Scope

**Where it lives.** In the bundle: the plan's "Scope", "Change", or
"Proposed changes" section, including any "In scope" and "Not in
scope" or "Out" line, and its numbered change list; read against the
one behavior the issue body reports and the repro isolates. Live: the
"Scope" section of `plan.md` and its files list, against the issue
body.

**What good looks like.** One bounded change: every item in the list
is needed either to stop the reported behavior or to verify that it
stopped, and the plan names what it leaves out. Deferrals with reasons
are bounded ("option 1 is a bigger rework than this bug needs"; "the
Windows variant is untestable here, deferred and stated"). A docs-only
plan is bounded when the docs address the reported behavior. The
failure shapes: a library migration ("replace node-fetch with undici
while touching the network stack"), a rewrite of the surrounding
module or a state machine, a new option, setting, or settings-panel
field, a CI or test-harness migration, a second bug fixed "while in the
area", or a multi-front campaign where the actual fix for the reported
behavior is one bullet among five. Offering to split the campaign into
a PR series does not make it bounded; the scope is what the plan
proposes.

## Executability

**Where it lives.** In the bundle: the plan's "Files", "Files and
areas", "Approach", or numbered steps, and any file path or function
name in backticks anywhere in the plan. Live: the "Files" and
"Approach" sections of `plan.md`.

**What good looks like.** A stranger with the repo checked out could
start: at least one concrete file, function, module, or document is
named, and one mechanism is chosen (what is added, removed, or changed
where, in what order). "The push completion callback in
`sync_controller.go` adds the commits context to its refresh scope" is
executable in one sentence. An open question about a detail does not
sink a plan whose main approach is chosen. The failure shapes: no file
or area at all; the approach still a choice ("gocui? tcell? not sure";
"upstream or vendored, whichever is easier"; "recover() somewhere");
the first step is to investigate or profile rather than to change
something; or the change is described only by its goal ("make startup
fast", "handle the error properly") with no mechanism.

## Test plan

**Where it lives.** In the bundle: the plan's "Test plan" or "Test"
section, read against the repro evidence's steps, trigger, and
artifact (the test should re-run that trigger and name what changes).
Live: the "Test plan" section of `plan.md`, read against the repro
steps the student posted in week 2.

**What good looks like.** At least one observable outcome tied to the
issue's trigger that a reader could watch pass or fail: the repro
steps re-run with the expected output, exit code, color, or absence
named ("at step 3 the color must flip without leaving the view";
"repro re-run, expected exit 0"); a named test case with its assertion;
a fixture with the expected result ("each of the three spellings
prints `C:\t\fixture\src\foo\a.spec.ts`"); or a measured number with a
threshold. Both of the issue's own cases added as regression tests
count. The failure shapes: "run the full test suite and make sure
nothing regresses" as the only test; a feeling ("should feel fast",
"nothing else should feel broken"); an artifact nobody could check from
the plan; or no test plan at all.

## Honesty

**Where it lives.** In the bundle: the plan's "Risk", "Risks and
unknowns", or "Open question" lines, and every sentence anywhere in the
plan or comment that states certainty, cost, compatibility, or
completeness ("confirmed", "the cheapest", "no behavior change",
"covers the whole failure surface", "will land", "this week"). Read
each against the repro evidence, the thread, and the plan's own stated
unknowns. Live: the "Risks and unknowns" section of `plan.md`, the same
sentences in `plan.md` and `comment.md`, and after the build the
"Deviations" section, where an honest mid-build change is recorded.

**What good looks like.** The plan claims no more than the repro
evidence and thread show, and marks what it has not measured or tested
as a risk, an open question, or a deferral ("I have not yet measured
the per-print cost; if it shows up in the benchmark I will move the
check"). A terse plan with no risks section passes when it makes no
claim beyond its evidence. The failure shapes: a platform or variant
declared fixed that the repro never ran on; a performance or
compatibility claim with no measurement ("no behavior change" for a
normalization never tested on the other platform); a cause stated as
certain that a control disputes; a promised date or guarantee. In live
mode a deviation recorded under "Deviations" with its reason is honest
work; a deviation visible only in the diff is not.

## Comms

**Where it lives.** Two places. The thread side: the thread highlights
(eval) or the live issue thread, read for explicit maintainer, owner,
or collaborator direction: a named culprit file or line range, a
proposed or rejected mechanism, a patched binary or draft PR posted
with a request to test it, a works-as-intended note, a prior-art PR.
Then the candidate plan comment and the plan's scope and approach,
read for where they name that direction. The repo side: the repo facts
block's `contribution policy` line (CONTRIBUTING.md sections, AI
policy files, what they require and of what) and its `bug reports` or
template line, read against the plan comment. Live: the issue thread,
the repo's CONTRIBUTING.md, any AI_POLICY.md, and the templates under
`.github/`, against `comment.md`.

**What good looks like.** Thread-aware: the comment names the
direction and follows it ("my plan follows the direction proposed
here: a generation counter bumped on capacity changes"), engages a
prior PR instead of racing it ("I have read PR #2089; if it lands first
I will rebase my tests onto it"), or explains a divergence ("this
proposal deliberately stays out of the ignore crate, per the note that
the deep fix is hard"). When the thread has no direction, there is
nothing to engage and the check passes. The failure shape is silence: a
docs-only workaround where the owner has already named the culprit
file, posted a patched binary, and asked for testing, and the comment
mentions none of it; a mechanism the thread rejected as too expensive,
proposed again; an open PR on the same fix the comment never names.

Conventions: read the policy line for what it requires and of what.
Course packages are AI-assisted work, so when a policy requires
disclosing all AI usage in any form, or AI use in issues and comments,
the plan comment has to say that AI assisted and roughly how; silence
there fails even when the plan is otherwise excellent. A policy that
asks for the contributor's own words, for the contributor to understand
and own the work, or that limits its disclosure ask to pull requests
or code, is a condition a first-person, issue-specific comment already
meets; AI-assisted is not AI-generated. A ban on fully AI-generated
content with assistive use allowed is the same kind of condition. Other
asks (review bandwidth is scarce, first-time contributors go through a
vouch flow, tests expected) are the preferred nudge: a comment that
keeps the change minimal where review time is scarce, or notes the
vouch flow, meets them.

## Reading the bundle honestly

Every bundle is frozen on the capture date at its top, and the real
issue has moved on since. Grade the snapshot: the gold labels describe
it, not today's GitHub. In eval mode the bundle is the whole world;
never fetch. In live mode the drafts are the candidate side and GitHub
is the issue side, and the package is what the drafts contain and
quote, not other files on the student's disk. In particular, a live
plan that relies on repro evidence it does not quote is graded as if
that evidence were absent.
