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
