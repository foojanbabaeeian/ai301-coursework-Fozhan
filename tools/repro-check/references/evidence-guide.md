# Evidence guide: where proof lives in a reproduction package

Every rubric check names a kind of proof. This guide says where each
kind lives, in an eval bundle and on GitHub, and what good looks like
when you find it. The rule that runs through all five families: read
the thing itself against the issue, never the write-up's shape. A long,
well-headed report can show the wrong behavior; a four-line one can be
complete.

Bundle anatomy, top to bottom: the **repo facts** block (repo line,
`bug reports` template line, `contribution policy` line), the **issue**
(title, opener and their association, body excerpt with the reporter's
environment, steps, actual, expected), the **thread highlights**
(maintainer notes that sharpen the trigger, other reporters, linked
PRs), the **candidate claim comment**, and the **candidate repro
report**. Live, the same five things are: the repo's CONTRIBUTING and
issue templates, the issue page, its comment thread, and the student's
two drafts.

## Environment

**Where it lives.** In the bundle: the repro report, usually a line or
block starting "Environment:" but sometimes folded into the first
sentence or a table. Read it next to two other places: the issue body's
own environment lines (the version and platform the reporter used, and
any "confirmed on latest and main" statement) and the `bug reports`
template line in the repo facts (what the maintainers ask every
reporter to record). Live: the draft's environment lines, the issue
page's environment section, and the repo's issue template under
`.github/ISSUE_TEMPLATE/`.

**What good looks like.** The OS, the project's version or commit or
build, and whichever component the issue says decides the outcome (a
driver, a shell, a browser language order, a build profile, a dependency
version) are all named. The versions match what the issue targets, or
the difference is called out in the report. One line can be enough:
"yq 4.53.3 (Homebrew), macOS 15.5 (arm64)" is a full record for a CLI
bug. "Windows 11" with no project version is not a record, and neither
is a report that names no environment at all, however good its
artifact; a stranger cannot place the attempt.

## Steps

**Where it lives.** In the bundle: the repro report's numbered steps
and fenced command blocks, plus any input file or config it shows,
read against the issue's own "steps to reproduce" and trigger command.
Live: the draft's steps, and the issue page's steps.

**What good looks like.** Each step is something a stranger can type or
click: exact commands, input files shown or described exactly enough to
recreate (quoting the issue's exact text or its script counts, and so
does naming the one distinguishing content of a minimal file), the
starting state stated (fresh directory, empty repo, fresh venv), and
every resource obtainable. Interactive steps written as key presses or
UI actions ("focus the file, press s, type a name, Enter") are steps.
The failures to look for: a step that depends on something private or
unshared (a company monorepo, an internal config), a generic step with
no command ("set up the project", "ran the reproduction"), a step that
drops an option the issue names as part of the trigger (the driver on a
driver-specific bug), or no steps at all, only a description of the
symptom.

## Behavior shown

**Where it lives.** In the bundle: the fenced output blocks, log
excerpts, tracebacks, exit codes, and described screenshots in the repro
report, read line by line against the behavior the issue's body names
(its error text, panic message, exit code, wrong output, or missing
output) and against any maintainer note in the thread that sharpens the
trigger ("the `--replace` flag is also required"). Live: the draft's
output blocks and attachments against the issue page.

**What good looks like.** An artifact shows the issue's behavior on the
issue's trigger or an equivalent one: the same error class and message,
the same wrong output, the same absence. A control run that shows the
non-triggering case is a bonus, not a requirement. Read the artifact
before the sentence that follows it; the sentence is the writer's
claim, the artifact is the evidence. Three ways an artifact fails to
show the issue: it shows only setup (a version banner, a session list,
tabs on screen) and never the symptom; it shows an adjacent behavior (a
graceful argument-validation error where the issue reports a panic or
abort, a compile error where the issue reports a runtime path error, a
parse error where the issue reports a panic, garbled text with the
process still alive where the issue reports a crash, exit 1 where the
issue says exit 101); or the input was quietly changed from the issue's
trigger (a different range syntax, a colon for an equals sign, a
rewritten expression) so that a different code path ran. When the
command in the report differs from the issue's, compare the outputs: if
the output matches the issue's, the trigger was equivalent; if it does
not, the report is testing something else.

## Honesty

**Where it lives.** In the bundle: the repro report's result line, its
"Expected" and "Actual" lines, its conclusion, and every sentence
carrying confidence or frequency ("100% reproducible", "on two
machines", "I verified", "guaranteed"), read against the artifacts in
the same report and against the issue's own expected and actual. Live:
the same sentences in the draft.

**What good looks like.** Every claim the report makes is backed by an
artifact in the report, and the conclusion goes no further than what
was run. Expected and actual are stated the same way round as the
issue states them. An honest cannot-reproduce is a pass: it says so up
front, shows what it ran and what it saw, and names what differed from
the report's conditions and what a triggering setup would likely need.
The failure shapes: a confident root cause with no transcript, a "can
confirm" with nothing shown, frequency or breadth claims doing the work
of a missing artifact, "expected" written as the buggy behavior, and a
conclusion that generalizes to a platform or build the report never
showed the behavior on (especially one the maintainers said they could
not reproduce on). A minor side observation without its own artifact
does not sink a report whose central claim is backed.

## Comms

**Where it lives.** Two places. The claim comment sits in its own
section of the bundle, read against the issue's title, body, and thread
(the specifics available to name). The repo's conventions sit in the
repo facts block: the `contribution policy` line (CONTRIBUTING.md
sections, AI policy files, what the policy says about disclosure and
about comments) and the `bug reports` template line. Live: the draft
claim comment, the issue page, and the repo's CONTRIBUTING.md, any
AI_POLICY.md, and the issue and PR templates under `.github/`.

**What good looks like.** The claim names something only this issue
has (the symptom in the writer's words, a version, a file, a thread
pointer, what they ran) and promises an action the writer controls:
investigate, reproduce, test the draft patch, read a code path, report
back. It does not promise a fix, a date, or ask to be assigned; a
modest claim that reports a failed attempt and a next step is a good
claim. Boilerplate is the tell: if the comment would read the same on
any other issue, it is not specific. For conventions, read the policy
line for what it requires and of what. Course packages are AI-assisted
work, so when a policy requires disclosing all AI usage, in any form,
or in comments and issues, at least one of the two comments has to say
that AI assisted and roughly how; silence there is a fail even when
every proof check passes. A policy that asks for comments in the
contributor's own words, or for the contributor to understand and own
the work, or that limits its disclosure ask to pull requests, is a
condition a first-person, issue-specific comment already meets;
AI-assisted is not AI-generated. A ban on fully AI-generated content
with assistive use allowed is the same kind of condition. The
bug-report template is the maintainers' checklist of what they will
ask for; a report that carries each applicable item, under any heading
or none, respects it.

## Reading the bundle honestly

Every bundle is frozen on the capture date at its top, and the real
issue has moved on since. Grade the snapshot: the gold labels describe
it, not today's GitHub. In eval mode the bundle is the whole world;
never fetch. In live mode the drafts are the candidate side and GitHub
is the issue side, and the package is what the drafts contain and
quote, not other files on the student's disk.
