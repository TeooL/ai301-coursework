# Evidence guide: where evidence lives in a PR package

<!--
THIS IS THE PART YOU WRITE (third week running: the map stays in your
hands). Your tool uses this guide as its map: for every kind of
evidence a rubric check names, this file says WHERE to find it in a PR
package and WHAT GOOD LOOKS LIKE when you do.

The four families below are the harness's failure categories under
the names the eval README uses: plan fidelity = silent-drift, test
evidence = not-tested, diff quality = unreviewable, standards and
comms = standards-wall. A package that fails none of them is a
clear-accept. Under each family heading below, write:

- Where it lives: the exact places to look. In an eval bundle (which
  section of the package: the plan-context block's scope pair and test
  plan, the candidate PR's diff, commits, description, or
  test-evidence section, the repo-facts block's template asks and
  stated policy). In live mode (where in your working copy and on
  GitHub: your plan.md and its deviation notes, your branch's diff,
  your draft title and description, your captured test output, the
  repo's PR template and CONTRIBUTING.md).
- What good looks like: one or two sentences someone else could apply.
  Prefer observable conditions ("every changed file falls inside the
  plan's stated boundary or a deviation note") over adjectives ("the
  diff is clean").

A rubric check whose evidence this guide cannot locate is a check
nobody else can execute, and this week that cuts three ways: your
procedure says WHEN to gather each family, this guide says WHERE, and
your SKILL.md says the tool reads both. Write the map you wish your
executor had.
-->

## Plan fidelity (harness category: silent-drift)

Where it lives: in a bundle, the plan is in **Plan context** (its scope,
files, approach, known limits, and any deviation note) and the change is
in the Candidate PR's **Diff**. The fidelity claims are in the
**Description**. In my own PR: `plan.md` (including `## Deviations`),
the output of `git diff main...HEAD`, and the description in
`pr_draft.md`.

What good looks like: every hunk in the diff traces to a change the plan
names or a deviation note explains, and every change the plan promises
is either in the diff or openly deferred. Silent drift goes both ways.
*More than the plan*: a new option or flag, a rename or rewrite pass, or
a file the plan never names (a second hunk in an untouched file is the
classic tell). *Less than the plan*: a promised piece (a docs update, a
second fix) missing with no note. A description that says "exactly as
planned" or "no other changes" over a diff that contradicts it is drift
too, and the diff is what counts. A shortfall the plan's notes or the
description state openly is honest, not drift.

## Test evidence (harness category: not-tested)

Where it lives: in a bundle, the Candidate PR's **Test evidence**, read
against the test plan in **Plan context** and the repro evidence quoted
there. In my own PR: `test_evidence.md`, read against the test plan in
`plan.md`, and the Testing section of `pr_draft.md`.

What good looks like: the plan's own repro steps re-run on the failing
path the issue describes, with the before and the after both shown (an
output, an exit code, a visible state), for every failure mode of the
reported bug. A test-plan item that checks something the fix adds (a new
warning or error message) has no meaningful before, so its observed
outcome is enough. Plus the repo's own test command and its outcome. Weak
evidence looks like "tested locally", "works on my machine", "cargo test
passes" with nothing shown, a run of the control or the path the fix
never touched, or a test plan with two failure modes and evidence for
only one.

## Diff quality (harness category: unreviewable)

Where it lives: in a bundle, the Candidate PR's **Diff** and
**Commits**. In my own PR: `git diff main...HEAD` and `git log --oneline
main..HEAD`.

What good looks like: a reviewer sees the fix and its test and nothing
else. Debris looks like debug output left in (`print`, `eprintln!`,
`console.log`, a DEBUG log line), commented-out code or a commented-out
first attempt, a dead function kept behind `allow(dead_code)`, a TODO on
an unrelated line, import reordering, re-indented or re-printed lines
whose content didn't change, or a "while I was here" edit. Comments that
explain why the fix works are fine. Commit messages like "wip", "fix",
or "misc cleanups while debugging" are a tell that debris rode along.

## Standards and comms (harness category: standards-wall)

Where it lives: in a bundle, the asks are in **Repo facts** (the PR
template's required sections or checklist, contributing asks like a
changelog entry, and the contribution or AI policy), and the
maintainers' direction is in **Thread highlights**. The PR honors them
in the **Description**, and in the **Diff** for required files. In my own
PR: Path Review's `.github/PULL_REQUEST_TEMPLATE.md` (Summary, Issue with
`Closes #`, Changes, Testing checklist, Screenshots / Demo, Notes for
Reviewers), `docs/CONTRIBUTING.md` (branch naming, Conventional Commits,
removing the seeded bug's `xfail` markers), `scope.md`'s house rules,
the course's AI-use disclosure in Notes for Reviewers, and the issue
thread.

What good looks like: every section the template asks for has real
content, every required checklist item is answered honestly, the issue
reference is in the form the repo asks for, and any file the repo
requires (a changelog or whatsnew entry) is in the diff. If the stated
policy requires AI-use disclosure, the description names the tool and
how it was used. Visibly ignored asks are a missing `Closes #`, a
checklist deleted, a required changelog absent from the diff, or no
disclosure where the policy requires one. Asks the repo never states
are not required.
