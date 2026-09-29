# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

---

## Your identity upstream

**GitHub username**

TeooL

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/55#issuecomment-5882215397

```
Hi! I'm a student working through this issue as part of a course project — this is my first open-source contribution. I'd like to take this one.

Plan: reproduce the JavaScript/TypeScript detection gap in `_detect_languages` (and the related `_detect_tools`/`_detect_databases` matching) by running the five tests named above — `test_javascript_detection`, `test_text_with_typescript_files`, `test_devops_tool_detection`, `test_docker_compose_detection`, `test_database_technology_detection` — in my own environment, then look at what a fix should widen without introducing false positives.

I'll follow up here with my environment, exact steps, and what I observed.
```

**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/55#issuecomment-5882270618

````
**Environment**

- OS: Linux (WSL2), kernel `6.6.114.1-microsoft-standard-WSL2`
- Python: 3.14.4
- pytest: 9.1.1
- Repo commit: `f89c06f` (fork of `codepath/pathreview-ai301-fa26-s1`, branch `fix/55-skill-extractor-js-ts-detection`)
- Setup: `python3 -m venv .venv && .venv/bin/pip install -e ".[dev]"` — the unit suite doesn't need the Docker/Postgres/Redis stack from `docs/SETUP.md`; `tests/unit/test_skill_extractor.py` has no DB fixtures.

**Steps**

```bash
python3 -m venv .venv
.venv/bin/pip install -e ".[dev]"
.venv/bin/pytest tests/unit/test_skill_extractor.py -v -rx
```

**Observed**

```
13 passed, 5 xfailed in 0.53s
```

The 5 marked `xfail`, all tagged `issue #55: skill extractor does not detect JavaScript/TypeScript`:
`test_text_with_typescript_files`, `test_database_technology_detection`, `test_devops_tool_detection`, `test_javascript_detection`, `test_docker_compose_detection`.

Re-running with `--runxfail` to see the assertions underneath:

```bash
.venv/bin/pytest tests/unit/test_skill_extractor.py -v --runxfail
```

```
5 failed, 13 passed in 0.16s
```

This matches the issue. Reading `ingestion/parsers/skill_extractor.py` against each failure:

1. **`test_javascript_detection`** — the JS/TS regex is `r"\b(import|require)\s+"`, which needs whitespace right after the keyword. The test's `require('fs')` has no space before `(`, so it never matches, and no `filename` is passed to trigger the extension check either.
2. **`test_text_with_typescript_files`** — the sample (`export interface User {...}`, `export class UserService {...}`) is TypeScript-specific syntax with neither `import`/`require` nor a filename, so nothing in `_detect_languages` fires for it at all.
3. **`test_database_technology_detection`** — the text imports `psycopg2`, but `DATABASES` only has the literal key `"postgresql"`, and `_detect_databases` checks `if db in text_lower` — `"postgresql"` is not a substring of `"psycopg2"`, so it's a miss.
4. **`test_devops_tool_detection`** and **`test_docker_compose_detection`** — Dockerfile syntax (`FROM`/`RUN`/`EXPOSE`) and docker-compose YAML (`services:`/`build:`/`ports:`) never contain the literal word `"docker"`, and `_detect_tools` only checks `if tool in text_lower` against `TOOLS = {"docker": 0.95, ...}`.

All five trace back to the same shape the issue names: `_detect_languages` only recognizes JS/TS via a filename extension or a literal `import`/`require` token followed by whitespace, and `_detect_tools`/`_detect_databases` only match when the technology's own name string appears verbatim in the text.

**What I haven't checked yet:** whether widening these patterns (matching `require(` with no space, or Dockerfile/compose structural keywords) risks false positives elsewhere in the corpus this feeds. That's the open question before I propose a fix.
````

---

## Eval iterations

**Run history**

Two runs, in order:

1. **Full run, 20/20.** `agreement: 20/20 scored items  (bar: 18/20: PASS)`, with
   `categories: clear-accept 8/8  disclosure 1/1  no-evidence 4/4  unfollowable-comms 3/3  wrong-target 4/4`.
   This was the first full run against the drafted rubric and evidence guide — I built the checks by working
   backward from the gold-labels' five stated categories (`clear-accept`, `no-evidence`, `wrong-target`,
   `unfollowable-comms`, `disclosure`) before running anything, so each required check targeted one category's
   defining signal.
2. **Confirming full run (with `--save-run`), 19/20 — the committed run.** `agreement: 19/20 scored items
   (bar: 18/20: PASS)`, with `categories: clear-accept 7/8  disclosure 1/1  no-evidence 4/4  unfollowable-comms
   3/3  wrong-target 4/4`. One clear-accept package (`pkg-05`) flipped between the two runs on the exact same
   rubric and evidence guide — model-to-model grading variance, not a rubric edit. This is the run saved in
   `eval-run.txt`.

**Package analysis**

`pkg-05` (conda/conda#16543, category `clear-accept`).

The gold label is **accept** (`gold-labels.json` note: "minimal env.yml repro with a `json.tool` parse failure as
the artifact; conda's stated generative-AI policy is permissive-with-responsibility, no disclosure requirement").
On the confirming run — the one saved in `eval-run.txt` — my rubric graded it **reject**, failing on `Steps
followable`. On the first run, with the identical rubric text, it graded **accept**. This is the one disagreement
in the committed run, so it's the honest one to explain.

The candidate report's steps read: "wrote a minimal `env.yml` containing a valid `dependencies:` list plus a
`category:` section (the section conda does not recognize), then: `conda env update --quiet --json -f env.yml
2>/dev/null`." The report describes the input file in prose — what it contains and why — rather than pasting its
literal YAML. My rubric's `Steps followable` check reads: "a stranger holding only the stated environment and
public, shareable inputs could re-run the exact sequence... steps that name only a general setup instead of the
specific action that triggers the bug fail this." On the confirming run, the grader apparently read "a valid
`dependencies:` list plus a `category:` section" as a description of the input rather than the input itself — not
literally re-runnable without reconstructing the YAML — and failed the check on that basis.

I think gold has the better read. The `category:` section is the entire trigger (conda rejects that section name
specifically), and the report says so plainly enough that reconstructing a two-line `env.yml` is not a real
obstacle for a stranger — the artifact (the `EnvironmentSectionNotValid` warning landing on stdout, then breaking
`json.tool`) is the part doing the actual proving, and it's shown verbatim. My check's wording asks whether the
steps are "the exact sequence," which is stricter than the substance the category is actually built to test
(whether a stranger could get to the same trigger, not whether every input byte was pasted). That gap between my
check's literal wording and its intent is what let one run read this package's plain-English input description as
a failure.

**Check rationale**

From `tools/repro-check/rubric.md`, the `Steps followable` row as it is currently written:

> | Steps followable | the repro report's steps section | a stranger holding only the stated environment and public, shareable inputs could re-run the exact sequence to reach the trigger; steps that depend on a private or unshared resource (an internal repo, an unshared config, credentials), or that name only a general setup ("configure the project") instead of the specific action that triggers the bug, fail this | required |

I wrote it this way because the `unfollowable-comms` category has two genuinely different failure shapes in the
gold set, and I wanted one check that catches both rather than splitting it into two checks that would mostly
duplicate each other. `pkg-18` fails because its repro "lives entirely in a private monorepo with an unshared
config" — a stranger has no way to obtain the input at all, no matter how well it's described. `pkg-06` and
`calib-04` fail because the steps name a stage ("configure the driver," implicitly) without the specific action
that actually triggers the bug, even though nothing is private. I rejected writing a separate "no private
resources" check and a separate "no vague stage-names" check, because every package in the eval set that fails
one of these also reads as failing the general "a stranger could re-run this" test, so a single check stated at
that level of intent covers both failure shapes without adding a row that would agree with this one on every
package it's ever graded.

**Trade-offs**

`pkg-05` is the case this check's wording gives up, and I know it because it's the one disagreement in the
run I committed: a report that fully names its trigger in prose, without pasting the triggering file verbatim,
can read as "not the exact sequence" under this check's literal wording even when a stranger could trivially
reconstruct the input from what's written. The check is written to catch reports that hide or omit the trigger;
it occasionally also catches a report that names the trigger clearly but doesn't attach it as a literal file. I
did not loosen the check to carve out this case, because the same looseness would also have to spare genuinely
vague steps ("set up a matching environment") that only look specific by comparison — the eval set doesn't hand
me a clean line between "described precisely enough" and "not attached verbatim," and case-by-case judgment is
exactly what a single-run grader is inconsistent at, which is what happened here between my two runs. I left it
as a known miss rather than chase one package's wording, since the run still clears the bar (19/20, category
floor intact at `clear-accept 7/8`) and a rewrite risked trading this false reject for a false accept somewhere
in `unfollowable-comms` instead.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.
