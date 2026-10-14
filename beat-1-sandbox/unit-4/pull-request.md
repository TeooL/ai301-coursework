# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s1/pull/192

**Branch**

fix/55-skill-extractor-js-ts-detection

## Eval iterations

**Run history**

Three runs, in order:

1. **Full run, 19/20.** `agreement: 19/20 scored items  (bar: 18/20: PASS)` with
   `categories: clear-accept 6/7  not-tested 4/4  silent-drift 4/4  standards-wall 2/2  unreviewable 3/3`.
   The one disagreement: `pkg-05  clear-accept  accept  reject  NO  failed: Evidence is decisive`.
2. **Partial run, `--only pkg-05,pkg-04,pkg-07,pkg-10,pkg-14,calib-04 --include-calibration`, 5/5.** After
   loosening the evidence check, I re-ran pkg-05 with all four `not-tested` packages and the borderline
   calibration package as canaries. Result: `agreement: 5/5 scored items`, `categories: clear-accept 1/1
   not-tested 4/4`, and `calib-04  calib  reject  reject  yes`. Being partial, it printed no bar verdict.
3. **Confirming full run with `--save-run`, 20/20 — the committed run.** `agreement: 20/20 scored items
   (bar: 18/20: PASS)` with `categories: clear-accept 7/7  not-tested 4/4  silent-drift 4/4  standards-wall
   2/2  unreviewable 3/3`. This is the run in `eval-run.txt`.

**Package analysis**

`pkg-05` (a nushell keybinding-merge PR, category `clear-accept`). The gold label is **accept**
(`gold-labels.json`:
"merge identity widened to (name, mode, modifier, keycode) with a replace warning; engages fdncred's uniqueness question explicitly; repro table before/after shown; template sections filled"). My first rubric graded it **reject** on `Evidence is decisive`. The current rubric grades it
**accept**, on both the canary run and the committed run.

The plan's test plan has two items: "re-run the issue's script, expect both `atuin` rows" and "re-run with
two same-name same-key bindings, expect one row plus a warning". The PR's test evidence shows the first one
fully: a before table with one row (`atuin | none | up`) and an after table with two
(`atuin | control | char_r` and `atuin | none | up`). The second it only states: "Same-key redefine prints
the one-time warning." My first version of the check asked for "an observable before and after for every
failure mode the test plan names", and the grader read the warning item as a failure mode with no before,
so it failed the check with: "Test plan item 'two same-name same-key bindings, expect one row plus a
warning' has no shown before/after".

That reading was wrong in a way my rubric caused. The warning isn't the bug. It's new behavior the fix
adds, so there is no "before" to show; before the fix, the warning didn't exist. The bug's failure mode
(the second `atuin` binding disappearing) is exactly what the before/after table proves. My check made no
distinction between "re-run the bug" items and "check the new thing works" items in a test plan, so a
complete PR failed for not showing a before that can't exist.

**Check rationale**

From `tools/pr-precheck/rubric.md`, as it reads now:

> | Evidence is decisive | the test evidence, read against the plan's test plan and the repro evidence's failing steps; and the repo's own test command and its outcome | the evidence re-runs the plan's repro on the failing path (not only a control or an unchanged path) and shows an observable before and after for every failure mode of the reported bug that the issue or the repro evidence shows, unless one is disclosed as deferred; test-plan items that check new behavior the fix adds (a warning, an error message, a guard) or a control need only their observed outcome stated, since they have no meaningful before; and it names the repo's test command it ran with the outcome; fails on "tested locally", "works on my machine", "tests pass" with nothing named, evidence that exercises only a path the fix does not touch, or a named failure mode never re-run | required |

It reads this way because of pkg-05. The first version required a before and after "for every failure mode
the test plan names". The fix narrows that requirement to failure modes of the reported bug: ones "that the
issue or the repro evidence shows". It also adds the clause saying test-plan items checking "new behavior
the fix adds (a warning, an error message, a guard) or a control need only their observed outcome stated,
since they have no meaningful before". The procedure got matching steps: tag each test-plan item as a *bug
failure mode* or *new behavior/control* before grading.

What I kept strict is what the `not-tested` category is built around. The evidence must re-run "the failing
path (not only a control or an unchanged path)", which is what catches pkg-07 (only the single-file control,
"the path that never breaks") and pkg-14 (a GET with header casing, "the unchanged path"). And "tested
locally", "works on my machine", and "tests pass" with nothing named still fail, which catches pkg-04 and
pkg-10. I rejected the simpler fix of dropping "every failure mode" altogether. calib-04 shows why: its plan
names two failure modes of the bug, its evidence re-runs only one, and gold rejects it. Requiring every
*bug* failure mode keeps that case a reject.

**Trade-offs**

Loosening this check could have flipped a `not-tested` reject to accept, so before the confirming run I
re-ran canaries with `--only`: all four `not-tested` packages (pkg-04, pkg-07, pkg-10, pkg-14), plus
calib-04, the borderline worksheet package whose second failure mode was never re-run. All five still
graded `reject`, and the confirming full run kept `not-tested 4/4`. That's how I know the change didn't
move anything it shouldn't have.

What it gives up: the check now depends on the grader sorting each test-plan item correctly into "bug
failure mode" or "new behavior". A PR whose plan describes a real failure mode in new-feature language
("expect a clear error instead of a crash") could have that item read as new behavior, pass on a stated
outcome, and never show the crash before the fix. The eval set doesn't contain that case, so my score
doesn't show the risk. It's the case I accept this check may miss.
