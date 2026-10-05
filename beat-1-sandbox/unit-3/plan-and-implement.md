# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

---

## Posted upstream

**GitHub username**

TeooL

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/55#issuecomment-5998062165

````
Plan for #55, built from my repro above (`--runxfail`: `5 failed, 13 passed` on `f89c06f`).

There are several repros and a plan from other contributors above now. My repro matches theirs, and my plan overlaps with @jing2003's on where the fix goes. Here's mine, with the specific signals I'm choosing and the false-positive checks I'm holding the change to.

**Cause:** three separate gaps in `ingestion/parsers/skill_extractor.py`. The JS regex `\b(import|require)\s+` needs a space after `require`, so `require('fs')` misses. TypeScript is only ever chosen from a `.ts` filename. And `_detect_databases` / `_detect_tools` only match a technology's literal name (`psycopg2` isn't `postgresql`, and a Dockerfile never says `docker`). While planning, I also found that the TypeScript test sample currently comes back as `['Python']`, because the Python annotation regex matches the `str` in `id: string`.

**Change (one file plus the test markers):**
- `require\s*\(` added to the JS regex
- a TypeScript signal from `interface X {` declarations, rather than `: string` annotations (that kind of check is what causes the Python mislabel)
- a small driver-alias map: `psycopg2`/`psycopg`/`asyncpg` → PostgreSQL, `pymysql` → MySQL, `pymongo` → MongoDB
- Docker detection that needs two signals: `FROM <image>` plus another Dockerfile directive, or `services:` plus `build:`/`image:`
- removing the five `xfail(strict=True)` markers, per `docs/CONTRIBUTING.md`

**Not changing:** the existing Python→JavaScript mislabel on `import os` text, and the TS→Python one. Both are real but separate from #55, so I'll note them in the PR instead of fixing them on this branch.

**Test plan:** `pytest tests/unit/test_skill_extractor.py -v --runxfail` goes from `5 failed, 13 passed` to `18 passed`. A normal run shows `XPASS(strict)` until the markers are gone, then `18 passed`. Three guard inputs have to come out the same before and after: a SQL `SELECT ... FROM users` (no Docker), a GitHub Actions YAML with `build:` (nothing detected), and the Python-imports test text (unchanged). Then `make test-unit`, `make lint`, `make typecheck`.

**Open questions I'm flagging rather than assuming:** `interface X {` is also Java/C# syntax, so a Java snippet with no filename would now read as TypeScript. And I haven't traced yet how the new `TypeScript`/`Docker` entries affect downstream review output.
````

---

## Your branch

**Branch**

fix/55-skill-extractor-js-ts-detection

**Evidence**

My Unit 2 reproduction steps, run against `f89c06f` before the change and against commit `f1049db` on the
branch after it. Both runs: Linux (WSL2), Python 3.14.4, pytest 9.1.1. `guards.py` calls
`SkillExtractor().extract_skills(...)` on the three false-positive guard inputs from my plan's test plan
(a SQL query, a GitHub Actions YAML, and the Python-imports test's text).

Before the fix:

```
$ .venv/bin/pytest tests/unit/test_skill_extractor.py -v -rx

=========================== short test summary info ============================
XFAIL tests/unit/test_skill_extractor.py::TestSkillExtractor::test_text_with_typescript_files - issue #55: skill extractor does not detect JavaScript/TypeScript
XFAIL tests/unit/test_skill_extractor.py::TestSkillExtractor::test_database_technology_detection - issue #55: skill extractor does not detect JavaScript/TypeScript
XFAIL tests/unit/test_skill_extractor.py::TestSkillExtractor::test_devops_tool_detection - issue #55: skill extractor does not detect JavaScript/TypeScript
XFAIL tests/unit/test_skill_extractor.py::TestSkillExtractor::test_javascript_detection - issue #55: skill extractor does not detect JavaScript/TypeScript
XFAIL tests/unit/test_skill_extractor.py::TestSkillExtractor::test_docker_compose_detection - issue #55: skill extractor does not detect JavaScript/TypeScript
======================== 13 passed, 5 xfailed in 0.25s =========================

$ .venv/bin/pytest tests/unit/test_skill_extractor.py -q --runxfail
=========================== short test summary info ============================
FAILED tests/unit/test_skill_extractor.py::TestSkillExtractor::test_text_with_typescript_files
FAILED tests/unit/test_skill_extractor.py::TestSkillExtractor::test_database_technology_detection
FAILED tests/unit/test_skill_extractor.py::TestSkillExtractor::test_devops_tool_detection
FAILED tests/unit/test_skill_extractor.py::TestSkillExtractor::test_javascript_detection
FAILED tests/unit/test_skill_extractor.py::TestSkillExtractor::test_docker_compose_detection
5 failed, 13 passed in 0.16s

$ .venv/bin/python guards.py
sql query                    -> []
github actions yaml          -> []
python imports test text     -> ['Numpy', 'Python', 'JavaScript']
```

After the fix (markers removed):

```
$ .venv/bin/pytest tests/unit/test_skill_extractor.py -v -rx

tests/unit/test_skill_extractor.py::TestSkillExtractor::test_text_with_python_imports PASSED [  5%]
tests/unit/test_skill_extractor.py::TestSkillExtractor::test_text_with_python_type_annotations PASSED [ 11%]
tests/unit/test_skill_extractor.py::TestSkillExtractor::test_text_with_typescript_files PASSED [ 16%]
tests/unit/test_skill_extractor.py::TestSkillExtractor::test_jupyter_ipynb_detection PASSED [ 22%]
tests/unit/test_skill_extractor.py::TestSkillExtractor::test_mixed_language_text PASSED [ 27%]
tests/unit/test_skill_extractor.py::TestSkillExtractor::test_frameworks_detected_with_high_confidence PASSED [ 33%]
tests/unit/test_skill_extractor.py::TestSkillExtractor::test_react_detection PASSED [ 38%]
tests/unit/test_skill_extractor.py::TestSkillExtractor::test_database_technology_detection PASSED [ 44%]
tests/unit/test_skill_extractor.py::TestSkillExtractor::test_devops_tool_detection PASSED [ 50%]
tests/unit/test_skill_extractor.py::TestSkillExtractor::test_skill_has_evidence_list PASSED [ 55%]
tests/unit/test_skill_extractor.py::TestSkillExtractor::test_filename_based_detection PASSED [ 61%]
tests/unit/test_skill_extractor.py::TestSkillExtractor::test_javascript_detection PASSED [ 66%]
tests/unit/test_skill_extractor.py::TestSkillExtractor::test_docker_compose_detection PASSED [ 72%]
tests/unit/test_skill_extractor.py::TestSkillExtractor::test_aws_gcp_azure_detection PASSED [ 77%]
tests/unit/test_skill_extractor.py::TestSkillExtractor::test_empty_text PASSED [ 83%]
tests/unit/test_skill_extractor.py::TestSkillExtractor::test_unrecognized_language PASSED [ 88%]
tests/unit/test_skill_extractor.py::TestSkillExtractor::test_confidence_scores_are_floats PASSED [ 94%]
tests/unit/test_skill_extractor.py::TestSkillExtractor::test_skill_detection_dataclass PASSED [100%]

============================== 18 passed in 0.29s ==============================

$ .venv/bin/python guards.py
sql query                    -> []
github actions yaml          -> []
python imports test text     -> ['Numpy', 'Python', 'JavaScript']
```

`--runxfail` is not needed after the fix, because the five `xfail` markers are gone and the tests now run as
ordinary tests. The three guard results are identical before and after.

## Eval iterations

**Run history**

Three runs, in order:

1. **Full run, 19/20.** `agreement: 19/20 scored items  (bar: 18/20: PASS)` with
   `categories: clear-accept 6/7  scope-creep 4/4  thread-convention 2/2  unbuildable 3/3  wrong-cause 4/4`.
   The one disagreement was `pkg-14  clear-accept  accept  reject  NO  failed: A stranger could start
   executing`.
2. **Partial run, `--only pkg-14,pkg-10,pkg-17,pkg-18,pkg-04`, 5/5.** I loosened the executability check,
   then re-ran pkg-14 together with canaries: all three `unbuildable` packages and one `thread-convention`
   package. Result: `agreement: 5/5 scored items`, with pkg-14 now `accept` and all four canaries still
   `reject`. Being partial, it printed no bar verdict.
3. **Confirming full run with `--save-run`, 19/20 — the committed run.** `agreement: 19/20 scored items
   (bar: 18/20: PASS)` with `categories: clear-accept 6/7  scope-creep 4/4  thread-convention 2/2
   unbuildable 3/3  wrong-cause 4/4`. pkg-14 disagreed again, this time on a different check:
   `failed: Diagnosis follows the repro evidence`. This is the run in `eval-run.txt`.

**Package analysis**

`pkg-14` (zellij-org/zellij#5451, category `clear-accept`). The gold label is **accept** (`gold-labels.json`:
"honestly scoped-down: reattach handshake fix with a regression-window repro; defers the untestable Windows
variant and says so; arguable on the deferral, ready as scoped"). My rubric graded it **reject** on both full
runs, for a different reason each time.

On run 1 it failed `A stranger could start executing`. The grader's evidence line quoted the plan's "exact
functions to be pinned in the PR after tracing the query issuance with debug logs". My check at the time said
the plan must name "where the change goes (a file, function, or specific site)". The plan names a code path
("the client attach path in `zellij-server`'s client connection handling") and one approach ("consuming or
draining pending OSC query responses ... before pane input is wired"), but not a function. So the grader read
the deferred function name as the same kind of hedge as pkg-17's "gocui? tcell? not sure". Those two cases
aren't the same: pkg-14 has made its real decision, and pkg-17 hasn't.

On the committed run it passed executability but failed `Diagnosis follows the repro evidence`. On run 1, the
grader had already noted, while passing that check, that "the cache-path fit is asserted rather than directly
evidenced". The plan says "the cache control fits: with an empty cache the color data is refetched along the
fresh-attach path once", which is an explanation it gives, not something the repro shows. My diagnosis check
fails "a cause asserted with no tie to any repro step", and whether that one sentence counts is a judgment
call the grader made differently across runs. The core cause (leak only on reattach, clean on 0.44.1) is tied
to repro steps. Only the explanation of the cache control is not. That's why I think gold's accept is right,
and why I left the diagnosis check strict (see Trade-offs).

**Check rationale**

From `tools/plan-check/rubric.md`, as it reads now:

> | A stranger could start executing | the plan's named files, functions, or code sites, and its chosen approach | the plan commits to one approach and names where it goes, at least down to a specific file or a specific code path within a named component (a file, a function, or "the client attach path in `zellij-server`'s session handling"); pinning the exact function later is fine when the approach and the code path are already fixed; leaving the approach, the layer, or the component undecided ("somewhere", "X or Y, whichever is easier", "gocui? tcell? not sure", "profile and see", "poke around") fails | required |

It reads this way because of pkg-14. My first version passed only if the plan named "a file, function, or
specific site", and failed any plan "leaving the location or the approach open". That failed pkg-14 for
putting off a function name it hadn't pinned down yet. The unbuildable packages fail for a different reason:
they leave the real decision open. pkg-17 can't choose a layer ("gocui? tcell? not sure"). pkg-18 hasn't
picked a location ("recover() 'somewhere'") or an approach ("upstream or vendored, whichever is easier").
pkg-10 has no files and no approach ("profile and optimize"). So the revision draws the line at the
*decision*. A plan passes if it commits to one approach and to a code path inside a named component, even
when the exact function comes later. I quoted pkg-17's own hedge into the fail list, so the grader can see
what the far side of the line looks like. I rejected the alternative of dropping the location requirement
altogether: then a plan like pkg-10, with no files at all, would only fail on its approach, and that's a much
weaker catch.

**Trade-offs**

The loosening was the risky direction, since it could flip an `unbuildable` reject to accept. So before the
confirming run I re-ran canaries with `--only`: all three `unbuildable` packages (pkg-10, pkg-17, pkg-18) and
one `thread-convention` package (pkg-04), which is the 2-package category the eval README flags. All four
still graded `reject` after the change, and the confirming full run kept `unbuildable 3/3` and
`thread-convention 2/2`. That's how I know the change didn't move anything else.

What it gives up: a plan that names only a subsystem path, defers the function, and never actually reaches a
real fix site would now pass this check. The check trusts that "the client attach path" is specific enough to
start from. And the change didn't fix pkg-14. On the confirming run it failed on diagnosis instead. I chose not
to loosen the diagnosis check to chase it. That check is what catches all four `wrong-cause` packages, and
those plans are confident and polished (pkg-16 is "long and confident") and fail only because one control step
rules them out. Loosening "a cause asserted with no tie to any repro step" for pkg-14's one unproven sentence
risked letting those through, and losing one arguable clear-accept costs less than that. pkg-14 stays a known
miss.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
