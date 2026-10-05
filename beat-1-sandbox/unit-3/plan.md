# Plan: issue #61, health check DB probe passes a raw SQL string

Issue: https://github.com/codepath/pathreview-ai301-fa26-s1/issues/61
My repro comment: https://github.com/codepath/pathreview-ai301-fa26-s1/issues/61#issuecomment-5986719879
Code state: `main` at `f89c06fc3ff292df2a04a39ac51319d32a76b779`

## Diagnosis

`api/routes/health.py:32` passes a plain string to the session:

```python
await db.execute("SELECT 1")
```

SQLAlchemy 2.x refuses to coerce a plain string into a statement, so the
call raises before any SQL is sent. The `except Exception` on line 35
catches it, logs it, and marks postgres `unhealthy`. The database itself
is fine.

The evidence I rely on, from my repro run (`repro_61.py`, SQLAlchemy
2.1.3, PostgreSQL 16.15):

- Block [0], the database is reachable from the same session factory:
  ```
  yes: PostgreSQL 16.15 on x86_64-pc-linux-musl
  ```
- Block [1], the probe exactly as line 32 writes it:
  ```
  exception: sqlalchemy.exc.ArgumentError
  message:   Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')
  ```
  The traceback ends in `coercions._no_text_coercion`, reached from
  `orm/session.py` `_execute_internal`, so the rejection happens while
  coercing the argument.
- Block [2], the control. The same statement wrapped in `text()`
  returns:
  ```
  returned: 1
  ```
- Block [3], the real handler with a live session:
  ```
  [error    ] postgres_health_check_failed   error="Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')"
  [error    ] redis_health_check_failed      error="'Settings' object has no attribute 'redis_host'"
  ...
  dependencies: {'postgres': 'unhealthy', 'redis': 'unhealthy', 'vector_db': 'healthy'}
  ```

The only difference between [1] and [2] is the `text()` wrapping, so the
missing wrapper on line 32 is the cause. The fix goes at that line. It
does not change how the `except` block reports failures, because the
`except` block is reporting a real exception correctly.

Before deciding how wide the change is, I re-ran the search for other
raw-string `execute` calls myself, as my repro report said I would:

```
$ grep -rnE '\.execute\(\s*["'"'"']' --include=*.py . --exclude-dir=.venv
./api/routes/health.py:32:        await db.execute("SELECT 1")
./repro_61.py:35:    print('    await db.execute("SELECT 1")')
./repro_61.py:38:            await s.execute("SELECT 1")
```

Line 32 is the only one in the app; the other two hits are my own repro
script.

## Scope

In scope:

- `api/routes/health.py`: import `text` from `sqlalchemy` and change line
  32 to `await db.execute(text("SELECT 1"))`.
- A unit test for the postgres probe (see Test plan).
- `pyproject.toml`: the mypy override for `api.routes.health` disables
  `attr-defined`, `call-overload`, and `index`, and its comment ties it to
  #62 "and related #61". CONTRIBUTING says fixing a seeded bug removes its
  suppression, so I remove `call-overload` and leave `attr-defined` (#62)
  and `index`. A classmate on this thread (andrewskoblov) points out that
  `db` is unannotated in `health_check`, so mypy never type-checks the
  line 32 call: mypy passes with or without the entry, on fixed or
  unfixed code. So the mypy run only shows the removal is safe. It does
  not prove the entry was there for line 32, and I won't present it that
  way.

Not in scope:

- The redis check's `settings.redis_host` error. That is #62. Because of
  it, `/health` will still return 503 after this fix, and I will say so
  rather than fix it here.
- The vector DB placeholder, the safety-events placeholder,
  `datetime.utcnow()`, and the 503 logic.
- The `except Exception` handlers. They behave correctly once the probe
  is valid.
- Any other baseline lint or type debt.

## Files I'll touch

- `api/routes/health.py` (one import, one line)
- `tests/unit/test_health.py` (new)
- `pyproject.toml` (only the `call-overload` entry)

`repro_61.py`, `plan.md`, and `comment.md` stay untracked and out of
every commit.

## Approach

1. Create branch `fix/61-health-db-probe-text` from `main`.
2. Write the unit test first and run it against unchanged `main` so I see
   it fail.
3. Add `from sqlalchemy import text` to the imports and change line 32 to
   `await db.execute(text("SELECT 1"))`.
4. Re-run the unit test and `repro_61.py`.
5. Install the dev tools (`pip install -e ".[dev]"` or the
   `pyproject.toml` dev extras) and run `ruff`, `black --check`, `mypy`,
   and `pytest tests/unit` directly, since `make` is not installed on this
   machine. Remove `call-overload` from the health override and confirm
   mypy still passes (a safety check only, per the Scope note above).
6. Commit with a Conventional Commit message, for example
   `fix(api): wrap health check DB probe in text()` with `Fixes #61` in
   the footer.

## Test plan

1. **Re-run my unit 2 repro.** `./.venv/Scripts/python.exe repro_61.py`
   against the changed code, with the `db` service up. Blocks [0] to [2]
   test SQLAlchemy directly, so they should look the same. Block [3] runs
   the real handler, and that is where the fix shows:
   - Before (my posted run): `postgres_health_check_failed` with the
     `ArgumentError` message, and `'postgres': 'unhealthy'`.
   - Expected after: no `postgres_health_check_failed` line, and
     `'postgres': 'healthy'` in `dependencies`.
   - Still expected after: `HTTP 503`, with `'redis': 'unhealthy'` from
     #62. That is not a sign the fix failed.
2. **Unit test.** Call `health_check` with a stand-in session whose
   `execute` records the statement it receives, and assert it is a
   SQLAlchemy `TextClause` whose text is `SELECT 1`. Against unchanged
   `main` it fails because the argument is a `str`. After the fix it
   passes. The redis check (#62) still fails, so the handler always
   raises a 503 `HTTPException`; the test catches that before checking
   the recorded statement. It needs no running database, so it can live
   in `tests/unit`. `pyproject.toml` sets no `asyncio_mode`, so the test
   is marked `@pytest.mark.asyncio`.
3. **Repo checks.** `ruff check`, `black --check`, `mypy`, and
   `pytest tests/unit`, all passing, with output saved.

## Risks and unknowns

- **Python version:** I am on Python 3.13.14 and the repo targets 3.11.
  The change uses no version-specific features, but CI is the real check.
- **Tooling I haven't run yet:** `make` is not installed here, and mypy
  and pytest are not in my venv yet, so I have not run the repo's own
  checks. CI is the final word on lint and type checks.
- **The unit test's stand-in session:** it checks what line 32 passes,
  not whether Postgres accepts it. The repro run in step 1 covers the real
  database path.
- **Overlap with classmates:** several classmates have posted plans for
  the same one-line change. Per the house rules that doesn't block mine.

## Deviations

**`pyproject.toml` is not changed. I planned to remove `call-overload`
from the `api.routes.health` mypy override, and I put it back.** Once I
removed it, mypy reported a `call-overload` error at line 47, the redis
client call `redis.Redis(host=settings.redis_host, port=settings.redis_port, ...)`,
not at line 32:

```
$ ./.venv/Scripts/python.exe -m mypy api/routes/health.py
api\routes\health.py:47: error: No overload variant of "Redis" matches argument types "Any", "Any", "int", "bool"  [call-overload]
Found 1 error in 1 file (checked 1 source file)
```

So that suppression covers #62's redis code, not this issue. Removing it
would turn the typecheck red for a bug this change doesn't fix. With the
entry restored, mypy reports `Success: no issues found in 1 source file`.
The final change is `api/routes/health.py` (one import, one line) plus
`tests/unit/test_health.py`. I found this before posting the plan
comment, so I updated `comment.md` to leave the override alone and quote
the line 47 error. The posted comment matches what I built.

Everything else went as planned: the unit test failed on unchanged
`main` (`assert isinstance('SELECT 1', TextClause)` was False) and
passes after the fix; `repro_61.py` block [3] went from
`postgres_health_check_failed` / `'postgres': 'unhealthy'` to
`postgres_health_check_passed` / `'postgres': 'healthy'`, with the 503
still there because of #62. ruff, black, and mypy pass on the changed
files, and `pytest tests/unit` gives 376 passed, 53 xfailed.
