# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Posted upstream

**GitHub username**

foojanbabaeeian

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/72#issuecomment-6087221027

Text of the comment as posted:

````markdown
**Plan**, built from my repro report above (https://github.com/codepath/pathreview-ai301-fa26-s1/issues/72#issuecomment-5806481719, Windows 11, Python 3.11.9, passlib 1.7.4, bcrypt 4.3.0, commit `f89c06f`). I see several classmates' plans on this thread and six open PRs against this issue (#75, #84, #85, #92, #102, #109), most taking an `except ValueError` route; per the course's house rules I am posting my own plan from my own reproduction rather than piggybacking.

**Cause, from my repro.** `verify_password` in `core/security.py` returns `bool(pwd_context.verify(plain_password, hashed_password))` with no handler. For `'not_a_valid_bcrypt_hash'`, `''`, and `'plaintext'`, passlib's identify step raised `passlib.exc.UnknownHashError: hash could not be identified`, exit code 1. For `'$2b$notarealhash'` and `'$2b$12$tooshort'`, identification succeeded and the bcrypt backend raised a bare `ValueError` (`not enough values to unpack`, `salt too small`). The control run with a well-formed hash returned `True` for the right password and `False` for the wrong one, so the format of the stored hash is what decides the failure, not the comparison. One fact I checked while planning: `passlib.exc.UnknownHashError` subclasses `ValueError`, so a single `except ValueError` covers all four shapes, and a handler that catches only `UnknownHashError` would still let the two bcrypt-prefixed shapes escape.

**Change, one bounded fix.**

- `core/security.py`: wrap the `pwd_context.verify` call in `verify_password` in `try` / `except ValueError: return False`, with a comment naming the two sources (passlib's `UnknownHashError`, bcrypt's salt `ValueError`). This mirrors `decode_access_token` in the same file, which already does `except JWTError: return None`. One line added to the docstring.
- `tests/unit/test_security.py`: delete the `@pytest.mark.xfail(strict=True, reason="issue #72 (manifest H-05) ...")` marker from `test_verify_with_wrong_hash_format`, as the issue and `docs/CONTRIBUTING.md` ask, and add one parametrized test asserting `False` for `''`, `'plaintext'`, `'$2b$notarealhash'`, and `'$2b$12$tooshort'`, so the `ValueError` path is pinned too.

Not in scope: `hash_password` and the JWT helpers; the caller in `api/routes/auth.py` (it already treats `False` as a failed login); any logging or up-front hash-format validation; `pyproject.toml` (no suppression there names this issue).

**Test plan.** Re-run the steps from my repro on the branch: the control run still prints `True` then `False`; the direct call `verify_password('password', 'not_a_valid_bcrypt_hash')` prints `False` with exit code 0 instead of the traceback; the four-shape loop prints `-> False` four times instead of two `UnknownHashError` and two `ValueError` lines; `pytest tests/unit/test_security.py -k test_verify_with_wrong_hash_format -v` reports `PASSED` instead of `XFAIL`; then the whole `test_security.py` file and `make test-unit` pass with no `xfailed` or `xpassed` entries.

**Risks and unknowns.** Catching `ValueError` is wider than catching `UnknownHashError`; I accept that because the contract is fail closed and the only caller is the login path, where a masked error denies a login rather than granting one. I have not yet run `make test-unit`, `make lint`, or `make typecheck` here, since my venv holds only the security module's dependencies; I will install the full dev set before pushing. `core` is not one of the commit scopes CONTRIBUTING lists, so I plan to use `fix(api)` and say so in the PR. Tested on Windows only; the change has no platform-specific code.

Branch: `fix/72-verify-password-fail-closed` on my fork.

I used Claude Code to help draft this plan and check it against my own writing rules; the reproduction it rests on is my own run, and I will read every diff before it goes into the branch.
````

---

## Your branch

**Branch**

fix/72-verify-password-fail-closed

**Evidence**

My unit 2 reproduction steps, re-run on `main` (before) and on the branch (after).
Windows 11 Home (10.0.26200), Git Bash, Python 3.11.9 in `.venv`, passlib 1.7.4,
bcrypt 4.3.0. Two filters applied to every block and nothing else: passlib 1.7.4's
`(trapped) error reading bcrypt version` probe (quoted once in my unit 2 report) and
the pydantic deprecation warning from `core/config.py` are dropped, and absolute paths
are shortened to `...`.

Before, on `main` at `f89c06f`:

```
$ git rev-parse --abbrev-ref HEAD; git rev-parse --short HEAD
main
f89c06f

$ .venv/Scripts/python -c "from core.security import verify_password, hash_password; h = hash_password('password'); print('control (valid hash):', verify_password('password', h)); print('control (wrong password):', verify_password('wrong', h))"
control (valid hash): True
control (wrong password): False

$ .venv/Scripts/python -c "from core.security import verify_password; print(verify_password('password', 'not_a_valid_bcrypt_hash'))"; echo "exit code: $?"
Traceback (most recent call last):
  File "<string>", line 1, in <module>
  File "...\core\security.py", line 37, in verify_password
    return bool(pwd_context.verify(plain_password, hashed_password))
  File "...\.venv\Lib\site-packages\passlib\context.py", line 2343, in verify
    record = self._get_or_identify_record(hash, scheme, category)
  File "...\.venv\Lib\site-packages\passlib\context.py", line 2031, in _get_or_identify_record
    return self._identify_record(hash, category)
  File "...\.venv\Lib\site-packages\passlib\context.py", line 1132, in identify_record
    raise exc.UnknownHashError("hash could not be identified")
passlib.exc.UnknownHashError: hash could not be identified
exit code: 1

$ .venv/Scripts/python -c "
from core.security import verify_password
for bad in ['', 'plaintext', '\$2b\$notarealhash', '\$2b\$12\$tooshort']:
    try:
        print(repr(bad), '->', verify_password('password', bad))
    except Exception as e:
        print(repr(bad), '-> raised', type(e).__module__ + '.' + type(e).__name__ + ':', e)
"
'' -> raised passlib.exc.UnknownHashError: hash could not be identified
'plaintext' -> raised passlib.exc.UnknownHashError: hash could not be identified
'$2b$notarealhash' -> raised builtins.ValueError: not enough values to unpack (expected 2, got 1)
'$2b$12$tooshort' -> raised builtins.ValueError: salt too small (bcrypt requires exactly 22 chars)

$ .venv/Scripts/python -m pytest tests/unit/test_security.py -k test_verify_with_wrong_hash_format -v -p no:cacheprovider
tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format XFAIL [100%]
================ 24 deselected, 1 xfailed, 1 warning in 2.41s =================
```

Intermediate check, with the `core/security.py` change applied and the xfail marker
still present. This is the `XPASS(strict)` signal `docs/CONTRIBUTING.md` says to expect:

```
$ .venv/Scripts/python -m pytest tests/unit/test_security.py -k test_verify_with_wrong_hash_format -v -p no:cacheprovider
tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format FAILED [100%]
_______________ TestSecurity.test_verify_with_wrong_hash_format _______________
[XPASS(strict)] issue #72 (manifest H-05): password verify raises UnknownHashError instead of returning False
FAILED tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format
================= 1 failed, 24 deselected, 1 warning in 1.75s =================
```

After, on `fix/72-verify-password-fail-closed` at `a7e0f30`, the same commands:

```
$ git rev-parse --abbrev-ref HEAD; git rev-parse --short HEAD
fix/72-verify-password-fail-closed
a7e0f30

$ .venv/Scripts/python -c "from core.security import verify_password, hash_password; h = hash_password('password'); print('control (valid hash):', verify_password('password', h)); print('control (wrong password):', verify_password('wrong', h))"
control (valid hash): True
control (wrong password): False

$ .venv/Scripts/python -c "from core.security import verify_password; print(verify_password('password', 'not_a_valid_bcrypt_hash'))"; echo "exit code: $?"
False
exit code: 0

$ .venv/Scripts/python -c "
from core.security import verify_password
for bad in ['', 'plaintext', '\$2b\$notarealhash', '\$2b\$12\$tooshort']:
    try:
        print(repr(bad), '->', verify_password('password', bad))
    except Exception as e:
        print(repr(bad), '-> raised', type(e).__module__ + '.' + type(e).__name__ + ':', e)
"
'' -> False
'plaintext' -> False
'$2b$notarealhash' -> False
'$2b$12$tooshort' -> False

$ .venv/Scripts/python -m pytest tests/unit/test_security.py -k test_verify_with_wrong_hash_format -v -p no:cacheprovider
tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format PASSED [100%]
================= 1 passed, 28 deselected, 1 warning in 1.98s =================

$ .venv/Scripts/python -m pytest tests/unit/test_security.py -v -p no:cacheprovider
tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format PASSED [ 75%]
tests/unit/test_security.py::TestSecurity::test_verify_with_malformed_hash_shapes[] PASSED [ 79%]
tests/unit/test_security.py::TestSecurity::test_verify_with_malformed_hash_shapes[plaintext] PASSED [ 82%]
tests/unit/test_security.py::TestSecurity::test_verify_with_malformed_hash_shapes[$2b$notarealhash] PASSED [ 86%]
tests/unit/test_security.py::TestSecurity::test_verify_with_malformed_hash_shapes[$2b$12$tooshort] PASSED [ 89%]
======================= 29 passed, 1 warning in 10.48s ========================
```

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

Before spending anything I graded the four calibration packages by hand against the
finished rubric and procedure, in the order the procedure reads them (repro evidence
before plan): calib-01 passes every required check (one named file, one mechanism, the
repro re-run with the color flip as the observable); calib-02 fails `executable` and
`test-decisive`; calib-03 fails `cause-grounded` because the timing matrix shows the
26-second cost with no pager in the loop; calib-04 fails `test-decisive` alone on "run the
full test suite". All four matched the gold notes, which is the anchor the rubric's header
comment records.

1. Full run, `--save-run eval-run.txt`: **19/20**, categories `clear-accept 6/7  scope-creep 4/4
   thread-convention 2/2  unbuildable 3/3  wrong-cause 4/4`, bar PASS. This is the run in
   `eval-run.txt`; its agreement line reads `agreement: 19/20 scored items  (bar: 18/20: PASS)`.
   The one disagreement is pkg-14, graded reject on `unknowns-honest` where the gold label is
   accept.

I did not revise after the first full run and did not run `--only` re-grades. The bar and
the category floor were both met, pkg-14 is one of the four packages the gold notes call
arguable ("arguable on the deferral, ready as scoped"), and the fix I would make to flip it
is one I decided against (Trade-offs, below). Revising a file after the saved run would also
have put the uploaded `rubric.md` out of step with the fingerprint in `eval-run.txt`, so a
revision would have meant a second full run.

**Package analysis**

`pkg-14` (zellij-org/zellij#5174). My rubric decided **reject**, on `unknowns-honest`; the
gold label is **accept**, category clear-accept, with the note "honestly scoped-down:
reattach handshake fix with a regression-window repro; defers the untestable Windows variant
and says so; arguable on the deferral, ready as scoped".

My other six required checks passed it, and I agree with each of those: the diagnosis
survives the controls (0.44.1 clean on the same setup, the cache-clear control giving one
clean attach), the scope is one bounded change with the Windows variant deferred and the
reason given, and the test plan names an observable outcome ("5 consecutive SSH reattach
cycles with no rgb strings in any pane"). The check that failed reads every sentence of
certainty against the repro evidence, and this plan has two that the package does not
show: the diagnosis names a mechanism ("zellij issues OSC 4/10/11 color queries ... the
reattach path wires the client's stdin to the session before the query responses have been
consumed") that no artifact in the repro evidence shows, and the files section says "the
leak's origin is visible in `zellij --debug` output", a claim about an artifact the plan
quotes nothing from. My pass condition requires that "anything it has not measured or
tested is marked as such", and the plan writes those two as settled facts. By the rubric's
wording that is a fail, and the grader applied it as written.

Why I think the gold label is right anyway, and why I still kept the check: the OSC
mechanism is an inference that fits every control (the plan even walks through why the
cache control fits), and the plan's risk section is honest about the one thing it will
actually get wrong (eating a keystroke). The package is "ready as scoped" because a
stranger could build it and the test plan would catch a wrong mechanism. What my check
cannot tell apart is a confident inference that the evidence supports from a confident
claim the evidence disputes; it reads both as "stated as settled". That is the same
blindness that makes it catch pkg-16, the "long and confident" wrong-cause package, which
is the trade I accepted.

**Check rationale**

Quoted from `tools/plan-check/rubric.md` as currently written:

> | cause-grounded | The plan's stated cause (its diagnosis, root cause, or "as the issue states" line), read against every artifact, step, and control run in the repro-evidence block, and against any maintainer analysis in the thread highlights | Pass when the stated cause would produce the artifact the repro shows AND is consistent with every control run in the repro evidence: each control that passes or behaves differently is something the cause predicts. A cause that quotes the issue's or a maintainer's analysis passes when the repro evidence agrees with that analysis. Fail when any step or control in the repro evidence rules the cause out (the cause blames a component the control shows working on the same input; the evidence shows the damage already done before the stage the plan blames; the cost or failure appears with the blamed component removed from the loop), when the cause names a mechanism no artifact in the package shows, or when the plan names no cause at all and goes straight to changes. Read the controls before the diagnosis: a confident cause the evidence contradicts fails here even when the thread asserts it. | required |

Why it reads that way. The wrong-cause category has four scored packages and one
calibration package, and in every one of them the plan's cause is plausible on its face
and is contradicted by a control run inside the package's own repro evidence: pkg-01's
tokenizer is ruled out by the no-flag control, pkg-07's tree-shaken module by the
instance-method error printing in the same build, pkg-11's collect operator by the
top-level `[.a] | length` control, pkg-16's post-read cast by the zeros already missing in
step 4, and calib-03's key bindings by the 26-second cost with no pager in the loop. So the
check is written as a question about controls, not about plausibility: "each control that
passes or behaves differently is something the cause predicts". The three fail clauses in
parentheses are those five packages' shapes, written generally enough to cover a sixth.

The last sentence, "Read the controls before the diagnosis", is there because calib-03 is
the operator-swap trap: a polished plan that adopts the thread's confident diagnosis. A
grader who reads the plan first sees the controls as confirmation. The procedure enforces
the same order (repro evidence is read-order step 6, the plan is step 7), and the evidence
guide's diagnosis section lists the five control shapes by name so the grader knows what
"ruled out" looks like.

What I rejected: a pass condition that said the cause must be "supported by" the evidence.
Three clear accepts (pkg-02, pkg-05, pkg-13) state a cause the thread or maintainer
already isolated, and the repro confirms the symptom without separately proving the
mechanism; "supported by" would have made those unclear. "Consistent with every control"
passes them and still fails the five, because what separates the two groups is not how
much evidence the plan cites but whether any control contradicts it.

**Trade-offs**

The check I would loosen to flip pkg-14 is `unknowns-honest`, whose fail clause "a cause
stated as certain that the evidence disputes" is doing the right work, but whose pass
condition "anything it has not measured or tested is marked as such" is what caught
pkg-14's unquoted debug-log claim. The narrower rule, "fail only when a claim is
contradicted by the package", would pass pkg-14. I did not make that change, and here is
how I know what it would have cost. With the contradiction-only rule, the check becomes a
copy of `cause-grounded`'s last fail clause, and the honesty family loses its only
independent bite: pkg-16 ("long and confident") would still fail on `cause-grounded`, but a
future package that invents an untested mechanism the controls happen not to touch would
pass both. The canaries I would have had to re-run with `--only` are pkg-16 (wrong-cause,
the confident one) and pkg-20 plus pkg-04 (the two thread-convention packages, since the
honesty and comms checks both read the comment), at about $0.60; and then a full run to
re-save, at about $4. I chose instead to keep the stricter wording and record pkg-14 as a
known miss: the plan it rejects is one a maintainer would accept, and the fix for that
blind spot is to add "or an inference the plan says it will confirm in the PR" to the pass
condition. That is the first revision I would make if a second full run were in budget,
and it is recorded here so the next version of this rubric starts from it.

What the preferred check gives up is simpler: `repo-asks-followed` never moves the verdict,
so pkg-03's vouch-flow note and calib-01's review-bandwidth nod are reported but not
rewarded, and a plan that ignores a "keep PRs small" ask still accepts if its scope is
bounded. That is on purpose: the scope check already holds the line that matters.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
