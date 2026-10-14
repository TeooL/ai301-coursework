# Rubric: is this pull request ready to submit?

<!--
THIS IS THE PART YOU WRITE (fourth week running; this is the rubric's
final form in the sandbox). Your frame in SKILL.md executes whatever
checks you define here, via your procedure.md. It ships empty on
purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four
   columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where in the package. Name
     the part (the diff read against the plan's scope, the test
     evidence read against the plan's test plan, the description read
     against the diff, the repo-facts block's template asks) or a
     location from your references/evidence-guide.md. "The PR" is not
     a source; "the diff's changed files read against the plan's
     stated boundary" is.
   - Pass condition: a decision rule about the OUTCOME that someone
     else could apply and get your answer. Judge the thing itself
     (does the diff fall inside the plan plus its deviation notes? is
     the claimed evidence observable?), never the write-up's shape
     (how long the description is, how many commits there are).
     Structure-shaped checks are what make graders disagree with
     themselves.
   - Weight: `required` (a fail here holds the package) or `preferred`
     (never changes the verdict).

2. A verdict rule below the table: how the check grades combine into
   accept (submit) or reject (hold), including how `unclear` is
   treated. The verdict space is binary. State the `unclear`
   treatment explicitly: the frame here is YOUR SKILL.md, so a rubric
   that stays silent is only covered if your frame's grading
   discipline says what happens (the contract's own default is that
   an unverifiable claim fails).

Cover what actually gets bad PRs submitted. The failure families the
lecture named ARE the harness's scoring categories, same names as the
eval README: silent drift (the diff silently does more or less than
the posted plan, or the description claims fidelity the diff
contradicts), not tested (the evidence proves nothing observable, or
the repo's own checks were never run), unreviewable (debris or
unrelated hunks bury the change), and standards wall (the repo's
stated template and disclosure asks are ignored). Your evidence
guide's four headings map onto these one to one (plan fidelity =
silent drift, test evidence = not tested, diff quality =
unreviewable, standards and comms = standards wall), and the category
floor is scored on exactly these names plus clear accept. A rubric
that ignores a category will fail the eval packages built around
that category. And remember the honest-outcome
rule, fourth week running: a PR that honestly discloses a shortfall
can be ready; a rubric that equates "less than everything" with
"hold" fails the set.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Diff matches the plan | every file and hunk in the diff, read against the plan's scope, approach, file list, and deviation notes; and the description's fidelity claims ("exactly as planned", "no changes outside X", "docs now document Y"), read against the diff | every hunk implements something the plan or its deviation notes name, and every change the plan promises is in the diff or is disclosed as deferred (in the plan's deviation notes or the description); fails if the diff adds unannounced scope (a new option/flag/setting, a rewrite or rename pass, a change to a file the plan never touches), drops a promised change with no note, or the description claims fidelity or a delivery the diff contradicts; a shortfall the plan's notes or the description state openly passes | required |
| Evidence is decisive | the test evidence, read against the plan's test plan and the repro evidence's failing steps; and the repo's own test command and its outcome | the evidence re-runs the plan's repro on the failing path (not only a control or an unchanged path) and shows an observable before and after for every failure mode of the reported bug that the issue or the repro evidence shows, unless one is disclosed as deferred; test-plan items that check new behavior the fix adds (a warning, an error message, a guard) or a control need only their observed outcome stated, since they have no meaningful before; and it names the repo's test command it ran with the outcome; fails on "tested locally", "works on my machine", "tests pass" with nothing named, evidence that exercises only a path the fix does not touch, or a named failure mode never re-run | required |
| Diff is free of debris | the diff's hunks and the commit list | every hunk is the fix, its test, or a change the plan or its deviation notes name; fails if the diff carries debug output (prints, eprintln, console.log, DEBUG logging), commented-out code or abandoned first attempts, dead functions or `allow(dead_code)`/unused stubs, or formatting, import-order, or re-indent churn on lines the fix does not change; comments that explain the fix are not debris | required |
| Repo's stated asks are met | the repo's stated PR template, contributing asks, and AI-use policy (repo-facts block; live: `.github/PULL_REQUEST_TEMPLATE.md`, `docs/CONTRIBUTING.md`, scope.md house rules), read against the title, description, and diff; and explicit maintainer direction in the thread, read against the description | every item the repo states as required is present with real content: the template's sections and checklist, the issue reference it asks for (such as `Closes #N`), and any file it requires the diff to touch (such as a changelog or whatsnew entry); if the stated policy requires AI-use disclosure, the description discloses it (treat every package as AI-assisted work); explicit maintainer direction in the thread is engaged; fails if a stated ask is visibly skipped or a required disclosure is missing; asks the repo never states are not required | required |
| Commits describe the change | the commit list | commit messages say what changed rather than "wip", "fix", or "misc cleanups" | preferred |

## Verdict rule

Accept (ready to submit) only if every required check passes. A required
check graded fail holds the package. A required check graded unclear also
holds it: unclear counts as fail, because a PR that cannot be verified from
the package is not ready to submit. Preferred checks are reported but never
change the verdict. Doing less than the plan is not by itself a fail: a
shortfall disclosed in the plan's deviation notes or the description passes
every check it touches.
