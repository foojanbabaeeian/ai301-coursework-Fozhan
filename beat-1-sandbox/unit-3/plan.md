# Plan: issue #72, `verify_password` fails closed on malformed stored hashes

Issue: https://github.com/codepath/pathreview-ai301-fa26-s1/issues/72
Repro this plan builds on: my posted report,
https://github.com/codepath/pathreview-ai301-fa26-s1/issues/72#issuecomment-5806481719
(Windows 11, Python 3.11.9, passlib 1.7.4, bcrypt 4.3.0, commit `f89c06f`).

## Diagnosis

`verify_password` in `core/security.py` is one line with nothing around it:

```python
return bool(pwd_context.verify(plain_password, hashed_password))
```

My reproduction showed what escapes from that line. The trigger the covering
test uses:

```
$ .venv/Scripts/python -c "
from core.security import verify_password
verify_password('password', 'not_a_valid_bcrypt_hash')
"
  File "...\pathreview-ai301-fa26-s1\core\security.py", line 37, in verify_password
    return bool(pwd_context.verify(plain_password, hashed_password))
  File "...\passlib\context.py", line 1132, in identify_record
    raise exc.UnknownHashError("hash could not be identified")
passlib.exc.UnknownHashError: hash could not be identified
$ echo $?
1
```

And the other malformed shapes I tried:

```
'' -> raised passlib.exc.UnknownHashError: hash could not be identified
'plaintext' -> raised passlib.exc.UnknownHashError: hash could not be identified
'$2b$notarealhash' -> raised builtins.ValueError: not enough values to unpack (expected 2, got 1)
'$2b$12$tooshort' -> raised builtins.ValueError: salt too small (bcrypt requires exactly 22 chars)
```

The control run in the same report rules out the password side: with a
well-formed hash, `verify_password('password', h)` printed `True` and
`verify_password('wrong', h)` printed `False`. So the failure is decided by
the stored hash's format, not by the comparison.

Cause: passlib's `CryptContext.verify` first tries to identify the hash's
scheme and raises `UnknownHashError` when no scheme matches (empty string,
plain text). When the string carries a bcrypt prefix but is otherwise
malformed, identification succeeds and the bcrypt backend raises a plain
`ValueError` while parsing the salt. `verify_password` converts neither to
`False`, so both propagate to the caller (the login route in
`api/routes/auth.py`, line 80, which is the only caller outside the tests).

One more fact I checked while planning, because it decides what the handler
catches: `passlib.exc.UnknownHashError` is a subclass of `ValueError`
(`UnknownHashError.__mro__` is `UnknownHashError, ValueError, Exception`).
So a single `except ValueError` covers all four shapes above, and a handler
that caught only `UnknownHashError` would still let the two bcrypt-prefixed
shapes escape, which is what my repro comment warned about.

## Scope

In scope:

- `verify_password` in `core/security.py`: return `False` instead of raising
  when the stored hash is malformed.
- `tests/unit/test_security.py`: remove the `@pytest.mark.xfail(strict=True,
  reason="issue #72 (manifest H-05) ...")` marker from
  `test_verify_with_wrong_hash_format`, as the issue and
  `docs/CONTRIBUTING.md` ask, and add one test covering the four other
  malformed shapes from my repro so the `ValueError` path is pinned too.

Not in scope:

- `hash_password`, `create_access_token`, `decode_access_token`: untouched.
- The caller in `api/routes/auth.py`: untouched. It already treats `False`
  as a failed login, so it benefits without a change.
- Logging a malformed hash, or validating the hash format before calling
  passlib: not added. Passlib's identify step already does that work, and
  the issue asks only that verification fail closed.
- `pyproject.toml`: no change. I grepped it for `72`; the only mention of
  xfail is the general comment at line 150, and no mypy or ruff suppression
  names this issue.

## Files

- `core/security.py` (one function, `verify_password`)
- `tests/unit/test_security.py` (one marker removed, one test added)

## Approach

1. In `core/security.py`, wrap the call in `verify_password`:

   ```python
   try:
       return bool(pwd_context.verify(plain_password, hashed_password))
   except ValueError:
       # passlib raises UnknownHashError (a ValueError subclass) for a hash it
       # cannot identify, and the bcrypt backend raises a bare ValueError for a
       # bcrypt-prefixed hash with a malformed salt. Both mean "not a usable
       # stored hash", so verification fails closed.
       return False
   ```

   This mirrors the pattern already in the same file: `decode_access_token`
   wraps `jwt.decode` in `except JWTError: return None`. I will also add one
   line to the docstring's Returns section saying a malformed stored hash
   returns `False`.

2. In `tests/unit/test_security.py`, delete the four-line
   `@pytest.mark.xfail(...)` decorator above `test_verify_with_wrong_hash_format`
   (lines 218 to 221 at `f89c06f`). The test body stays as shipped.

3. Below it, add `test_verify_with_malformed_bcrypt_prefixed_hash`, a
   `pytest.mark.parametrize` over `""`, `"plaintext"`, `"$2b$notarealhash"`,
   and `"$2b$12$tooshort"`, each asserting `verify_password("password", bad)
   is False`. The two `$2b$` cases are the ones that raise `ValueError`
   rather than `UnknownHashError`, so they guard against a future narrowing
   of the handler.

4. One commit on branch `fix/72-verify-password-fail-closed`, message in the
   repo's Conventional Commits shape. `core` is not one of the scopes
   `docs/CONTRIBUTING.md` lists (ingestion, rag, agent, safety, api,
   frontend); I will use `fix(api)` because the auth route is the consumer,
   and say so in the PR.

## Test plan

The repro steps from my unit 2 report, re-run against the branch, with what I
expect after the fix:

1. Control run (unchanged behavior expected):

   ```
   .venv/Scripts/python -c "
   from core.security import verify_password, hash_password
   h = hash_password('password')
   print('control (valid hash):', verify_password('password', h))
   print('control (wrong password):', verify_password('wrong', h))
   "
   ```

   Expected: `control (valid hash): True` and `control (wrong password):
   False`, same as before.

2. The issue's trigger, direct call:

   ```
   .venv/Scripts/python -c "
   from core.security import verify_password
   print(verify_password('password', 'not_a_valid_bcrypt_hash'))
   "; echo $?
   ```

   Before: the `UnknownHashError` traceback and exit code 1. Expected after:
   prints `False`, exit code 0.

3. The four malformed shapes, same loop as the repro:

   ```
   .venv/Scripts/python -c "
   from core.security import verify_password
   for bad in ['', 'plaintext', '\$2b\$notarealhash', '\$2b\$12\$tooshort']:
       try:
           print(repr(bad), '->', verify_password('password', bad))
       except Exception as e:
           print(repr(bad), '-> raised', type(e).__module__ + '.' + type(e).__name__ + ':', e)
   "
   ```

   Before: two `UnknownHashError` lines and two `ValueError` lines. Expected
   after: four lines ending in `-> False`.

4. The covering test:

   ```
   .venv/Scripts/python -m pytest tests/unit/test_security.py -k test_verify_with_wrong_hash_format -v
   ```

   Before: `XFAIL`. Expected after (marker removed): `PASSED`. As an
   intermediate check, with the fix applied but the marker still present,
   pytest should report `XPASS(strict)` as a failure, which is the
   CONTRIBUTING-described signal that the marker must go.

5. The whole security test file, then the unit suite:

   ```
   .venv/Scripts/python -m pytest tests/unit/test_security.py -v
   make test-unit
   ```

   Expected: every test in `test_security.py` passes, including the new
   parametrized one, with no `xfailed` or `xpassed` entries; `make test-unit`
   passes with no new failures.

## Risks and unknowns

- Catching `ValueError` is wider than catching `UnknownHashError`. A
  `ValueError` raised for some other reason inside passlib would also be
  turned into `False`. I accept that because the issue's stated contract is
  fail closed, and the only caller is the login path, where a masked error
  means a denied login rather than a granted one. If a reviewer prefers the
  narrower `except (UnknownHashError, ValueError)` spelling for readability,
  that is a one-line change.
- My venv holds only the packages `core/security.py` needs (passlib, bcrypt,
  python-jose, pydantic, pytest). I have not yet run `make test-unit`, `make
  lint`, or `make typecheck` on this machine; they need the project's full
  dev dependencies, which I will install before pushing. Whether the full
  unit suite runs cleanly on Windows outside Docker is an unknown until then.
- Commit scope: `core` is not a documented scope, so `fix(api)` is a judgment
  call, flagged for review.
- The `(trapped) error reading bcrypt version` lines passlib 1.7.4 prints
  against bcrypt 4.x are unrelated to this change and will still appear.
- I tested on Windows only. The change has no platform-specific code, but I
  have not run it on macOS or Linux.

## Deviations

The plan held: the diff is the two files the plan named, the handler is
the `try` / `except ValueError: return False` block written in the
approach, the xfail marker is gone, the parametrized test covers the four
shapes listed, and it all went in as one commit. Three small things to
record, none of which changes what the posted plan promised:

1. The new test is named `test_verify_with_malformed_hash_shapes`, not
   the `..._bcrypt_prefixed_hash` name I first wrote in the build. Two of
   its four cases are not bcrypt-prefixed, so the first name was wrong
   about its own contents. The cases themselves are the ones the plan
   lists.
2. The plan said `make test-unit` had not been run and was an unknown.
   It has now been run. On this machine the first run reported 30 errors
   in tests that download tiktoken's encoding file over TLS, which fails
   behind the local antivirus certificate interception; with
   `SSL_CERT_FILE` and `REQUESTS_CA_BUNDLE` pointed at that local CA the
   same command passes (380 passed, 52 xfailed, 0 errors). The errors
   never touched `core/security.py` or its tests, and the environment
   workaround is not part of the change. `make lint` and `make typecheck`
   pass with no workaround.
3. The commit scope question stands as the plan flagged it: the commit is
   `fix(api): ...` because `core` is not a documented scope. Open PRs
   from classmates use both `fix(api)` and `fix(security)`, so the
   maintainers may prefer either.
