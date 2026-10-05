# Plan: issue #55 — skill extractor misses JavaScript/TypeScript, PostgreSQL, Docker

Issue: https://github.com/codepath/pathreview-ai301-fa26-s1/issues/55
My repro: https://github.com/codepath/pathreview-ai301-fa26-s1/issues/55#issuecomment-5882270618

## Diagnosis

From my repro on commit `f89c06f` (Linux WSL2, Python 3.14.4, pytest 9.1.1):

```
.venv/bin/pytest tests/unit/test_skill_extractor.py -v -rx
13 passed, 5 xfailed in 0.53s

.venv/bin/pytest tests/unit/test_skill_extractor.py -v --runxfail
5 failed, 13 passed in 0.16s
```

The five failures come from three separate gaps in `ingestion/parsers/skill_extractor.py`, and each one
lines up with a specific line of the code I quoted in the repro:

1. **JavaScript (`test_javascript_detection`).** The JS regex is `r"\b(import|require)\s+"`. It needs
   whitespace after the keyword, so `require('fs')` (no space before `(`) never matches, and no filename
   is passed to trigger the `.js` extension check.
2. **TypeScript (`test_text_with_typescript_files`).** `lang` only becomes `"TypeScript"` when the
   filename contains `.ts`. The sample has no filename and no `import`/`require`, so the JS/TS block
   never fires at all.
3. **PostgreSQL and Docker (`test_database_technology_detection`, `test_devops_tool_detection`,
   `test_docker_compose_detection`).** `_detect_databases` and `_detect_tools` only check
   `if db in text_lower` / `if tool in text_lower`. `"postgresql"` isn't a substring of `"psycopg2"`,
   and Dockerfile directives (`FROM`/`RUN`/`EXPOSE`) and Compose keys (`services:`/`build:`/`ports:`)
   never contain the word `"docker"`.

One extra thing I found while planning, by calling `extract_skills` directly on the TypeScript test's
sample text: it currently returns `['Python']`, not an empty list. The Python type-annotation regex
`r":\s*(int|str|float|bool|list|dict)"` matches the `str` inside `id: string`. That's why I treat the
TypeScript case as "no TypeScript signal exists" rather than "the JS signal is too narrow".

## Scope

**In scope:** add the missing detection signals for the five inputs the issue's tests exercise, and
remove those five tests' `xfail` markers, as `docs/CONTRIBUTING.md` requires for seeded bugs.

**Not in scope, deliberately:**

- The two existing cross-language false positives I found: Python `import os` text is already tagged
  `JavaScript` (because `\bimport\s+` fires on it), and TypeScript `: string` annotations trip the Python
  annotation regex. Both are real bugs, but neither is #55 and no test here covers them. I'll mention
  them in the PR for a separate issue instead of fixing them on this branch.
- Any change to confidence scoring, the `SkillDetection` output shape, or the other tools/databases in
  `TOOLS`/`DATABASES` beyond the aliases below.
- Restructuring the extractor (for example, moving to a table of regex rules).

## Files

- `ingestion/parsers/skill_extractor.py` — the fix (four small changes, listed under Approach).
- `tests/unit/test_skill_extractor.py` — delete the five `@pytest.mark.xfail(strict=True, reason="issue
  #55: ...")` markers. No other test edits. (I checked `pyproject.toml`: there's no #55 suppression to
  remove.)

## Approach

All four changes are in `ingestion/parsers/skill_extractor.py`:

1. **JS `require(`** in `_detect_languages`: change the import regex to
   `r"\b(?:import\s+|require\s*\()"`, so `require('fs')` and `require ("fs")` both count.
2. **TypeScript syntax** in `_detect_languages`: add one TS-only signal,
   `r"\b(?:export\s+)?interface\s+\w+\s*\{"`, recorded as evidence `"TypeScript interface declaration"`,
   and set `lang = "TypeScript"` when either the `.ts` filename or this signal is present. I'm using
   `interface X {` rather than `: string` annotations, because a bare annotation pattern is exactly the
   kind of check that already caused the Python mislabel above.
3. **Database driver aliases** in `_detect_databases`: a small class-level map from driver/package name
   to an existing `DATABASES` key: `psycopg2`, `psycopg`, `asyncpg` → `postgresql`; `pymysql` → `mysql`;
   `pymongo` → `mongodb`. A hit adds the same `SkillDetection` the literal name would, with evidence
   naming the driver.
4. **Docker structure** in `_detect_tools`: detect Docker when either
   - the text has a line starting with `FROM <image>` (`r"^\s*FROM\s+\S+"`, multiline) **and** at least
     one more line starting with another Dockerfile directive (`RUN|CMD|EXPOSE|COPY|ADD|ENTRYPOINT|WORKDIR`), or
   - the text has a top-level-style `services:` key **and** a nested `build:` or `image:` key.

   Requiring two signals instead of one is the false-positive guard: SQL `SELECT ... FROM users` has no
   second directive, and a CI YAML with `build:` has no `services:`.

Order of work: (1) and (2) first and run the tests, then (3), then (4), running the test file after each
step, then delete the markers last.

## Test plan

My repro steps re-run, with the outcome each one must show after the fix:

1. Before deleting the markers:
   `.venv/bin/pytest tests/unit/test_skill_extractor.py -v --runxfail` → **18 passed** (was
   `5 failed, 13 passed`).
2. Also before deleting them, a normal run should **fail with `XPASS(strict)`** on all five. That proves
   the markers are what's left between the fix and a green run, not that the tests were changed.
3. After deleting the markers:
   `.venv/bin/pytest tests/unit/test_skill_extractor.py -v -rx` → **18 passed, 0 xfailed** (was
   `13 passed, 5 xfailed`).
4. False-positive guards, by calling `SkillExtractor().extract_skills(...)` directly before and after:
   - `"SELECT name FROM users WHERE id = 1;"` → must stay `[]` (no Docker).
   - a GitHub Actions YAML (`on: push` / `jobs:` / `build:` / `runs-on: ubuntu-latest`) → must stay `[]`.
   - the Python-imports test text → must stay exactly `['Numpy', 'Python', 'JavaScript']`, so I can show
     I didn't change the existing (out-of-scope) behavior in either direction.
5. `make test-unit`, `make lint`, `make typecheck` all pass, since `docs/CONTRIBUTING.md` says CI must be
   green, and the whole unit suite is where an accidental side effect would show up.

## Risks and unknowns

- **Docker false negatives.** A Dockerfile excerpt with only a `FROM` line, or a Compose file that uses
  `image:` under a key that isn't `services:`, won't be detected. I'm choosing to miss those over
  tagging every SQL query as Docker, but that's a judgment call reviewers might disagree with.
- **The `interface` signal isn't TypeScript-only everywhere.** Java and C# also use `interface X {`. In
  this extractor, Java/C# are only detected from filenames, so a Java snippet with no filename would now
  come back as TypeScript. I haven't found a test that covers this, and I'm naming it rather than
  assuming it's fine.
- **The driver alias list is a choice, not a complete list.** I picked the common Python drivers for
  databases already in `DATABASES`. Node drivers (`pg`, `mongoose`) are left out, because `pg` as a bare
  substring would match far too much.
- **Unknown: how `extract_skills` output is used downstream.** Gaining `TypeScript` and `Docker` entries
  may change review output for real portfolios. I haven't traced the callers yet; I'll check
  `ingestion/pipeline.py` before opening the PR.

## Deviations

The fix itself went as planned: same file, same four changes, same order, and the five markers removed. A
few places where the build doesn't match what I wrote above:

1. **Docker detection lives in a small helper.** The two-signal check is a new private static method,
   `_docker_structure_evidence`, which `_detect_tools` calls, instead of being written inline in
   `_detect_tools`. The behavior is the one planned. I split it out so the two-signal rule is in one
   place, with a one-line docstring saying why one signal isn't enough.
2. **Driver aliases match whole words only.** The plan didn't say how a driver name should match. I used
   `\bpsycopg2\b`-style word boundaries, so a driver name inside a longer identifier doesn't count.
3. **Step 5 of the test plan didn't fully run as written.** `make test-unit` (`380 passed, 48 xfailed`) and
   `make lint` passed, and `black --check` leaves both files unchanged. But `make typecheck` fails on my
   machine, and it fails identically on an unmodified checkout of `main` (I stashed my change and re-ran
   it). The error is inside numpy's stubs: `pyproject.toml` pins mypy to `python_version = "3.11"`, but my
   venv is Python 3.14 with numpy 2.5.3, whose stubs use 3.12-only syntax. Instead I ran
   `.venv/bin/mypy --python-version 3.12 ingestion/parsers/skill_extractor.py`, which gives
   `Success: no issues found in 1 source file`. CI on the PR is the real check here.
4. **One unknown is answered.** I listed "how `extract_skills` output is used downstream" as open. Nothing
   outside the tests imports `ingestion.parsers.skill_extractor`. `agent/tools/skill_extractor.py` is a
   different `SkillExtractor` class. So the new `TypeScript`/`Docker` entries don't change any review
   output today.

Results against the test plan (output saved from my runs):

```
before:  pytest tests/unit/test_skill_extractor.py -v -rx        -> 13 passed, 5 xfailed
         pytest tests/unit/test_skill_extractor.py -q --runxfail -> 5 failed, 13 passed
during:  --runxfail -> 18 passed;  normal run -> 5 x [XPASS(strict)], 5 failed, 13 passed
after:   pytest tests/unit/test_skill_extractor.py -v -rx        -> 18 passed
guards (identical before and after):
  sql query                -> []
  github actions yaml      -> []
  python imports test text -> ['Numpy', 'Python', 'JavaScript']
```
