# Contribution: feat(exporters): add Prometheus Pushgateway metrics exporter

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** 1
**Student:** Fozhan Babaeiyan Ghamsari
**GitHub Username:** foojanbabaeeian
**Issue:** https://github.com/Zwony/ecotrace/issues/232
**Pull Request:** https://github.com/Zwony/ecotrace/pull/250

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `[1]` unless you make more than one contribution this term; it
just numbers them.

---

## Toolkit

Every story section below, from Why I Chose This Issue through Pull
Request, ends with a Toolkit block: which tool did that phase's work
and how you used it, what it produced, what difficulties or
shortcomings it presented, and what you changed or would change
because of them. Together the blocks are the thread the rubric's
Toolkit points read, and they are the raw material for your unit-9
portfolio, so keep them current as you work rather than
reconstructing them later.

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

This section also keeps one short dated entry per week of tool work,
starting below: what you built or changed that week, what broke, and
what you changed because of it. The blocks tell the story where it
happened; these entries are the toolkit's own timeline.

All four entries are graded. Keep every heading below, and fill each
one in the week it belongs to.

**Unit 5: Turned my unit 1 grader into issue-scout and ran it on GitHub and the course site**

I wrote three `gh search issues` queries from my fit profile (Flask, pygame,
scikit-learn, PyTorch, Flutter) and six ranking rules, after a plain "good first
issue + Python" search came back with 54 of 100 results from one personal repo.
The real scan graded five issues and accepted one, but its top five ignored my
"avoid frontend" line because my ranking had no fit step, and three of the five
already had open PRs that my "fewest comments first" order could not see. When I
checked the course site's list the same way, six issues that looked free had a
claim comment in the thread, so I logged that the unclaimed check has to read
comments and read every thread myself before grading my picks (ecotrace #232 and
codex-lb #1870, both accepted); I also logged that the scope check passed the much
larger codex-lb issue too easily, to revisit with next week's scope patch.

**Unit 6: Installed the voice guard with my upstream rules and wrote ecotrace's repo memory file**

I merged my three voice-guide copies into one guide at
`~/.claude/voice-guard/voice-guide.md`, added five upstream rules (claims,
questions, pings), and hooked the guard into Claude Code. It broke in two ways at
first: the manual check failed because Python could not find `claude.exe`, and a
draft with an emoji crashed the checker on Windows' cp1252 encoding and would have
let the post through unchecked, so I added the real `claude.exe` folder to my
user PATH and set `PYTHONUTF8=1`, and re-tested an emoji ping (stopped), a filler
draft (stopped), and a clean claim (passed). It then stopped my real claim for
asserting the `ImportError` without showing it, under my own "Show it, do not
assert it" rule; I pasted the command and output, and the hook passed it when I
posted. My `CLAUDE.local.md` for ecotrace records what took longest to learn: the
exporter dispatch passes `run_id` and `run_label` even though `add_exporter`'s
docstring lists four arguments, and `flake8 ecotrace/` already fails on main.

**Unit 7: Installed the ship-gate, wrote ecotrace's manifest, and shipped PR #250 through it**

My manifest runs ecotrace's `pytest tests/` and flake8 on the new module, written
as `.venv\Scripts\python.exe -m ...` because the gate runs each check in a fresh
Windows `cmd` without my venv. The whole-package `flake8 ecotrace/` went in the
excluded table with a date, because it already fails on main with 759 warnings.
On my real branch the gate passed the tests (266 passed in 136 s), the lint, and
all eight pr-precheck checks, and let `gh pr create` through. What fought me came
before the gate: the voice guard (which runs in the same hook) stopped the PR
description three times. The first stop was emoji copied from ecotrace's own PR
template; the other two were results I described without pasting the output. I
kept every template item but dropped the emoji, and pasted the Pushgateway and
planted-bug output into the description.

**Unit 8: Wrote my playbook from ecotrace's real response times and ran the PR monitor on PR #250**

My playbook sets a 4-day first-review threshold. I picked it after measuring
that Zwony answered the six most recent merged outside PRs within 13 minutes to
14 hours, while open PR #248 had waited about 2 days. It also has one-nudge
rules taken from my voice guide's ping rule, a rule that bot comments are not
feedback, and walk-away criteria. The monitor read PR #250 (open, merge state
blocked pending review, SonarCloud passing) and labelled it `FEEDBACK-IN`,
because its state check counts the SonarCloud bot's "Quality Gate passed"
comment as review feedback. The draft it produced, guided by my bot rule and
threshold, was "wait, nothing to send", with the first possible nudge on
2026-11-09. I sent nothing, and will re-run it after that date if no maintainer
has commented.

---

## Why I Chose This Issue

I found this issue on the AI301 Issue Selection site. My filters (Python,
TypeScript, C++, Shell, HTML; tags documentation, enhancement, bug, new feature,
ui) gave 2,407 issues. I checked the 883 Python/JS/TS/HTML ones against GitHub for
assignees, open PRs, and live repos, then read the threads of the best-looking
survivors, because six of them turned out to have a fresh claim comment that the
batch check could not see. That left two picks, `Zwony/ecotrace#232` and
`Soju06/codex-lb#1870`. My scout graded both `accept`. After the unit 6
scope patch it called ecotrace #232 **straightforward** (the issue names the file to
create, the exporter to copy, and the test file) and codex-lb #1870 **ambitious**
(backend API, a React multi-select, every locale, and PostgreSQL tests). My own
scan of GitHub, separately, accepted only one issue, a PyTorch compiler
internals task that fit my profile badly. I chose ecotrace #232 because it is pure
Python, needs no infrastructure I can't run, and is mostly about writing a small
module and good tests, which is exactly what my fit profile says I want to get
better at. It is also a feature request filed by the owner with an exact spec,
so I could finish it before the deadline. I hope to learn how a project's
exporter interface and test patterns shape a new contribution, and how a
maintainer reviews a first PR from a stranger.

The scout was useful as a second opinion but not as a search engine. It never
proposed this issue; the course site did. Its scan's ranking had no fit step, so
Flutter UI issues reached my top five, and my "fewest comments first" order
surfaced issues that had just been claimed. Its old scope check also passed the
much larger codex-lb issue on a technicality. What held up was the per-issue
grading: every claim, open PR, and stale repo it reported was real when I checked.
I replaced the scope check with `scope-feasible` and its difficulty note, which is
what finally told the two picks apart, and I logged ranking fixes I have not made
yet (a per-query quota, and naming the Development-box PR list in the rubric).

Claim comment: https://github.com/Zwony/ecotrace/issues/232#issuecomment-6646012183

**Toolkit**

- Tool: issue-scout, with my search strategy (three `gh search issues` queries
  scoped to the projects in my fit profile, six ranking rules) and my unit 1
  rubric. I ran one scan, graded my two site picks, patched the rubric, and
  re-graded them.
- What it produced: the scan graded five and accepted one (pytorch #113040).
  Both site picks graded `accept`; after the patch, ecotrace #232 came back
  "straightforward" and codex-lb #1870 "ambitious".
- Difficulties: the ranking ignored fit, comment count picked freshly claimed
  issues, the rubric's "linked PRs" was ambiguous, and `scope-bounded` passed a
  multi-day feature because the issue called its seven parts "one focused PR".
- What changed: replaced `scope-bounded` with `scope-feasible` plus the
  difficulty note (unit 6 patch), logged in `scan-notes.md`. Ranking and the
  PR-list wording are logged as next fixes, not made, because my real issue came
  from the site and another $1 scan would not have changed the pick.

---

## Understanding the Issue

**Problem Description**

EcoTrace measures the carbon footprint of Python functions and can send each
measurement to exporters (a cloud service, a webhook, or OpenTelemetry). Teams
that use Prometheus and Grafana have no direct way to get those numbers into
Prometheus: the only route is the OpenTelemetry exporter, which needs extra
optional packages and a collector in between.

**Expected Behavior**

A user can attach a `PrometheusExporter` to EcoTrace with a Pushgateway URL.
After each tracked function, it pushes `ecotrace_carbon_emitted_gco2` and
`ecotrace_execution_duration_seconds`, labelled with the function name and run
ID, in the Prometheus text format, using only `requests`.

**Current Behavior**

There is no such exporter. On `main` (`05d9a12`),
`from ecotrace.exporters import PrometheusExporter` raises `ImportError`, and
nothing in the repo writes the Prometheus text format or talks to a Pushgateway.

**Affected Components**

`ecotrace/exporters/` (a new `prometheus.py`, plus the lazy registry in
`exporters/__init__.py`), the exporter calling convention in `ecotrace/core.py`
(`add_exporter()` and the dispatch that calls each exporter's `export()`, which I
read but do not change), and `tests/test_exporters.py`.

**Toolkit**

- Tool: none built for this phase; I read the issue, `core.py`, and the three
  existing exporters myself, with Claude Code helping me find the dispatch code.
- What it produced: the four answers above, and the exporter interface
  (`export(carbon_emitted, func_name, duration, region, run_id=..., run_label=...)`)
  the new class has to match.
- Difficulties: `core.py`'s `add_exporter` docstring lists only four arguments,
  but the dispatch also passes `run_id` and `run_label`, so the docstring was not
  enough; I had to read the dispatch.
- What changed: the exporter accepts both keyword arguments and falls back to the
  instance's `_run_id`, and one test covers each path.

---

## Reproduction Process

**Environment Setup**

Windows 11 Home (10.0.26200) with Git Bash, Python 3.11.9 in a fresh venv, and
ecotrace at `main` `05d9a12` (2026-11-02). I forked `Zwony/ecotrace`, cloned it
(`origin` is my fork, `upstream` is Zwony), and installed it the way
CONTRIBUTING.md says: `python -m venv .venv` then `pip install -e ".[dev]"`, plus
`pytest==8.3.4 requests-mock==1.12.1 pytest-cov==5.0.0` to match
`.github/workflows/ci.yml`. The one challenge was that Avast intercepts TLS on my
machine, so `pip` failed with `CERTIFICATE_VERIFY_FAILED` until I pointed
`PIP_CERT` at Avast's certificate. Nothing in ecotrace changed. Baseline on main:
`pytest tests/` gave 251 passed, 2 skipped, and `flake8 ecotrace/` already
reported 759 warnings (mostly line length), which CI does not run.

**Steps to Reproduce**

1. `ls ecotrace/exporters/` shows `__init__.py`, `_retry_queue.py`, `cloud.py`,
   `otel.py`, `webhook.py`, with no Prometheus module.
2. `.venv/Scripts/python -c "from ecotrace.exporters import PrometheusExporter"`
3. Observed: `ImportError: cannot import name 'PrometheusExporter' from 'ecotrace.exporters'`.
   A repo-wide `grep -rniI "prometheus\|pushgateway"` finds only docs saying the
   OTel exporter can reach Prometheus.

**Reproduction Evidence**

- **Commit showing reproduction:** none. This is a missing feature, so the
  reproduction is an import against unchanged `main` `05d9a12`.
- **Screenshots/logs:**
  ```
  $ .venv/Scripts/python -c "from ecotrace.exporters import PrometheusExporter"
  Traceback (most recent call last):
    File "<string>", line 1, in <module>
  ImportError: cannot import name 'PrometheusExporter' from 'ecotrace.exporters' (C:\Users\fooja\Documents\GitHub\ecotrace\ecotrace\exporters\__init__.py)
  ```
- **My findings:** the gap is real and nothing partial exists to build on, but
  the pattern to follow does: `WebhookExporter` already posts with `requests`,
  attaches with `add_exporter`, and stays inert if `requests` is missing. I did
  not run a real Pushgateway; the issue asks for tests that mock the endpoint.

**Toolkit**

- Tool: repro-check (unit 2), with the wild scope from unit 6, grading my claim
  draft and my repro report together before anything was posted.
- What it produced: `accept` on all eight checks, after it verified every repo
  fact itself (the HEAD commit, the exporter listing, `__all__`, and the CI pins),
  plus two voice-guide notes: "a few days" was softer than my own rule's "a number
  of days", and my claim asserted the `ImportError` without showing it.
- Difficulties: repro-check could not run until the wild scope was in place; with
  the sandbox scope it would have judged ecotrace by Path Review's classroom rules.
- What changed: I changed the claim to "within a week", put the command and its
  output in the claim, and used the explicit `.venv/Scripts/python` path in the
  steps so a stranger does not hit their system Python.

---

## Solution Approach

**Analysis**

EcoTrace sends finished measurements to exporters. `EcoTrace.add_exporter()`
(`ecotrace/core.py:460`) appends an object to `self._exporters`, and after each
measurement `core.py:418-456` calls every exporter's
`export(carbon_emitted, func_name, duration, region, run_id=..., run_label=...)`
on a two-thread pool, falling back to a synchronous call if the pool rejects the
job. `ecotrace/exporters/` has `cloud.py`, `otel.py`, and `webhook.py`, and
`exporters/__init__.py` loads each one lazily through `__getattr__`. There is no
Prometheus exporter, so the gap is a missing module, not a bug in an existing
one: the only route to Prometheus is `OTelExporter`, which needs the optional
OpenTelemetry packages and a collector. `requests` is already a core dependency
in `pyproject.toml`, so a direct push needs nothing new.

**Proposed Solution**

Add `ecotrace/exporters/prometheus.py` with a `PrometheusExporter` modelled on
`WebhookExporter`: constructor `(ecotrace_instance, url, job="ecotrace",
headers=None, timeout=5)` that registers with `add_exporter`; an `export()` that
builds two gauges (`ecotrace_carbon_emitted_gco2` and
`ecotrace_execution_duration_seconds`, labelled `function` and `run_id`) with
`# HELP`/`# TYPE` lines, escaped label values and a trailing newline, and POSTs
them with `Content-Type: text/plain; version=0.0.4` to
`<url>/metrics/job@base64/<job>/function@base64/<function>/run_id@base64/<run_id>`,
so each function and run is its own Pushgateway group (a POST replaces
same-named metrics within a group, and the group comes only from the URL path);
push errors logged at debug and never raised; inert if `requests` is missing.
Register it in `exporters/__init__.py`.

Deliberately not changing: `core.py` and the exporter calling convention, the
other exporters, dependencies (no `prometheus_client`), the README, and the
CHANGELOG (the maintainer writes release notes). The code stays Python 3.8
compatible because CI runs 3.8 to 3.14.

**Implementation Plan**

1. Branch `feat/prometheus-exporter` from `upstream/main`, as the issue asks.
2. Write `ecotrace/exporters/prometheus.py`: the class, a label-escaping helper,
   a body formatter, and `export()`, with docstrings and a usage example.
3. Register `PrometheusExporter` in `ecotrace/exporters/__init__.py`.
4. Add tests to `tests/test_exporters.py`, patching `requests.post` like the
   webhook tests.
5. Run `pytest tests/test_exporters.py`, the full `pytest tests/`, and
   `flake8 ecotrace/exporters/prometheus.py`; re-run the reproduction import.
6. Commit in small `feat:` / `test:` steps, rebase on `upstream/main`, and open
   the PR with `Closes #232`.

Deviations: two.

1. Step 5's lint was going to be CONTRIBUTING.md's whole-package
   `flake8 ecotrace/`, but it fails on `main` with 759 warnings in files this
   issue never touches. I lint only the files I change, and recorded the
   whole-package check as excluded in my ship manifest.
2. My first plan and first build pushed everything to `<url>/metrics/job/<job>`,
   and I wrote that "POST adds to the job's group, so samples coexist." That was
   wrong: the Pushgateway would have kept only the last function pushed. plan-check
   rejected the plan for stating it as settled, a code-review agent found the same
   bug from the Pushgateway docs, and a real Pushgateway in Docker confirmed it.
   I moved `function` and `run_id` into the URL grouping key (commit `703c4d0`).
   The URL in my claim comment is now out of date, so the PR description explains
   the change.

**Toolkit**

- Tool: plan-check (unit 3) with the wild scope, grading `plan.md` and a
  local-only `plan-comment.md`. I ran it twice.
- What it produced: the first run was `reject` on `unknowns-honest`. It quoted my
  sentence "POST adds to the job's group instead of replacing it" and explained
  that the Pushgateway groups by URL path. It also noted I had never pushed to a
  real gateway. The second run, after the fix and the Docker test, was `accept`
  on all eight checks.
- Difficulties: the rejection arrived after I had already built and committed the
  first version, because I ran plan-check late. Its procedure also still mentions
  the sandbox's `Repo:` line and a bug-shaped repro with control runs, neither of
  which fits a feature request in the wild.
- What changed: the design (grouping key), plus 15 tests instead of 8. I also
  changed my order of work: next time plan-check runs before the first commit,
  and anything my tests mock gets one real run before I call it settled.

---

## Testing Strategy

Six observable checks, run before and after on Windows 11 / Python 3.11.9 (the
numbers below are from the final branch, `4c36dde`):

1. **The reproduction import.** Before, on `main` `05d9a12`:
   `ImportError: cannot import name 'PrometheusExporter'`. After, on my branch:
   `<class 'ecotrace.exporters.prometheus.PrometheusExporter'>`.
2. **New tests that fail without the fix.** 15 tests in `tests/test_exporters.py`
   cover:
   - the exact grouped URL, the headers, and the exact body;
   - the full text format, label escaping, and `NaN`/`Inf`;
   - a custom job, the `run_id` keyword, the fallback, and an explicit empty run_id;
   - two functions pushing to two groups;
   - slash, empty and non-ASCII names;
   - a custom timeout, and headers overriding Content-Type;
   - a connection error and an HTTP 400 being swallowed;
   - missing `requests`, and the package import.

   Copied into a worktree of `main`, the file fails at collection with
   `ModuleNotFoundError: No module named 'ecotrace.exporters.prometheus'`. On my
   branch, `pytest tests/test_exporters.py` gives 33 passed.
3. **Planted bugs.** I put 12 bugs into `prometheus.py` one at a time, including
   the original job-only URL, a wrong escaping order, a missing trailing newline,
   standard instead of URL-safe base64, and a hard-coded timeout. I then ran the
   Prometheus tests on each copy. Every bug made at least one test fail; the
   job-only URL made 10 fail. The first version of my tests let four of these
   through, which a test-coverage review found.
4. **The repo's own suite and lint, as the ship-gate runs them** (plain `cmd`,
   no venv activated):
   - `pytest tests/` went from 251 passed, 2 skipped on `main` to 266 passed,
     2 skipped on the branch.
   - `flake8` on both changed exporter files prints nothing.
   - The whole-package `flake8 ecotrace/` is excluded, because it already fails on
     main with 759 warnings in other files, and CI does not run it.
5. **A real Pushgateway** (`prom/pushgateway:v1.11.1` in Docker):
   - With the old job-only URL, pushing `train` then `evaluate` left only
     `evaluate` in the gateway's `/metrics`.
   - With the fix, EcoTrace tracking `train()` then `evaluate()` left both.
   - `models/train.step` and an empty name were accepted (HTTP 200).
   - A `DELETE` on one group's URL removed only that group (HTTP 202).
6. **The voice guard on the PR description.** It stopped three drafts before
   passing: one for the emoji in the repo's PR-template checkboxes, and two for
   results I described without pasting their output. Every result claimed in the
   PR now has its output next to it.

Not covered: Python 3.8 to 3.14. CI's matrix checks those, and the code avoids
syntax newer than 3.8.

**Toolkit**

- Tool: the ship-gate's manifest commands (run by hand before the gate), a
  throwaway `git worktree` of `main`, a test-coverage review agent that planted
  bugs, and a Pushgateway container.
- What it produced: the before/after numbers above, saved in
  `test_evidence.md` in my clone (kept out of the PR by `.git/info/exclude`).
- Difficulties: the gate runs checks in a fresh Windows `cmd` shell without my
  venv, so the commands had to be written as `.venv\Scripts\python.exe -m ...`,
  and I had to confirm the suite also passes there without my Avast certificate
  variables (it does: 251 passed in 128 s).
- What changed: I narrowed the lint row to the files I change, and moved the
  whole-package lint to the manifest's excluded table with the date and reason,
  instead of letting a pre-existing failure block the PR. The bigger change: my
  mocked tests had passed a design that was broken in a real Pushgateway, so I
  added the Docker check and the planted-bug run, and seven more tests.

---

## Implementation Notes

**2026-11-04, afternoon: choosing and claiming**

Narrowed the Issue Selection site's 2,407 issues to two picks by checking claims
and open PRs on GitHub, then reading threads by hand after finding six "free"
issues that had claim comments. Chose ecotrace #232. Cloned, installed, ran the
baseline (251 passed), and reproduced the missing exporter. The voice guard
stopped my first claim draft for asserting the `ImportError` without showing it;
I added the command and output, and the claim went up through the hook at 16:21.

**2026-11-04, evening: building**

Created `feat/prometheus-exporter` from `upstream/main`. Wrote
`ecotrace/exporters/prometheus.py` by following `webhook.py`: same `requests`
import guard, same `add_exporter` registration, errors logged at debug level.
Decisions made while building: gauges rather than counters, because each sample
is one function's footprint for one run, not a running total; POST rather than
PUT, so pushes from different runs and functions add to the job's group instead
of replacing it (I asked the maintainer about this in the claim and kept that as
my stated default; this belief turned out wrong, see the night entry); `job="ecotrace"` by default and URL-quoted; label values
escaped, because a function name with a quote would otherwise break the body; a
module-level `format_metrics()` so the text format can be tested without HTTP.
Kept every line under 79 characters because CONTRIBUTING.md names `flake8`.
Committed the module (`a335e4c`) and the tests (`a9e73c7`) separately.

**2026-11-04, evening: one wrong turn**

My first draft of the lint check was the whole-package `flake8 ecotrace/` from
CONTRIBUTING.md, which fails on `main` with 759 warnings in files I never touch.
Rather than "fix" unrelated files (out of scope for this issue), I linted only the
new module and recorded the whole-package check as excluded, with the reason.

**2026-11-04, night: the review found a real bug**

Before opening the PR I had plan-check grade the plan, and two separate review
agents look at the diff: one for code, one for test coverage. plan-check rejected
my sentence "POST adds to the job's group, so samples from different runs and
functions coexist" as an unmeasured claim. The code reviewer, given only the
code, fetched the Pushgateway README and found the same thing: the group is set
by the URL path, and a POST replaces same-named metrics in that group, so every
push would wipe the previous function's numbers. My 8 tests had all passed
because they mock `requests.post`.

I asked for and got permission to pull `prom/pushgateway:v1.11.1` and ran it in
Docker. The old URL kept only `evaluate` after `train` then `evaluate`; the new
URL kept both. The fix (`703c4d0`) puts `function` and `run_id` in the URL as
`@base64` grouping labels. It also spells `NaN`/`Inf` the Prometheus way,
documents that groups persist until deleted, and wraps a long `__all__` line.

The test reviewer then planted bugs one at a time and found four that my tests
missed: an empty `run_id` being replaced, a hard-coded timeout, headers not
overriding Content-Type, and non-UTF-8 or non-URL-safe encoding. I added tests
for those (`4c36dde`) and re-ran 12 planted bugs against the final branch; every
one now fails at least one test. plan-check accepted the corrected plan. The
voice guard then stopped my PR description three times before it passed (emoji
from the repo's template, and two results I described without pasting the
output).

**Toolkit**

- Tool: Claude Code as a pair, plus the course's ship-gate manifest; I read and
  approved each change before it was committed under my name.
- What it produced: four commits on `feat/prometheus-exporter`:
  - `feat: add Prometheus Pushgateway exporter`
  - `test: cover PrometheusExporter push, format, escaping and errors`
  - `fix(exporters): give each function and run its own Pushgateway group`
  - `test(exporters): pin run_id, timeout, header and UTF-8 handling`
- Difficulties: `core.py`'s `add_exporter` docstring under-describes the
  interface (it omits `run_id` and `run_label`), and Windows line-ending warnings
  appeared on commit (`LF will be replaced by CRLF`), which are local only.
- What changed: the exporter accepts the full keyword set the dispatch really
  passes, with a test for each run_id path.

---

## Pull Request

**PR Link:** https://github.com/Zwony/ecotrace/pull/250

**PR Description:** "feat(exporters): add Prometheus Pushgateway exporter",
opened 2026-11-04 from `foojanbabaeeian:feat/prometheus-exporter` into `main`,
with 4 commits. It follows ecotrace's PR template item by item:

- **Summary** with `Closes #232`.
- **Type of change:** new feature and tests.
- **What changed,** file by file: the exporter, its registration, and 15 tests.
- **Why the URL differs from my claim comment:** function and run_id are in the
  Pushgateway grouping key, so pushes can't overwrite each other.
- **What I did not change.**
- **Two choices for the maintainer:** POST and the `ecotrace` job name, and one
  group per function per run, which accumulate until deleted.
- **How to test,** with a Docker Pushgateway example.
- **The checklist,** with the README box left unticked and the reason given.
- **Output:**
  - the `ImportError` before;
  - the real Pushgateway's `/metrics` with the old URL (one function kept) and
    the new one (both kept);
  - the awkward-name and DELETE results;
  - the test counts (33 exporter tests; 266 passed overall versus 251 on main);
  - a collapsed planted-bug table.
- **AI assistance,** in my own words.

**Maintainer Feedback:**

- 2026-11-04: none yet. The only comment is the SonarCloud bot's
  "Quality Gate passed" (0 new issues). CI has not run, because GitHub holds
  workflows from a first-time contributor's fork until a maintainer approves
  them.
- 2026-11-04: ran the PR monitor with my playbook. It drafted "wait, nothing
  to send", and my first possible nudge is 2026-11-09 under my 4-day threshold.
  Nothing posted.

**Status:** Awaiting review

**Toolkit**

- Tool: the ship-gate (unit 7) with my `ship-manifest.md`, plus my pr-precheck
  (unit 4) inside it and my voice guard (unit 6) on the same `gh pr create`.
  I ran `check_gate.py --branch` by hand, then opened the PR through Claude Code.
- What it produced: `ship-gate: PASS`:
  - the repo tests passed in 136 s and the lint passed;
  - pr-precheck accepted all eight checks (diff matches plan, description
    matches diff, repro re-run shown, repo checks run, no debris, no unrelated
    hunks, template honored, disclosure per policy);
  - then the hook let `gh pr create` through, and the voice guard logged a pass
    at 18:02.
- Difficulties: before the gate even ran, the voice guard stopped the
  description three times: once for emoji from ecotrace's own PR template, and
  twice for results I described without pasting their output. The voice check
  also timed out once when another Claude session was running at the same time.
  pr-precheck flagged nothing.
- What changed: the description now pastes the output for every claim (in a
  collapsed block for the long table), and the template's checkboxes keep their
  words without the emoji.

---

## Learnings and Reflections

**Technical Skills Gained**

- **The Prometheus text exposition format:** `# HELP` and `# TYPE` lines,
  label-value escaping (the backslash first, then quotes and newlines), the
  `NaN`/`+Inf`/`-Inf` spellings, and the trailing newline.
- **How a Prometheus Pushgateway groups metrics:** the group is the URL path
  (job plus grouping labels, with `@base64` for awkward values), and a POST
  replaces same-named metrics within its group, so labels in the body cannot
  keep two pushes apart.
- **Fitting into an existing plugin interface:** reading `core.py`'s exporter
  dispatch instead of trusting its docstring, and matching `WebhookExporter`'s
  conventions (guarded import, inert without `requests`, errors logged, never
  raised).
- **Testing with `unittest.mock.patch` and `caplog`**, and checking the tests
  themselves by planting bugs one at a time to see which ones they miss.
- **Git across a fork:** `origin` and `upstream`, branching from `upstream/main`,
  a throwaway `git worktree` to run new tests against unchanged `main`, and
  keeping working files out of a PR with `.git/info/exclude`.
- **Running a real dependency in Docker** (`prom/pushgateway`) to check
  behaviour that mocks cannot show.

**Challenges Overcome**

- **A design bug my own tests could not see.** My first build pushed every
  measurement to one job-only URL, and all 8 mocked tests passed. plan-check
  rejected the plan, and an independent code review found from the Pushgateway
  docs that each push would wipe the previous function's data. A real
  Pushgateway in Docker confirmed it, and I fixed it with a grouping key
  (`703c4d0`; see Implementation Notes, "night").
- **Weak spots in my tests.** A test-coverage review planted bugs and found four
  that slipped through. I added a test for each, and all 12 planted bugs now
  fail at least one test.
- **Windows friction:**
  - Avast intercepting TLS for `pip`;
  - the voice guard crashing on an emoji until `PYTHONUTF8=1` was set;
  - the ship-gate running checks in a fresh `cmd` without my venv, so every
    manifest command names `.venv\Scripts\python.exe`.
- **Claimed issues that looked free.** Six "free" issues on the course site had
  claim comments that the assignee and linked-PR fields don't show, so I read
  every thread myself before choosing.
- **My own voice rules stopping my posts.** The guard stopped my claim once and
  my PR description three times, each time for something I had asserted without
  showing it, or for emoji. Each time the fix was to paste the output.

**What I'd Do Differently Next Time**

- **Run plan-check before writing code, not after.** The plan-check rejection
  arrived after I had committed the first version; earlier, it would have saved
  a fix commit.
- **Test anything I mock against the real thing once.** When the network or a
  service is mocked, one run against the real service (here a Docker
  Pushgateway) catches what the mocks hide.
- **Get the independent review in before the first commit, not the last.**
- **Read the protocol docs for the system I'm pushing to.** Here, that was the
  Pushgateway README, before choosing a URL.
- **Scope the ranking before the search.** My scout's scan ranked by recency and
  comment count, and surfaced claimed and off-profile issues. I would give it a
  fit step and a per-query quota before spending another scan.

---

## Resources Used

- ecotrace `CONTRIBUTING.md` and `.github/workflows/ci.yml`: the setup, test and
  lint commands, the Python 3.8 to 3.14 range, and the commit style I followed.
  https://github.com/Zwony/ecotrace/blob/main/CONTRIBUTING.md
- `ecotrace/exporters/webhook.py`, the exporter pattern the issue pointed to and
  that my exporter copies.
- Issue #232, the owner's spec (file path, metric names, `requests` only,
  mocked tests): https://github.com/Zwony/ecotrace/issues/232
- The Prometheus Pushgateway README, which the code review used to find the
  grouping bug and which documents the POST semantics and `@base64` grouping
  labels: https://github.com/prometheus/pushgateway
- The `prom/pushgateway` Docker image (v1.11.1), which I used to confirm the bug
  and the fix: https://hub.docker.com/r/prom/pushgateway
- The `gh search issues` manual, for my scout's queries:
  https://cli.github.com/manual/gh_search_issues
- The AI301 unit 5 to 8 starter repos (issue-scout, voice guard, ship-gate, PR
  monitor) and the AI301 Issue Selection site, where I found the issue.
