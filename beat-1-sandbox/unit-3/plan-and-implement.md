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

scorp6on

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/61#issuecomment-5987132342

Plan for this one, built from my repro above (`main` at `f89c06f`, SQLAlchemy 2.1.3).

**Cause.** In my run, `await db.execute("SELECT 1")` raised `ArgumentError: Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')`. The same statement wrapped in `text()` returned `1` on the same database. The database was reachable throughout, so I read the missing `text()` on `api/routes/health.py:32` as the cause. The `except` block is correctly reporting a real exception.

**Change.** Import `text` from `sqlalchemy` and change line 32 to `await db.execute(text("SELECT 1"))`. Before settling on that scope, I re-ran the search for other raw-string `execute` calls myself:

```
$ grep -rnE '\.execute\(\s*["'"'"']' --include=*.py . --exclude-dir=.venv
./api/routes/health.py:32:        await db.execute("SELECT 1")
```

(The command also prints two hits in my own untracked `repro_61.py`, left out above.) Line 32 is the only one in the app. I'm leaving the `pyproject.toml` mypy override for this module alone. As andrewskoblov noted above, `db` is unannotated in `health_check`, so mypy never checks line 32. When I removed `call-overload` locally, mypy flagged the redis client on line 47 instead, which is #62's code:

```
api\routes\health.py:47: error: No overload variant of "Redis" matches argument types "Any", "Any", "int", "bool"  [call-overload]
```

That differs from andrewskoblov's clean mypy run with the entry removed. My guess, which I haven't confirmed, is that it's because I have the repo's `types-redis` dev dependency installed. Either way, that entry looks like it belongs to #62's fix, not this one. Out of scope: the redis check (#62), the placeholder checks, the 503 logic, and the mypy override.

**How I'll check it.**
- Re-run my `repro_61.py`. Before the fix, block [3] (the real handler) logged `postgres_health_check_failed` and returned `'postgres': 'unhealthy'`. After it, I expect no `postgres_health_check_failed` line and `'postgres': 'healthy'`.
- `/health` will still return 503 because of #62's `redis_host` error, so the status code alone won't show this fix working.
- Add a unit test in `tests/unit` that fails on current `main` because the probe receives a `str`, and passes once it receives a `TextClause`. Because of #62 the handler still raises a 503, so the test catches that before checking the statement.
- Run ruff, black, mypy, and the unit tests.

**Unknowns.** I'm on Python 3.13 against the 3.11 target, and `make` isn't installed here, so I run ruff, black, mypy, and pytest directly. CI is the final word on those. If the build ends up different from this plan, I'll post what changed here.

I used an AI assistant (Claude Code) to help draft this plan. I ran the repro and the grep myself and understand every change proposed.

---

## Your branch

**Branch**

fix/61-health-db-probe-text

**Evidence**

Before: `main` at `f89c06f`, with `api/routes/health.py:32` reading `await db.execute("SELECT 1")`, against the `postgres:16-alpine` `db` service from `docker-compose.yml`. The unit test is the one added on the branch, run here against the unfixed handler.

```
$ ./.venv/Scripts/python.exe repro_61.py   # before: main, health.py unchanged
DATABASE_URL: postgresql+asyncpg://pathreview:pathreview@localhost:5433/pathreview_dev

[0] is the database actually reachable?
2026-10-04 22:14:21,334 INFO sqlalchemy.engine.Engine select pg_catalog.version()
2026-10-04 22:14:21,334 INFO sqlalchemy.engine.Engine [raw sql] ()
2026-10-04 22:14:21,345 INFO sqlalchemy.engine.Engine select current_schema()
2026-10-04 22:14:21,345 INFO sqlalchemy.engine.Engine [raw sql] ()
2026-10-04 22:14:21,349 INFO sqlalchemy.engine.Engine show standard_conforming_strings
2026-10-04 22:14:21,349 INFO sqlalchemy.engine.Engine [raw sql] ()
2026-10-04 22:14:21,351 INFO sqlalchemy.engine.Engine BEGIN (implicit)
2026-10-04 22:14:21,351 INFO sqlalchemy.engine.Engine select version()
2026-10-04 22:14:21,351 INFO sqlalchemy.engine.Engine [generated in 0.00010s] ()
    yes: PostgreSQL 16.15 on x86_64-pc-linux-musl
2026-10-04 22:14:21,353 INFO sqlalchemy.engine.Engine ROLLBACK

[1] the probe exactly as api/routes/health.py:32 writes it
    await db.execute("SELECT 1")
    exception: sqlalchemy.exc.ArgumentError
    message:   Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')
    traceback:
Traceback (most recent call last):
  File "C:\Users\vgnna\Documents\codepath\pathreview-ai301-fa26-s1\repro_61.py", line 38, in main
    await s.execute("SELECT 1")
  File "C:\Users\vgnna\Documents\codepath\pathreview-ai301-fa26-s1\.venv\Lib\site-packages\sqlalchemy\ext\asyncio\session.py", line 651, in execute
    result = await greenlet_spawn(
             ^^^^^^^^^^^^^^^^^^^^^
    ...<6 lines>...
    )
    ^
  File "C:\Users\vgnna\Documents\codepath\pathreview-ai301-fa26-s1\.venv\Lib\site-packages\sqlalchemy\util\concurrency.py", line 199, in greenlet_spawn
    result = context.switch(*args, **kwargs)
  File "C:\Users\vgnna\Documents\codepath\pathreview-ai301-fa26-s1\.venv\Lib\site-packages\sqlalchemy\orm\session.py", line 2473, in execute
    return self._execute_internal(
           ~~~~~~~~~~~~~~~~~~~~~~^
        statement,
        ^^^^^^^^^^
    ...<4 lines>...
        _add_event=_add_event,
        ^^^^^^^^^^^^^^^^^^^^^^
    )
    ^
  File "C:\Users\vgnna\Documents\codepath\pathreview-ai301-fa26-s1\.venv\Lib\site-packages\sqlalchemy\orm\session.py", line 2212, in _execute_internal
    statement = coercions.expect(roles.StatementRole, statement)
  File "C:\Users\vgnna\Documents\codepath\pathreview-ai301-fa26-s1\.venv\Lib\site-packages\sqlalchemy\sql\coercions.py", line 405, in expect
    resolved = impl._literal_coercion(
        element, argname=argname, **kw
    )
  File "C:\Users\vgnna\Documents\codepath\pathreview-ai301-fa26-s1\.venv\Lib\site-packages\sqlalchemy\sql\coercions.py", line 631, in _literal_coercion
    return self._text_coercion(element, argname, **kw)
           ~~~~~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^
  File "C:\Users\vgnna\Documents\codepath\pathreview-ai301-fa26-s1\.venv\Lib\site-packages\sqlalchemy\sql\coercions.py", line 624, in _text_coercion
    return _no_text_coercion(element, argname)
  File "C:\Users\vgnna\Documents\codepath\pathreview-ai301-fa26-s1\.venv\Lib\site-packages\sqlalchemy\sql\coercions.py", line 594, in _no_text_coercion
    raise exc_cls(
    ...<7 lines>...
    ) from err
sqlalchemy.exc.ArgumentError: Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')

[2] control: the same statement wrapped in text()
    await db.execute(text("SELECT 1"))
2026-10-04 22:14:21,361 INFO sqlalchemy.engine.Engine BEGIN (implicit)
2026-10-04 22:14:21,361 INFO sqlalchemy.engine.Engine SELECT 1
2026-10-04 22:14:21,361 INFO sqlalchemy.engine.Engine [generated in 0.00010s] ()
    returned: 1
2026-10-04 22:14:21,363 INFO sqlalchemy.engine.Engine ROLLBACK

[3] the real route handler, called with a live session
2026-10-04 22:14:21 [error    ] postgres_health_check_failed   error="Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')"
2026-10-04 22:14:21 [error    ] redis_health_check_failed      error="'Settings' object has no attribute 'redis_host'"
2026-10-04 22:14:21 [debug    ] vector_db_health_check_passed
    raised HTTP 503
    dependencies: {'postgres': 'unhealthy', 'redis': 'unhealthy', 'vector_db': 'healthy'}
$ ./.venv/Scripts/python.exe -m pytest tests/unit/test_health.py -q -p no:warnings   # before
F                                                                        [100%]
================================== FAILURES ===================================
__________ TestHealthCheck.test_postgres_probe_executes_textual_sql ___________

self = <tests.unit.test_health.TestHealthCheck object at 0x000002B62BA58910>

    @pytest.mark.asyncio
    async def test_postgres_probe_executes_textual_sql(self):
        """The postgres probe passes a text() clause, not a bare string (#61)."""
        db = AsyncMock()
    
        # The redis check (#62) can still fail, which makes the handler raise 503.
        with suppress(HTTPException):
            await health_check(db=db)
    
        db.execute.assert_awaited_once()
        statement = db.execute.await_args.args[0]
>       assert isinstance(statement, TextClause)
E       AssertionError: assert False
E        +  where False = isinstance('SELECT 1', TextClause)

tests\unit\test_health.py:28: AssertionError
---------------------------- Captured stdout call -----------------------------
2026-10-04 22:17:57 [debug    ] postgres_health_check_passed
2026-10-04 22:17:57 [error    ] redis_health_check_failed      error="'Settings' object has no attribute 'redis_host'"
2026-10-04 22:17:57 [debug    ] vector_db_health_check_passed
=========================== short test summary info ===========================
FAILED tests/unit/test_health.py::TestHealthCheck::test_postgres_probe_executes_textual_sql
1 failed in 1.24s
```

After: branch `fix/61-health-db-probe-text` (commit `6228e01`), same database.

```
$ ./.venv/Scripts/python.exe repro_61.py   # after: fix/61-health-db-probe-text
DATABASE_URL: postgresql+asyncpg://pathreview:pathreview@localhost:5433/pathreview_dev

[0] is the database actually reachable?
2026-10-04 22:14:25,830 INFO sqlalchemy.engine.Engine select pg_catalog.version()
2026-10-04 22:14:25,830 INFO sqlalchemy.engine.Engine [raw sql] ()
2026-10-04 22:14:25,833 INFO sqlalchemy.engine.Engine select current_schema()
2026-10-04 22:14:25,833 INFO sqlalchemy.engine.Engine [raw sql] ()
2026-10-04 22:14:25,835 INFO sqlalchemy.engine.Engine show standard_conforming_strings
2026-10-04 22:14:25,835 INFO sqlalchemy.engine.Engine [raw sql] ()
2026-10-04 22:14:25,837 INFO sqlalchemy.engine.Engine BEGIN (implicit)
2026-10-04 22:14:25,838 INFO sqlalchemy.engine.Engine select version()
2026-10-04 22:14:25,838 INFO sqlalchemy.engine.Engine [generated in 0.00010s] ()
    yes: PostgreSQL 16.15 on x86_64-pc-linux-musl
2026-10-04 22:14:25,840 INFO sqlalchemy.engine.Engine ROLLBACK

[1] the probe exactly as api/routes/health.py:32 writes it
    await db.execute("SELECT 1")
    exception: sqlalchemy.exc.ArgumentError
    message:   Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')
    traceback:
Traceback (most recent call last):
  File "C:\Users\vgnna\Documents\codepath\pathreview-ai301-fa26-s1\repro_61.py", line 38, in main
    await s.execute("SELECT 1")
  File "C:\Users\vgnna\Documents\codepath\pathreview-ai301-fa26-s1\.venv\Lib\site-packages\sqlalchemy\ext\asyncio\session.py", line 651, in execute
    result = await greenlet_spawn(
             ^^^^^^^^^^^^^^^^^^^^^
    ...<6 lines>...
    )
    ^
  File "C:\Users\vgnna\Documents\codepath\pathreview-ai301-fa26-s1\.venv\Lib\site-packages\sqlalchemy\util\concurrency.py", line 199, in greenlet_spawn
    result = context.switch(*args, **kwargs)
  File "C:\Users\vgnna\Documents\codepath\pathreview-ai301-fa26-s1\.venv\Lib\site-packages\sqlalchemy\orm\session.py", line 2473, in execute
    return self._execute_internal(
           ~~~~~~~~~~~~~~~~~~~~~~^
        statement,
        ^^^^^^^^^^
    ...<4 lines>...
        _add_event=_add_event,
        ^^^^^^^^^^^^^^^^^^^^^^
    )
    ^
  File "C:\Users\vgnna\Documents\codepath\pathreview-ai301-fa26-s1\.venv\Lib\site-packages\sqlalchemy\orm\session.py", line 2212, in _execute_internal
    statement = coercions.expect(roles.StatementRole, statement)
  File "C:\Users\vgnna\Documents\codepath\pathreview-ai301-fa26-s1\.venv\Lib\site-packages\sqlalchemy\sql\coercions.py", line 405, in expect
    resolved = impl._literal_coercion(
        element, argname=argname, **kw
    )
  File "C:\Users\vgnna\Documents\codepath\pathreview-ai301-fa26-s1\.venv\Lib\site-packages\sqlalchemy\sql\coercions.py", line 631, in _literal_coercion
    return self._text_coercion(element, argname, **kw)
           ~~~~~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^
  File "C:\Users\vgnna\Documents\codepath\pathreview-ai301-fa26-s1\.venv\Lib\site-packages\sqlalchemy\sql\coercions.py", line 624, in _text_coercion
    return _no_text_coercion(element, argname)
  File "C:\Users\vgnna\Documents\codepath\pathreview-ai301-fa26-s1\.venv\Lib\site-packages\sqlalchemy\sql\coercions.py", line 594, in _no_text_coercion
    raise exc_cls(
    ...<7 lines>...
    ) from err
sqlalchemy.exc.ArgumentError: Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')

[2] control: the same statement wrapped in text()
    await db.execute(text("SELECT 1"))
2026-10-04 22:14:25,846 INFO sqlalchemy.engine.Engine BEGIN (implicit)
2026-10-04 22:14:25,846 INFO sqlalchemy.engine.Engine SELECT 1
2026-10-04 22:14:25,846 INFO sqlalchemy.engine.Engine [generated in 0.00009s] ()
    returned: 1
2026-10-04 22:14:25,848 INFO sqlalchemy.engine.Engine ROLLBACK

[3] the real route handler, called with a live session
2026-10-04 22:14:26,169 INFO sqlalchemy.engine.Engine BEGIN (implicit)
2026-10-04 22:14:26,169 INFO sqlalchemy.engine.Engine SELECT 1
2026-10-04 22:14:26,169 INFO sqlalchemy.engine.Engine [cached since 0.3226s ago] ()
2026-10-04 22:14:26 [debug    ] postgres_health_check_passed
2026-10-04 22:14:26 [error    ] redis_health_check_failed      error="'Settings' object has no attribute 'redis_host'"
2026-10-04 22:14:26 [debug    ] vector_db_health_check_passed
    raised HTTP 503
    dependencies: {'postgres': 'healthy', 'redis': 'unhealthy', 'vector_db': 'healthy'}
2026-10-04 22:14:26,270 INFO sqlalchemy.engine.Engine ROLLBACK
$ ./.venv/Scripts/python.exe -m pytest tests/unit/test_health.py -q -p no:warnings   # after
.                                                                        [100%]
1 passed in 0.97s
```

In block [3], `postgres_health_check_failed` / `'postgres': 'unhealthy'` became `postgres_health_check_passed` / `'postgres': 'healthy'`, and the new unit test went from failing to passing. The 503 and `'redis': 'unhealthy'` are #62 in both runs, as the plan predicted.

## Eval iterations

**Run history**

1. Full run: 19/19 scored items agreed. pkg-02 errored instead of being graded, so this run had no bar result and wrote no `eval-run.txt`.
2. Full run, with template comments restored in the skill files: 18/19. pkg-02 errored again, and pkg-14 (clear-accept) disagreed on `certainty-is-honest`.
3. Partial, `--only pkg-02,pkg-14,pkg-07,pkg-06,pkg-20`, after loosening `certainty-is-honest` and setting `PYTHONUTF8=1` for the pkg-02 error: 4/5. pkg-02 and pkg-14 now agreed; the canary pkg-20 (thread-convention) flipped to accept.
4. Partial, `--only pkg-20,pkg-04,pkg-03,pkg-13`, after tightening the AI-disclosure and PR-precondition part of `comment-fits-thread-and-repo`: 3/4. pkg-20 agreed again; pkg-03 disagreed on `certainty-is-honest`.
5. Partial, `--only pkg-03`: 0/1 (same failure; used to read the check's evidence line).
6. Partial, `--only pkg-03,pkg-14,pkg-07,pkg-11,pkg-06`, after narrowing clause (1) of `certainty-is-honest`: 5/5.
7. Full run, saved to `eval-run.txt`: 20/20 scored items, every category matched (bar 18/20: PASS).

**Package analysis**

pkg-14 (zellij, raw OSC color responses leaking into the pane on reattach). Gold label: accept. My rubric rejected it in full run 2 and accepted it in the final run.

The plan's diagnosis predicts every run in the repro evidence: fresh attach is clean, every reattach leaks, 0.44.1 is clean, and an empty cache gives one clean attach. Its change acts on that mechanism (drain pending OSC responses before pane input is wired). It defers the Windows variant with a reason. Its test plan re-runs the 5-cycle reattach loop. In run 2, every check passed except `certainty-is-honest`. The grader wrote: "Mechanism and 'reattach-path change in 0.44.2' are stated as fact, yet the plan admits no trace has been done ('functions to be pinned... after tracing')". So my check was punishing the plan for stating its working cause plainly and for openly deferring the exact function names. Both are what an honest plan does. Whether the cause is supported was already graded by `diagnosis-follows-evidence`, which it passed. I rewrote the check so a plainly stated cause and openly deferred details are not overconfidence. After that, pkg-14 passed every check and was accepted, matching gold.

**Check rationale**

| certainty-is-honest | The plan's and the plan comment's claims of completed work, outcome, and completeness, read against what the repro evidence and thread actually establish. | Passes unless the plan or comment does one of these: (1) rests its diagnosis or its fix on work the package does not show, where the repro evidence does not support that claim either (for example "I traced this to X" when the repro evidence points elsewhere or shows nothing about X). Incidental side remarks about local checks that the diagnosis does not depend on are not overconfidence; (2) guarantees more than the reproduced behavior ("fixes all cases", "no risk", "cannot break anything"); or (3) answers a maintainer's stated concern in the thread (for example "the fix is hard" or "this is intended behavior") by promising a simple fix without addressing it. A plan stating its working cause plainly is not overconfidence: whether that cause is supported is graded by diagnosis-follows-evidence, not here. Details openly deferred to the build ("exact function to be pinned after tracing") are honest. A plan without a risks section is not a fail by itself. | required |

It reads this way because of two over-strict versions. The first version failed any plan that "presents a guess as fact ('I traced this to...' when no trace is shown...)". That rejected pkg-14, a good plan that names its mechanism without hedging and leaves the exact functions for the build. So I added that stating a working cause plainly isn't overconfidence: grounding belongs to `diagnosis-follows-evidence`, and this check shouldn't grade it twice. The next version still said "claims work the package does not show". That rejected pkg-03, where the grader caught the side remark "I have verified gzip, xz, and zstd locally", which the diagnosis does not depend on. So clause (1) now fails only when the diagnosis or fix rests on unshown work that the repro evidence doesn't support either, and incidental side remarks are excluded. What's left is the real failure family: claimed traces that point away from the evidence, guarantees beyond the reproduced behavior, and promising a simple fix over a maintainer's stated concern.

**Trade-offs**

Loosening `certainty-is-honest` means it now misses a plan whose only flaw is an unverified side claim, like pkg-03's "I have verified gzip, xz, and zstd locally". A maintainer might still want that claim backed, but my rubric accepts it as long as the diagnosis and fix stand on the repro evidence. I accept that miss.

I checked that the loosening didn't flip anything else in two ways. First, before loosening, I listed each reject's failed checks in `results.json` from full run 2. Every reject also failed at least one other required check, so none rested on `certainty-is-honest` alone. Second, I re-ran canaries with `--only`: pkg-06 (scope-creep), and pkg-07 and pkg-11 (wrong-cause). Those are the packages that had failed `certainty-is-honest` alongside other checks, and all three still rejected. The final full run confirmed 20/20.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
