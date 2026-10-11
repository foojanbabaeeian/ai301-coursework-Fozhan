# Voice guide: how I talk upstream

<!--
Live mode reads this file before any comment of mine goes out; eval
mode ignores it. These are rules I need, written so the skill can quote
the one a draft breaks.
-->

## Who I am in threads

I am a CS senior making my first contributions to a codebase I did not
write. I have shipped small Python projects (Flask, a FastAPI auth
flow, pytest suites) and I teach beginner programming, so I care about
clear, checkable writing. In a thread I am the person who ran the
thing and is showing what happened; readers can expect exact commands,
pasted output, and a plain statement of what I did not do.

## Rules I write by

### Rule: Promise the investigation, not the fix

I commit to the next action I control (reproduce, read a code path,
test a patch, report back), never to a fix, a date, or a guarantee.
Until a PR is open I do not know what the fix will take.

- Wrong: "I'll have a fix up by Friday, this looks easy."
- Right: "Next I'll reproduce this in a clean venv on my machine and
  post the environment, commands, and traceback here."

### Rule: Name the specific, not the vibe

Every claim comment names something only this issue has: the function,
the exception, the file, the test, a pointer from the thread. If my
sentence would fit any other issue, I delete it.

- Wrong: "This looks like a great first issue, I'd love to work on it!"
- Right: "`verify_password` in `core/security.py` lets passlib's
  `UnknownHashError` escape on a malformed stored hash instead of
  returning `False`; I'll check whether other malformed shapes raise a
  different exception."

### Rule: Show it, do not assert it

"Confirmed", "reproduced", "100%", and "every time" are not evidence.
If I ran it, the command and its output go in the comment. If I did
not run it, I say I did not.

- Wrong: "Confirmed, this is 100% reproducible on my machine."
- Right: a fenced block with the exact command followed by the exact
  traceback it printed, then one line saying what it shows.

### Rule: Say what differed and what I skipped

My environment and the reporter's are never identical. I name the
version, OS, and setup I used, and any deviation from the issue's
conditions, and I say what I did not try, so nobody reads more into my
result than it supports.

- Wrong: "Works exactly as described in the issue."
- Right: "Tested on Windows 11 with Python 3.11; the issue was filed
  from macOS. I did not try the Docker stack, since this function runs
  without it."

### Rule: Own the AI help plainly

I use Claude Code to draft and to check my own writing against this
guide. When a repo's policy asks for disclosure, I say what the tool
did and what I did; even when it does not ask, I never let the tool's
words replace something I did not run myself.

- Wrong: a polished report with commands I never ran.
- Right: "I used an AI assistant to help draft this report; I ran every
  command myself and the output is pasted as printed."

### Rule: Commit to one approach, and say what it rules out

(Added for the plan beat.) A plan comment names the one change I will
make, the file it lives in, and the alternatives I am not taking and
why. "Whichever turns out easier" is a plan to decide later, which is
not a plan.

- Wrong: "I'll either catch the exception in `verify_password` or
  validate the hash format up front, whichever is cleaner."
- Right: "I'll wrap the `pwd_context.verify` call in `verify_password`
  and return `False` on `UnknownHashError` and `ValueError`. I am not
  adding an up-front format check, because passlib's own identify step
  already does that work and raises these two types."

### Rule: Engage what the thread already decided

(Added for the plan beat.) If a maintainer, or a classmate before me,
has already named a culprit, posted a patch, or rejected an approach,
my comment says so and either follows it or explains the divergence.
Silence about a direction already on the thread reads as not having
read the thread.

- Wrong: a plan that proposes a docs workaround when the owner has
  already pointed at the code and posted a test binary.
- Right: "Several classmates above catch `UnknownHashError` only; my
  reproduction showed two bcrypt-prefixed shapes raise `ValueError`
  instead, so my change catches both."

### Rule: Mark the unmeasured as unmeasured

(Added for the plan beat.) Anything I have not run, timed, or tested on
the platform in question is written as an open question or a risk, not
as a result. Confidence belongs after the test plan runs, not before.

- Wrong: "This has no effect on any other caller."
- Right: "The only callers I found are the two login paths in
  `api/routes/auth.py`; I have not run the integration suite, which
  needs Docker, so that is a stated gap."

## Things I never post

- "Same as above, can confirm." My proof is my own run, in my words.
- "Kindly assign this to me" or "please reserve this issue for me."
- A date, or the word "guaranteed."
- "Obviously", "clearly", or "definitely" in front of a claim I have
  not shown.
- A me-too with no stated next step.
- A root cause I have not shown in a traceback or a diff.
- An apology paragraph before the content, or a thank-you paragraph
  after it.
- Output I trimmed without saying so, or a version I did not run.
- "Same approach as above." My plan is from my own reproduction, even
  on a shared issue.
- "I can have the PR up this week." A timeline is a promise I cannot
  keep from inside a plan.
- A change list with a refactor, a migration, or a "while I'm in
  there" item the issue never asked for.
