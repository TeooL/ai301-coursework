# Rubric: is this a good first issue?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever checks
you define here. It ships empty on purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where. Name the source
     (repo-facts block, issue body, comment thread, or the locations in
     references/evidence-guide.md). "The repo" is not a source; "the last
     5 default-branch commit dates" is.
   - Pass condition: a condition someone else could apply and get your
     answer. Prefer thresholds with numbers ("a maintainer commented
     within 30 days") over adjectives ("maintainer is responsive").
   - Weight: `required` (a fail here rejects the issue) or `preferred`
     (never changes the verdict; a nice-to-have that helps rank the
     issues you accept).

2. A verdict rule below the table: how the check grades combine into
   accept or reject, including how `unclear` is treated. The verdict
   space is binary. If you write no rule for `unclear`, the skill treats
   it as fail.

Cover what actually kills first contributions. The lecture named four
families: the maintainer is alive, the repo is in use, the scope fits a
newcomer, and nobody else is already on it. A rubric that ignores a family
will fail eval issues designed around that family.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Repo alive | most recent default-branch commit date (repo front page / "last 5 default-branch commits" in repo facts) | the newest commit is within 180 days of today (live) or the capture date (eval) | required |
| Maintainer responsive | badges (Owner/Member/Collaborator) on comments in the issue thread, or the "maintainer first-response sample" in repo facts; also the last 5 default-branch commits (author, date) | pass if a maintainer-badged reply appears anywhere in the sampled evidence at any elapsed time, OR a named human (non-bot) appears in the last 5 default-branch commits within 14 days of today (live) or the capture date (eval); fail only if neither holds -- a thin or slow issue-comment sample does not fail this check on its own when recent human commit activity shows the project is actively maintained | required |
| Repo in active use | archived banner/flag; last push date; latest release date (repo front page sidebar / repo facts) | repo is not archived, AND either the last push or the latest release is within 365 days | required |
| Scope fits a newcomer | issue body and full comment thread | the issue names one clear deliverable (a checklist of concrete parts of the same feature/bug/doc gap still counts as one, and does not fail this on its own); fails if the issue is a self-described tracking/meta/"megaissue", spans multiple unrelated areas of the codebase with no single unifying deliverable, is a pure usage/support question, or a maintainer has stated the fix needs core-internals changes or that the design is still unsettled. For a new-feature request specifically (not a bug fix or a doc/content gap): also fails unless a maintainer or collaborator filed or endorsed it, or the request is fully self-contained with no unresolved TBD/undecided design point left open | required |
| Nobody already on it | Assignees box; Development box (linked PRs) and PR mentions in the thread; claim comments | no assignee is set and no linked PR is open (an open PR mentioned only in the thread counts too); a single old claim comment or closed linked PR only blocks if its most recent related activity falls within 180 days of now (live) or the capture date (eval) and was not walked back by a maintainer -- an isolated older claim with no follow-up since is stale and doesn't block; but fails regardless of recency if the thread shows three or more distinct contributors claiming and then going stale/unassigned with no merged fix -- a claim graveyard is a red flag even when nobody currently holds it | required |
| AI contribution policy allows it | `CONTRIBUTING.md`, `.github/`, any dedicated `AI_POLICY.md`/`AI_USAGE_POLICY.md`, PR/issue templates ("contribution policy" line in repo facts for eval mode) | no outright ban on AI-generated/AI-assisted contributions is stated; disclosure/testing/human-review conditions are fine and do not fail this check; silence passes | required |
| Good-first-issue label | issue labels | issue carries a "good first issue" label or clear equivalent | preferred |
| Label still fresh | label-added event date vs. issue open date (grey event lines in the thread, or issue open date vs. capture date in eval mode) | the good-first-issue label was added within 60 days of now (live) or the capture date (eval) | preferred |
| Fast response in this thread | author_association of the earliest maintainer-badged comment on this specific issue | a maintainer replied within 14 days of the issue being opened | preferred |

## Verdict rule

<!-- State how the grades above combine into accept or reject, and how
unclear is treated. Example shape (write your own): "accept if every
required check passes; preferred checks never change the verdict, they
rank accepted issues; unclear counts as fail." -->
Accept if every required check passes; preferred checks don't change the verdict, they determine order of accepted issues, unclear counts as fail.
