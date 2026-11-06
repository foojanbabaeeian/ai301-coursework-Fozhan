# Contribution: feat: add an upstream facet to dashboard request logs

**Contribution Number:** 2
**Student:** Fozhan Babaeiyan Ghamsari
**Issue:** https://github.com/Soju06/codex-lb/issues/1870
**Pull Request:** https://github.com/Soju06/codex-lb/pull/2708

This is my second, ungraded contribution, recorded with the course's
extra-round template. `contribution-story.md` (ecotrace #232, PR #250) is the
graded one. I did this one in parallel to build my portfolio. It ran in a
separate Claude Code session that stopped for my yes before every public action.

---

## Toolkit

A Toolkit block looks like this (copy it into each story section and fill it
there):

```
**Toolkit**
- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

### Unit 5: The scout graded this issue as my second pick

codex-lb #1870 was the second of two picks I took from the course's Issue
Selection site. My scout graded it `accept` on all checks. After the unit 6 scope
patch it rated the issue **ambitious**, because the owner's first-PR boundary
covers a backend API, a React multi-select, every locale, an OpenSpec change and
SQLite plus PostgreSQL tests. I picked the smaller ecotrace issue as my graded
contribution and kept this one as a stretch.

### Unit 6: The voice guard checked my claim before it went up

The claim comment went through the voice guard's manual check and then the hook
(both passed, 2026-11-04 17:07 and 17:10). It names this issue's code paths, my
first step, a 7-day update window, and how I would let go. It asks one question
and states my default.

### Unit 7: The ship-gate passed the PR, with a manifest written for Windows and WSL

The ship-gate's manifest ran the request-log backend tests (109 s), the dashboard
tests (191 s), and `openspec validate --specs`. It passed the branch at
`dcc74cc8` on 2026-11-05 10:39 and let `gh pr create` through. What fought me:
codex-lb's pre-commit hook calls `.venv/bin/python` and `ty`, which cannot run on
Windows. I approved committing with `--no-verify` and running the same checks in
WSL instead, and the PR description says so, with the output.

### Unit 8: CodeRabbit is this repo's reviewer, so its findings drive the follow-ups

codex-lb makes CodeRabbit review a merge gate: every actionable finding must be
fixed or explicitly answered. The follow-up work so far has come from its two
reviews, not from a nudge. No maintainer has commented yet.

---

## Why I Chose This Issue

I found it on the AI301 Issue Selection site while choosing my graded issue, and
my scout accepted it. The owner, Soju06, had already checked the gap against
`main` and written a first-PR boundary on 2026-09-28, which made the scope
unusually clear for a real project. It was also a much bigger and more full-stack
job than my profile usually aims for. I wanted to see whether I could work inside
a large codebase with a strict process (OpenSpec changes, CodeRabbit, simplicity
budgets) without losing control of the scope. I also wanted a second PR for my
portfolio.

The scout's verdict held up: the issue was unclaimed and the policy allowed AI
help. Its "ambitious" note was right too. The PR touches 27 files across
backend, frontend, locales and specs. The scout cannot tell you how much process
a repo puts around a PR, and here that process was most of the work.

Claim comment: https://github.com/Soju06/codex-lb/issues/1870#issuecomment-6646804388

**Toolkit**
- Tool: issue-scout, grading the course-site pick directly (and re-grading it
  after the unit 6 scope patch).
- What it produced: `accept`, difficulty "ambitious", with the edge to raise
  early: whether the count-cache-key change and the single-option facet hiding
  belong in this first PR.
- Difficulties: the old scope check passed a multi-day, multi-stack feature
  because the issue called its parts "one focused PR".
- What changed: the difficulty note made the size visible, and I asked the edge
  question in my claim.

---

## Understanding the Issue

### Problem Description

codex-lb's dashboard Request Logs view cannot filter or identify traffic by its
upstream. Request logs already store `modelSourceId` and `modelSourceKind` for
requests served by configured model sources, but the API takes no source filter
and the dashboard never shows them.

### Expected Behavior

The Request Logs view has an **Upstream** multi-select filter, built from the
upstreams actually seen in the logs. That includes deleted sources, which get a
fallback label. Model-source rows show which source served them.

### Current Behavior

On `main` (`72630bbf`), `GET /api/request-logs?upstream=src_a` ignores the
parameter and returns both a pool row and a source row. `GET
/api/request-logs/options` has no upstream facet. Every model-source row shows
"Unassigned" in the Account column.

### Affected Components

- **Backend:** `app/modules/request_logs/api.py`, `service.py` and
  `repository.py` (`_build_filters`, the 30-second count cache, and the
  demand-rollup count path).
- **Dashboard:** under `frontend/src/features/dashboard/`, the schemas, the API
  client, the URL-state hook, the filter bar, the page, and the request table.
- **Other:** the three locale files, and a new OpenSpec change.

**Toolkit**
- Tool: Claude Code reading the request-log path from API to repository to
  dashboard, with me checking each conclusion.
- What it produced: the analysis in `plan.md`. A key point was that the demand
  rollup has no `model_source_id` column, so a filtered count cannot use it.
- Difficulties: the count cache and the rollup path were not obvious from the
  API layer.
- What changed: the plan sends upstream-filtered counts down the raw count path
  and adds the selection to the cache key.

---

## Reproduction Process

### Environment Setup

Windows 11 with Python 3.13 via `uv` for the app, bun for the frontend, and WSL
for the parts of the suite that only run on POSIX. I forked `Soju06/codex-lb`
and cloned it, with `origin` as my fork and `upstream` as Soju06. Two problems
came up:

- On Windows, `make test-unit` stops at collection, because two test files
  import `uvloop` and `fcntl`, which exist only on POSIX.
- The pre-commit hook needs `.venv/bin/python`.

I ran the baseline and the hook's checks in WSL instead. The baseline on `main`
in WSL was `10473 passed, 107 skipped, 1 xfailed`.

### Steps to Reproduce

1. On `main` `72630bbf`, start the API with a SQLite test database holding one
   pool request and one request served by model source `src_a`.
2. `GET /api/request-logs?upstream=src_a`
3. Observed: `total: 2`, rows `[('req_src_a', 'src_a'), ('req_pool', None)]`.
   The filter is ignored. Also, `GET /api/request-logs/options` returns only
   `['accountIds', 'apiKeys', 'modelOptions', 'statuses']`.

### Reproduction Evidence

- **Commit showing reproduction:** none. The reproduction is a throwaway test
  run against unchanged `main`; its log is kept locally.
- **Screenshots/logs:**
  ```
  GET /api/request-logs?upstream=src_a -> 200
    total: 2
    rows: [('req_src_a', 'src_a'), ('req_pool', None)]
  GET /api/request-logs/options -> 200
    keys: ['accountIds', 'apiKeys', 'modelOptions', 'statuses']
  FAILED ... assert 2 == 1
  ```
  The before screenshot shows model-source rows reading "Unassigned" and no
  Upstream filter.
- **My findings:** on the frontend, `modelSourceId` is parsed in `schemas.ts`
  and never rendered.

**Toolkit**
- Tool: a throwaway integration test, plus a frontend grep.
- What it produced: the failing assertion above.
- Difficulties: Windows could not run the full baseline suite.
- What changed: I used WSL for the baseline and the hook checks.

---

## Solution Approach

### Analysis

The request-log API has no source parameter, and `_build_filters` has no
`model_source_id` condition. The count cache key leaves out the source, so two
upstream selections would share one cached total for 30 seconds. The demand
rollup that serves counts has no source column. On the dashboard, the options
schema, URL state and filter bar have no upstream field, and the table shows
"Unassigned" for every model-source row.

### Proposed Solution

**Backend:**
- An additive `upstream` query parameter (`subscription_pool` or a model-source
  ID).
- An upstream facet in the options endpoint that ignores its own selection,
  following the existing API-key facet.
- Upstream-filtered counts take the raw path, and the selection joins the cache
  key.

**Dashboard:**
- An Upstream multi-select filter.
- A model-source badge on rows, showing the source's name and a label for
  deleted sources.
- Strings in all three locales.

**OpenSpec:** a change, `add-request-log-upstream-facet`.

**Not changed:** no migration (the column and index already exist), no new
setting, and no change to routing or to reports.

### Implementation Plan

1. `docs(openspec)`: the change proposal, tasks and spec deltas.
2. `feat(request-logs)`: filter request logs by upstream.
3. `feat(request-logs)`: the upstream options facet.
4. `feat(dashboard)`: the upstream filter.
5. `feat(dashboard)`: the model source shown on rows.
6. Rebase, run the full checks, and capture screenshots.

Deviations:

- One commit beyond the plan: `fa17dca5`, which labels the badges with the
  source's name. The screenshots showed every badge reading "Model source", so
  two sources still looked identical. This sits inside the owner's "row/detail
  attribution" boundary, and the spec scenario was updated to match.
- Two fixes after review: `0acb7a40` and `92d80e3b` (see Pull Request).

**Toolkit**
- Tool: `plan.md`, written before any code, plus the OpenSpec change as the
  repo requires.
- What it produced: a five-commit plan that the history follows.
- Difficulties: the plan grew to about 1,200 lines in total. That is over the
  repo's ~800-line guideline, but CONTRIBUTING allows "a single 1500-line change
  scoped to one capability".
- What changed: commit `fa17dca5`, after the screenshots showed the badge text
  could not tell two sources apart.

---

## Testing Strategy

On the branch, before review fixes (`dcc74cc8`):

| Check | Result |
|---|---|
| `ruff format --check`, `ruff check`, `ty check` (WSL) | All pass |
| New and existing request-log tests | 76 passed |
| Full unit slice (WSL) | 10480 passed, 107 skipped, 1 xfailed; 1 failed |
| Frontend lint, typecheck and build | Clean |
| Dashboard tests | 41 files, 511 tests passed |
| `openspec validate --specs` | 67 passed |
| Repo's architecture and simplicity-budget scripts | Pass |

**The new tests fail on main:** run against `main` in WSL, 15 of them fail
because the filter and facet are missing.

**Known failures, outside this change:**
- The one failing unit test is in `tests/unit/test_db_sqlite_maintenance.py`.
  The branch doesn't touch `app/db`, and repeated runs failed on `main` too (main
  4 passed and 1 failed; branch 3 passed and 2 failed).
- The full frontend coverage run had 2 timeouts in
  `src/__integration__/api-keys-flow.test.tsx`. The same test also timed out on
  `main` in isolated runs.

**Screenshots:** before and after images are committed in the OpenSpec change's
`evidence/` folder, following the repo's own convention.

**Toolkit**
- Tool: WSL and Windows runs, with logs kept in `.evidence/` (not committed).
- What it produced: the numbers above, plus four before/after screenshots.
- Difficulties: the slow full suite (about 24 minutes in WSL), and two flaky or
  pre-existing failures that I had to show were not caused by this change.
- What changed: I ran each pre-existing failure on `main` too, and recorded both
  results.

---

## Implementation Notes

### 2026-11-04

Claimed the issue, reproduced the gap on `main`, and wrote `plan.md` and the
OpenSpec change. Built the backend filter and facet, then the dashboard filter
and row badge. `ty` could not run on Windows, so I committed with `--no-verify`
and ran the hook's checks in WSL. After the screenshots, I added `fa17dca5` so
the badges show the source's name.

### 2026-11-05

The ship-gate passed, and I opened PR #2708. My voice guard stopped the PR
body's AI line, because it said I ran commands that Claude Code ran. I revised
my rule to require saying plainly what the tool did. CodeRabbit's first review
asked for upstream selections to appear in the conversation summary; I fixed
that in `0acb7a40`. Its second review found that a source name containing markup
crashes the summary, because `Trans` parses the name. I fixed that in
`92d80e3b`, with a test that fails without the fix. I approved pushing that fix
on 2026-11-06.

**Toolkit**
- Tool: Claude Code in a separate session, plus the course tools (voice guard,
  ship-gate). Every public step waited for my yes.
- What it produced: 9 commits under my name, with no Claude trailer.
- Difficulties: the Windows and WSL split, and the length of the full suite.
- What changed: I ran the WSL steps as scripts, so each check could be
  re-run and logged.

---

## Pull Request

**PR Link:** https://github.com/Soju06/codex-lb/pull/2708

**PR Description:** "feat(request-logs): add upstream facet to dashboard
request logs", opened 2026-11-05 against `main`. It follows codex-lb's PR
template, and includes:
- the OpenSpec change;
- what changed in the backend and the dashboard;
- the default I used on the unanswered question from my claim (both the
  cache-key change and the facet hiding stay in this PR, and either can be split
  out on request);
- the commit that goes past the plan;
- why the commits used `--no-verify`, with the WSL check output;
- before and after screenshots;
- an AI-use line saying what Claude Code did and what I did.

**Maintainer Feedback:**

- 2026-11-05: CodeRabbit (the repo's required reviewer) asked to show upstream
  selections in the conversation summary. Fixed in `0acb7a40`.
- 2026-11-06: CodeRabbit found that a source name containing markup crashes the
  summary. Fixed in `92d80e3b`, with a test; I approved the push on 2026-11-06.
- 2026-11-06: CodeRabbit also listed two "failed pre-merge checks": apply the
  status filter to the upstream options facet, with cross-facet test coverage;
  and docstring coverage. Not yet addressed.
- No human maintainer has commented yet. The repo's CI is waiting for a
  maintainer to approve workflows from my fork.

**Status:** Iterating (open, CodeRabbit round in progress)

**Toolkit**
- Tool: the ship-gate (PASS at `dcc74cc8`), the voice guard on the body, and
  CodeRabbit's reviews.
- What it produced: an open PR with two review fixes so far.
- Difficulties: CodeRabbit's pre-merge checks raised scope questions (the status
  filter on the facet, and docstring coverage) that go beyond the owner's
  boundary.
- What changed: two fix commits. The remaining items are next.

---

## Learnings and Reflections

### Technical Skills Gained

- **Facet filters that ignore their own selection,** and why a count cache key
  must include every filter.
- **Why a pre-aggregated rollup can't answer a query** on a column it doesn't
  store.
- **OpenSpec-first development:** writing the change and its scenarios before
  the code.
- **i18n with `react-i18next`,** including how `Trans` can be broken by markup in
  interpolated values.
- **Running a POSIX-only test suite from Windows,** using WSL.

### Challenges Overcome

- Windows could not run the repo's hooks or full suite, so I moved those runs to
  WSL and said so in the PR.
- Two failures turned up that were not caused by this change. I ran both on
  `main` too, rather than ignoring them.
- My own voice rule blocked an honest AI-use line, so I rewrote the rule rather
  than the facts.

### What I'd Do Differently Next Time

- **Check the repo's automated reviewer config before writing code.**
  CodeRabbit's pre-merge checks (docstring coverage, linked-issue checks) are
  predictable from `.coderabbit.yaml`.
- **Size a stretch issue honestly up front.** The scout called this "ambitious",
  and it took more than a day of focused work plus a review round.

---

## Resources Used

- Issue #1870 and the owner's 2026-09-28 scoping comment:
  https://github.com/Soju06/codex-lb/issues/1870
- codex-lb `.github/CONTRIBUTING.md`, `AGENTS.md` and the PR template: the
  OpenSpec, CodeRabbit, PR size and commit rules.
- The CodeRabbit reviews on PR #2708, including the `react-i18next` `Trans`
  source it cited for the markup crash.
- An earlier OpenSpec change in the repo, `extend-account-list-sorting`, used as
  the model for keeping screenshots in `evidence/`.
