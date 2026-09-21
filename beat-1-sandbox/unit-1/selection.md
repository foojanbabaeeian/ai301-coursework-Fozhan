# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/72

**Verdict output**

````
All three issues are accepted. I ranked them by your fit profile: **#72 first, #73 second, #68 third**.

**Repo-wide checks (same result for all three)**
- **repo-alive:** passes. `archived: false`, and the last push was 2026-09-16, 4 days before today.
- **maintainer-active (preferred):** passes. The last 5 commits on `main` are all by `Aburke225`, the most recent on 2026-09-16.
- **ai-policy-ok:** passes. `docs/CONTRIBUTING.md` and the PR template say nothing about AI use, and there's no `AGENTS.md` or AI policy file. The template only requires green CI, tests, and removing the seeded bug's xfail marker.
- **Linked PRs:** the repo has no PRs at all, so none of these issues has a linked PR.

**Ranked read-out**

1. **#72, `verify_password` raises `UnknownHashError` on malformed hashes.** It is a Python bug with a one-line expected behavior: return `False`. It's filed by a maintainer with no assignee and no comments. It also gets you the project's review workflow: dropping the strict xfail marker on the covering test, plus `make check && make test-unit`. I put it first because it matches Python, tests, and learning an unfamiliar codebase, and nobody else is on it.
2. **#73, README and `.env.example` disagree about the LLM API key.** It is docs-only (2 files) with no assignee and no comments, so it's the easiest and cleanest of the three. It suits your interest in clear docs, but it involves no test-writing, so it ranks below #72.
3. **#68, keyword search raises `ZeroDivisionError` on an empty index.** It passes every check, but it ranks last because another student, `yulijasso`, posted a claim on 2026-09-20 with a reproduction and a plan. Under the Path Review house rule that claim doesn't block it, and I graded it as unclaimed. The maintainer estimates 2–4 hours, and you'd likely end up duplicating a PR that is already being planned.

The scope check passes for all three: each asks for one named change and is filed by a collaborator with the bug label. The unclaimed check passes for all three under the house rule.

```json
[
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/72",
    "checks": [
      {"name": "repo-alive", "grade": "pass", "evidence": "archived: false; pushed_at 2026-09-16T21:48:27Z (4 days before 2026-09-20)"},
      {"name": "maintainer-active", "grade": "pass", "evidence": "Last 5 main commits all by Aburke225 (human), newest 2026-09-16"},
      {"name": "scope-bounded", "grade": "pass", "evidence": "Collaborator-filed bug: verify_password should return False on malformed hash instead of raising UnknownHashError; files named"},
      {"name": "unclaimed", "grade": "pass", "evidence": "assignees: none; no linked PRs (repo has zero PRs); 0 comments"},
      {"name": "ai-policy-ok", "grade": "pass", "evidence": "docs/CONTRIBUTING.md and PR template contain no AI statement; no AGENTS.md or AI policy file"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/73",
    "checks": [
      {"name": "repo-alive", "grade": "pass", "evidence": "archived: false; pushed_at 2026-09-16T21:48:27Z"},
      {"name": "maintainer-active", "grade": "pass", "evidence": "Last 5 main commits all by Aburke225 (human), newest 2026-09-16"},
      {"name": "scope-bounded", "grade": "pass", "evidence": "Collaborator-filed: make README.md and .env.example agree on OPENROUTER_API_KEY / LLM_PROVIDER options; two named files"},
      {"name": "unclaimed", "grade": "pass", "evidence": "assignees: none; no linked PRs; 0 comments"},
      {"name": "ai-policy-ok", "grade": "pass", "evidence": "docs/CONTRIBUTING.md and PR template contain no AI statement; no AGENTS.md or AI policy file"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/68",
    "checks": [
      {"name": "repo-alive", "grade": "pass", "evidence": "archived: false; pushed_at 2026-09-16T21:48:27Z"},
      {"name": "maintainer-active", "grade": "pass", "evidence": "Last 5 main commits all by Aburke225 (human), newest 2026-09-16"},
      {"name": "scope-bounded", "grade": "pass", "evidence": "Collaborator-filed bug: KeywordSearcher.index([]) should not raise ZeroDivisionError (search() already returns []); files named"},
      {"name": "unclaimed", "grade": "pass", "evidence": "assignees: none; no linked PRs; yulijasso claim comments dated 2026-09-20 (NONE association) are ignored under the Path Review house rule"},
      {"name": "ai-policy-ok", "grade": "pass", "evidence": "docs/CONTRIBUTING.md and PR template contain no AI statement; no AGENTS.md or AI policy file"}
    ],
    "verdict": "accept"
  }
]
```
````

---

## Eval iterations

**Run history**

(An initial `--limit 3` smoke run to check the wiring came back 2/3; partial runs are never scored, so the list below is the full and `--only` runs.)

1. Full run: 17/20 (missed issue-04, issue-19, issue-20 — all three on the scope-bounded check)
2. `--only issue-04,issue-19,issue-20,issue-01,issue-05` after tightening scope-bounded: 5/5 (issue-01 and issue-05 were included as canaries, to confirm the reworded sub-task clause neither re-broke the borderline accept nor flipped the umbrella reject)
3. Full confirming run with `--save-run`: 20/20 — matches the agreement line in `eval-run.txt`

**Issue analysis**

issue-20. My rubric decided reject; the gold label is reject — but my first full run graded it accept, so this is the issue that moved my rubric the most. The check that decided it was scope-bounded, graded fail on this evidence: "Logo asset TBD." On the first run the check passed because the issue looks well specified: it is a bot-filed feature request with a tidy template, a stated solution, and even a success criterion. My fail conditions at that point only caught badly written requests (one-line wishes, self-described umbrella lists), so a polished template sailed through even though its deliverable depends on a product decision nobody has made — whose company logo, exactly? The asset is literally "TBD" and no maintainer has endorsed the idea. The threshold in the wrong place was "one-line wish with no description of expected behavior": it tested the polish of the write-up instead of whether the work is actually decided. I added a fail condition for a feature request whose deliverable depends on an asset, input, or product decision the issue leaves undefined, and it now rejects for the right reason.>

**Check rationale**

Quoted from `tools/issue-select/rubric.md` as currently written:

> | unclaimed | Repo facts: `this issue: assignees:` and `linked PRs:` with their states; the Comments section (claim comments and their dates) | Pass when assignees is `none`, no linked PR is `open`, and the newest claim comment ("I'll take this", "working on this", "/assign", "opened PR #N") is either absent, older than 90 days with no open PR, or was answered by a maintainer saying the issue is free. Fail when an assignee is set, or any linked PR is open (a bot nudge does not clear an assignee or an open PR), or a claim comment is 90 days old or newer with no maintainer release. Closed-unmerged PRs are abandoned attempts and do not block. | required |

Why it is written this way: A good-first-issue label only tells me the maintainers think the issue is friendly; it says nothing about whether someone already has it, so this check ignores labels and reads the three places a claim actually shows up: the assignee box, the linked-PR states, and the comment thread. An assignee or an open PR blocks outright because a real person is actively ahead of me, and a bot nudge does not undo either signal. Claim comments get a 90-day threshold because both the calibration set and the eval set contain old claims that went nowhere — issue-09 carries a 2022 "I'll take this" on a conda issue where a maintainer later invited new takers — and a comment everyone has forgotten should not sink a good issue. Closed-unmerged PRs do not block: an abandoned attempt says something about the issue's real difficulty, which is scope-bounded's job to read, not evidence that anyone still holds a claim.

**Trade-offs**

(a) An issue whose result this check changes: issue-09 passes only because its claim comment is from 2022 — far older than 90 days — with no open PR and a maintainer explicitly inviting new takers. A stricter version, "any claim comment blocks", would reject it and cost a clear-accept; both of my full runs (17/20 and 20/20) agreed with gold on issue-09, so the threshold is earning its keep. The price is the mirror case: a contributor who claimed 100 days ago and is still quietly working without an open PR gets graded free. I accept that miss because a claim with no PR after three months is more often abandoned than active, and where it matters this unit — the Path Review classroom repo — the house rule makes shared work harmless anyway.

---

## Selection rationale

**Selection rationale**

1. Fit: #72 is a small Python bug in the auth layer — `verify_password` raises passlib's `UnknownHashError` on a malformed stored hash instead of returning False. That is squarely inside what I already do: I built a FastAPI auth flow (magic-link sign-in, JWT sessions) for my own project and I write pytest suites, so a defensive fix around a hash check plus its covering test is something I can land in the few hours I have this week alongside classes.

2. What the verdict got right: every check passed on named evidence — the repo pushed 4 days before the run, the last 5 commits are all by a human maintainer, the issue has no assignee, zero comments, no linked PRs (the repo has none at all), and CONTRIBUTING plus the PR template say nothing restricting AI-assisted work. What I weighed that the rubric could not see: the bug sits in the part of a codebase I find most interesting (auth), I can run the project locally with its own `make check` / `make test-unit` workflow, and the change is contained enough that I only need to read the security module and its tests, not the whole codebase. The skill also ranked it above #73 (docs-only, no test-writing practice) and #68 (a classmate already posted a reproduction and plan), which matches how I would have chosen by hand.

3. Difficulty in claiming: Path Review is a classroom repo, so other students may claim #72 as well, and the house rule says that is fine — credit attaches to the PR I open, not to being first. At grading time #72 had no claim comments at all, so I may even be first to it; if claims appear before Unit 2, I will claim anyway and work it.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
