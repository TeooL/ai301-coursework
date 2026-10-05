# Rubric: is this plan ready to post and build from?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever
checks you define here (via your procedure.md). It ships empty on
purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four
   columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where in the package. Name
     the part (the plan's scope statement, the test plan read against
     the repro evidence's steps, the plan comment read against the
     thread highlights, the repo-facts block) or a location from your
     references/evidence-guide.md. "The plan" is not a source; "the
     plan's stated cause read against what the repro evidence shows"
     is.
   - Pass condition: a decision rule about the OUTCOME that someone
     else could apply and get your answer. Judge the thing itself (is
     this one bounded change? could a stranger start executing it?),
     never the write-up's shape (how many sections it has, how long it
     is, whether it uses headings). Structure-shaped checks are what
     make graders disagree with themselves.
   - Weight: `required` (a fail here holds the package) or `preferred`
     (never changes the verdict).

2. A verdict rule below the table: how the check grades combine into
   accept (ready) or reject (hold), including how `unclear` is
   treated. The verdict space is binary. If you write no rule for
   `unclear`, the skill treats it as fail.

Cover what actually gets bad plans posted. The lecture named the
failure families: the diagnosis ignores or contradicts the reproduced
evidence, the change is unbounded (scope creep), the plan targets the
symptom while the evidence points at the cause, a stranger could not
start executing it, the test plan proves nothing observable, the
unknowns are dressed up as certainty, and the comment ignores what the
thread or the repo's stated conventions ask. A rubric that ignores a
family will fail eval packages designed around that family.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Diagnosis follows the repro evidence | the plan's stated cause, read against every step of the repro-evidence block -- the failing run, every control run, and any debug/trace or intermediate output | the stated cause explains the failing step AND is consistent with every other step: if a control run, a trace showing where the error is raised, or an intermediate output shows the failure happening somewhere the cause cannot reach, the diagnosis fails; a cause adopted from the thread still has to survive the repro evidence, and a cause asserted with no tie to any repro step fails | required |
| Scope is one bounded change | the plan's in-scope and not-in-scope statements and every change it lists (files, approach steps, follow-ups), read against what the issue and the repro evidence actually need | every planned change is needed either to fix the reproduced behavior or to test that fix; bundling work the issue does not need -- a refactor or rewrite of surrounding code, a dependency migration, a new option/setting/feature, a cross-module unification, CI changes -- fails, even when the core fix inside it is correct; explicitly deferring related work, or scoping down to a subset with stated reasons, passes | required |
| A stranger could start executing | the plan's named files, functions, or code sites, and its chosen approach | the plan commits to one approach and names where it goes, at least down to a specific file or a specific code path within a named component (a file, a function, or "the client attach path in `zellij-server`'s session handling"); pinning the exact function later is fine when the approach and the code path are already fixed; leaving the approach, the layer, or the component undecided ("somewhere", "X or Y, whichever is easier", "gocui? tcell? not sure", "profile and see", "poke around") fails | required |
| Test plan is decisive | the plan's test plan, read against the repro evidence's failing step and its artifact | the test plan names an observable outcome that differs before and after the fix -- the repro's failing step re-run with the specific expected result (an output, an exit code, a visible state), or a named regression test asserting that behavior; a test plan that is only "run the full suite", "nothing else should break", "should feel fast", or that names no outcome tied to this fix, fails | required |
| Comment respects the thread and the repo's policy | the candidate plan comment, read against the thread highlights (comments by OWNER/MEMBER/COLLABORATOR/CONTRIBUTOR, and any open PR or prior art named there) and against the repo-facts contribution policy | if the thread carries explicit direction -- a maintainer naming the culprit location, proposing an approach, asking for something (such as testing a patch), or an open PR/prior art for the same fix -- the comment engages it, by following it or saying why it departs; ignoring it fails; and if the repo's stated policy requires disclosing AI use, the comment discloses it (treat every package as AI-assisted work); a policy that does not require disclosure passes on silence | required |
| Unknowns stated honestly | the plan's risks/unknowns statements and any deviation note, read against how confident its diagnosis and approach sound | open questions the evidence does not settle are named as open rather than written as settled facts | preferred |

## Verdict rule

Accept (ready to post and build from) only if every required check passes.
A required check graded fail holds the package; a required check graded
unclear (?) also holds it, because a plan that cannot be verified from the
package is not ready to build from. Preferred checks are reported but never
change the verdict.
