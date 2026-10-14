---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

Answer one question about one PR package: **is this ready to submit?**
A PR package is a candidate pull request (its title, description,
commits, diff, and test evidence), read against the plan it claims to
implement (deviation notes included) and the issue that plan belongs
to. Do not answer any other question: do not review code style for its
own sake, suggest a better fix, or grade more than one package per
run. Answer by executing the components in this directory, never from
gut feel.

## Inputs and modes

Run in exactly one of two modes.

**Live mode** grades the student's own PR before it is opened. Read
these inputs, and only these:

1. The plan: the `plan.md` the request names, including its
   `## Deviations` section.
2. The diff: every committed change the branch makes against the
   repo's default branch. Produce it by running `git diff main...HEAD`
   (three dots) from the working copy, plus `git log --oneline
   main..HEAD` for the commit list. Uncommitted changes are not part of
   the PR; do not read them.
3. The PR title and description: the draft file the request names
   (`pr_draft.md`; the title is its first line).
4. The test evidence: the file the request names (`test_evidence.md`).
5. The issue: its body and comment thread, fetched with `gh issue view
   <number> --repo <repo> --comments`.
6. The repo's stated asks: `.github/PULL_REQUEST_TEMPLATE.md` and
   `docs/CONTRIBUTING.md` from the working copy (or `gh api` if they are
   not there), plus any AI-use policy file.

A house-chain student reads the house plan and the house repro pack in
place of their own plan and repro; the same checks grade the same
things. If any named input file is missing, say which one in the
summary and grade the checks that needed it as `unclear`.

**Eval mode** grades a package bundle. The bundle is the whole world:
take every fact from its text (Repo facts, Issue, Thread highlights,
Plan context, and the Candidate PR's Title, Description, Commits, Diff,
and Test evidence). Fetch nothing and read nothing else. Eval mode
always grades a complete package: every check, the full verdict rule.

## The scope seam (live mode only)

In live mode, read `scope.md` before anything else. If its `Repo:`
line still holds a bracketed placeholder, stop without grading and tell
the student to fill the `Repo:` line in `scope.md` with their section's
Path Review repo. If the issue or the PR target is in any other
repository, refuse to grade and say why. Apply the house rules it
states as part of reading the evidence: in particular, the PR template
is always required with every section filled, and the branch must be a
`<type>/<issue-number>-<slug>` branch on the student's fork. Never
guess a scope. In eval mode, ignore `scope.md` entirely.

## The voice seam (live mode only)

In live mode, read `voice-guide.md` after the scope. Hold the outgoing
text, the PR title and the description, against each of its rules and
its "Things I never post" list. For every rule the draft breaks, quote
the rule and the offending line in the summary. The voice guide never
changes the verdict on its own, because no check in `rubric.md` reads
it: it is the student's conscience, reported out loud. In eval mode,
ignore `voice-guide.md` entirely.

## Component reads

Read `rubric.md` for the checks and the verdict rule: it decides what
is graded and how grades combine. Read `references/evidence-guide.md`
for where each evidence family lives, in a bundle and in a live
package. Then execute `procedure.md` step by step, exactly as written:
it decides the read order, how evidence is gathered, how each check
runs, and how the verdict is assembled. Where the procedure is silent
on something you need, say so in the summary as a procedure gap and
do the narrowest thing the rubric's wording supports; never invent a
step and present it as the procedure's.

If `rubric.md` has no checks of its own (only the template's comments),
or `procedure.md` has no steps of its own, refuse to grade: say which
file is empty and stop. An empty tool that invents checks at runtime
produces verdicts that look like judgment and are noise.

## Verdict and output

The verdict is binary: `accept` means ready to submit, `reject` means
hold. There is no third verdict and no score; put reservations in
check evidence lines, not in the verdict.

You may print a short readable summary first (one line per check, plus
any voice-guide notes in live mode). Then end your reply with this
fenced JSON block, valid, with every check from `rubric.md` in table
order, and nothing after it:

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

- Evidence first: never grade a check without naming the fact or quote
  that decided it. "Looks fine" is not evidence.
- Grade the thing, not the polish: read the diff itself against the
  plan, the evidence itself against the test plan, and the repo's
  stated asks themselves. A terse PR can be ready; a confident,
  well-formatted description can be hiding drift.
- Trust the diff over the description: when the description and the
  diff disagree, the diff is what the PR does.
- The rubric decides: if a check passes by its stated condition but
  feels wrong, it passes; note the tension in the summary.
- The procedure decides how: follow `procedure.md` as written and
  report its gaps instead of filling them silently.
- Unclear counts as fail: treat `unclear` as `rubric.md`'s verdict rule
  directs, and where it is silent, an unverifiable claim is a failing
  one. A PR you cannot verify from the package is not ready to submit.
- An honestly disclosed shortfall is not a failure: a PR that does less
  than the plan and says so in the plan's deviation notes or the
  description can be ready.
