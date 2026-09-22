# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/55

**Verdict output**

````
**Issue #55** — "Skill extractor fails to detect JavaScript and TypeScript" (`codepath/pathreview-ai301-fa26-s1`)

| Check | Grade | Evidence |
|---|---|---|
| Repo alive | pass | Last default-branch commit 2026-09-16 (6 days before today, 2026-09-22) |
| Maintainer responsive | pass | Named human Andrew Burke (Aburke225, non-bot) committed to main on 2026-09-16, 6 days ago |
| Repo in active use | pass | Not archived; last push 2026-09-16, within 365 days |
| Scope fits a newcomer | pass | Single bounded bug in one file (`ingestion/parsers/skill_extractor.py`), concrete repro steps (5 named failing tests), `tier-1`/"Starter difficulty" label |
| Nobody already on it | pass | `assignees: []`, no comments, no linked or mentioned PRs found |
| AI contribution policy allows it | pass | `docs/CONTRIBUTING.md` and PR template have no AI ban or disclosure clause; no dedicated `AI_POLICY.md`; silence passes |
| Good-first-issue label (preferred) | pass | Carries `tier-1` label, described as "Starter difficulty" — clear equivalent |
| Label still fresh (preferred) | pass | `tier-1` label added same second as issue creation (2026-09-10) |
| Fast response in this thread (preferred) | fail | Issue has zero comments — no maintainer reply to grade |

All required checks pass → **accept**.

```json
{
  "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/55",
  "checks": [
    {"name": "Repo alive", "grade": "pass", "evidence": "Newest default-branch commit 2026-09-16T21:42:18Z, 6 days before today (2026-09-22)"},
    {"name": "Maintainer responsive", "grade": "pass", "evidence": "Andrew Burke (Aburke225, human) committed to main on 2026-09-16, within 14 days of today"},
    {"name": "Repo in active use", "grade": "pass", "evidence": "isArchived: false; pushedAt 2026-09-16, within 365 days"},
    {"name": "Scope fits a newcomer", "grade": "pass", "evidence": "Body names one bounded bug in ingestion/parsers/skill_extractor.py with 5 concrete failing pytest cases named as repro steps"},
    {"name": "Nobody already on it", "grade": "pass", "evidence": "assignees: []; comments: []; no linked or mentioned PRs found via search"},
    {"name": "AI contribution policy allows it", "grade": "pass", "evidence": "docs/CONTRIBUTING.md and .github/PULL_REQUEST_TEMPLATE.md contain no AI ban/disclosure clause; no dedicated AI_POLICY.md file exists"},
    {"name": "Good-first-issue label", "grade": "pass", "evidence": "Labeled 'tier-1' with description 'Starter difficulty'"},
    {"name": "Label still fresh", "grade": "pass", "evidence": "'tier-1' labeled at 2026-09-10T21:37:55Z, same second as issue creation"},
    {"name": "Fast response in this thread", "grade": "fail", "evidence": "comments: [] — no maintainer reply exists to grade"}
  ],
  "verdict": "accept"
}
```
````

---

## Eval iterations

**Run history**

Three runs, in order:

1. **Full run, 12/20.** The harness printed `agreement: 12/20 scored items  (bar: 18/20: below the bar)` with
   `categories: claimed 4/4  clear-accept 2/8  dead-repo 3/3  policy 1/1  scope 2/4`. Two categories were the
   problem: six of the eight clear-accepts were rejected, and two of the four scope issues were accepted.
2. **Partial diagnostic run, 0/8.** I re-ran only the eight disagreements with
   `--only issue-01,issue-04,issue-09,issue-14,issue-15,issue-16,issue-19,issue-20` plus `--out`, to read the
   per-check evidence behind each one. It reproduced all eight (`agreement: 0/8 scored items`) and, being partial,
   refused to write `eval-run.txt`. This run diagnosed rather than scored: it showed that every false reject came
   from exactly three required checks (Maintainer responsive, Scope fits a newcomer, Nobody already on it), and
   that both false accepts came from Scope and claim-history blind spots.
3. **Full run, 20/20 — the committed run.** After revising those three checks, the harness printed
   `agreement: 20/20 scored items  (bar: 18/20: PASS)` with
   `categories: claimed 4/4  clear-accept 8/8  dead-repo 3/3  policy 1/1  scope 4/4`. This is the run saved in
   `eval-run.txt`.

**Issue analysis**

`issue-15` (zulip/zulip#19589, category `scope`).

My rubric now grades it **reject**; the gold label is **reject** (`gold-labels.json`: `"verdict": "reject"`, note
`"years of design debate and two abandoned PRs behind a friendly label"`). On my first run my rubric graded it
**accept**, disagreeing with gold.

The reason the first version accepted it is that the bundle passes every claim signal I was checking, one by one.
The repo facts read `this issue: assignees: none; linked PRs: zulip/zulip#20840 (closed); zulip/zulip#23123
(closed)` — no assignee, and no *open* PR. My original pass condition asked that "no claim comment stands
unanswered by a maintainer walking it back," and in this thread every claim had in fact been answered: each
`@zulipbot claim` is followed by `Hello @<user>, you have been unassigned from this issue because you have not
updated this issue or any referenced pull requests for over 14 days.` So each individual claim was closed out, and
the check passed on a technicality.

Read as a whole rather than claim by claim, the thread says the opposite. Inside just the first 40 of its 97
comments, LoganNiswander, leighadennis, blackbird7112, BrianMcDowell, sudhanshu154, Kaustubhkongile, ikrambil,
SamChen41 and souvik150 each claim it and each go quiet, across 2021 to 2024, with two PRs closed and nothing
merged. Nobody holds the issue *right now*, which is what my check measured, but nine people have already bounced
off it, which is the thing a newcomer actually needs to know. I added a clause that fails the check when three or
more distinct contributors have claimed and gone stale with no merged fix, and `issue-15` now rejects for the
reason gold rejects it.

**Check rationale**

From `tools/issue-select/rubric.md`, the `Nobody already on it` row as it is currently written:

> | Nobody already on it | Assignees box; Development box (linked PRs) and PR mentions in the thread; claim comments | no assignee is set and no linked PR is open (an open PR mentioned only in the thread counts too); a single old claim comment or closed linked PR only blocks if its most recent related activity falls within 180 days of now (live) or the capture date (eval) and was not walked back by a maintainer -- an isolated older claim with no follow-up since is stale and doesn't block; but fails regardless of recency if the thread shows three or more distinct contributors claiming and then going stale/unassigned with no merged fix -- a claim graveyard is a red flag even when nobody currently holds it | required |

It has three clauses because the eval set punished this check in two opposite directions at once.

The first clause (assignee, open PR) is the original one and it was never wrong — it is what still rejects all four
`claimed` issues.

The second clause exists because of `issue-09` (conda/conda#7617), gold `accept`, which my first rubric rejected.
One person claimed it on 2022-01-20, a maintainer replied `Think you can just give it a try`, and then nothing —
the linked PR is closed and the last thread activity is a `(bump)` on 2023-01-21, three and a half years before
the 2026-08-05 capture. A claim that old with no follow-up is not a person you are going to collide with, so a
lone stale claim now expires after 180 days.

The third clause is the `issue-15` fix above, and it deliberately cuts the other way: it blocks *regardless* of
recency. The two clauses are not in tension, because they are measuring different things. One old claim is
evidence about one person who wandered off. Nine old claims are evidence about the issue.

**Trade-offs**

The 180-day expiry is the clause that gives something up: it will wave through an issue where one contributor
claimed seven months ago and is still quietly working on it off GitHub, and my rubric would tell a newcomer the
lane is clear when it is not. The eval set has no such case, so nothing in my score paid for it, but it is a real
hole rather than a hypothetical one. The `three or more` threshold in the third clause is likewise a number I
picked rather than derived — a two-person graveyard still reads as clean to this check.

What the clause changed and what it did not:

- **It changed `issue-09`.** Rejected on run 1 against a gold label of `accept`; accepted on run 3. That one
  issue is the whole reason the second clause exists.
- **It changed nothing in the `claimed` category, and here is how I know.** That category scored `claimed 4/4`
  both before and after the edit. It could not have moved: `issue-03` (`#10985 (open)`), `issue-08`
  (`assignees: piyushagarwal-55`, `#39811 (open)`), `issue-13` (`#23438 (open); #23439 (open)`) and `issue-18`
  (`#9685 (open); #10371 (open); #11656 (open)`) are each caught by the *first* clause, which the staleness
  carve-out never touches — the carve-out only ever applies to claim comments and *closed* PRs.

---

## Selection rationale

**Selection rationale**

*1. Fit to my interests and to the time available.* I am a second-year CS student who has written mostly Python
and some JavaScript/TypeScript, and I want to grow toward TypeScript and full-stack work. This issue is Python,
but its subject is exactly the JS/TS ecosystem knowledge I do have — the bug is that `_detect_languages` only
recognizes JavaScript and TypeScript from `.js`/`.ts` filenames and an `import`/`require` pattern, so it misses
the technology described any other way. It is labeled `tier-1` (starter difficulty), it lives in one file
(`ingestion/parsers/skill_extractor.py`), and the five failing tests are already written, which bounds the work
to something I can finish alongside my other coursework rather than an open-ended build.

*2. What the verdict identified correctly, and what I weighed that the rubric could not.* The verdict got the
mechanical checks right: the repo is alive (a human maintainer committed six days ago), the issue is completely
unclaimed (no assignee, no comments, no linked PRs), the scope is one bounded bug with real reproduction steps,
and the contributing docs say nothing about AI either way. What the rubric could not weigh is what comes next in
the course. I ran the skill on three candidates and it accepted all three, ranking #40 ("Copy link" button) first
on fit because it is genuinely full-stack React/TypeScript plus a Python route — which is what I told `scope.md`
I wanted. I chose #55 over my own tool's top pick anyway, because Unit 2 asks me to *reproduce* the issue, and
#40 is a feature to build with nothing to reproduce, at a 5–8 hour estimate. #55 ships an exact reproduction
command (`pytest tests/unit/test_skill_extractor.py -q`) and five named failing tests. My rubric grades whether
an issue is a good first contribution in the abstract; it does not know that my next assignment is a reproduction
step, and that is the judgment I had to make myself.

*3. The anticipated difficulty in claiming it.* Low, mechanically: the issue has zero comments and no assignee
today, and the Path Review house rule in `scope.md` says classmates' claim comments do not block an issue here
anyway, so even if someone else claims it first I am not locked out. The likelier friction is that it is an
attractive unclaimed tier-1 bug, so I expect company on it. The part I expect to actually be work is that the
five tests are currently marked `xfail` rather than simply failing, and the fix spans three helpers
(`_detect_languages`, `_detect_tools`, `_detect_databases`), so "make the tests pass" also means deciding how
broadly to widen the matching without making detection fire on false positives.
