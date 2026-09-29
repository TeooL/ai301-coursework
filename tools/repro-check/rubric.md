# Rubric: is this reproduction package ready to post?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever
checks you define here. It ships empty on purpose: the judgment is your
work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four
   columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where in the package. Name
     the part (the claim comment, the repro report's environment
     record, the artifacts read against the issue's description, the
     repo-facts block) or a location from your
     references/evidence-guide.md. "The report" is not a source; "the
     output excerpt read against the error the issue describes" is.
   - Pass condition: a decision rule about the OUTCOME that someone
     else could apply and get your answer. Judge the thing itself (does
     the artifact show the issue's behavior?), never the write-up's
     shape (how many steps it has, how long it is, whether it uses a
     template's headings). Structure-shaped checks are what make
     graders disagree with themselves.
   - Weight: `required` (a fail here holds the package) or `preferred`
     (never changes the verdict).

2. A verdict rule below the table: how the check grades combine into
   accept (ready) or reject (hold), including how `unclear` is
   treated. The verdict space is binary. If you write no rule for
   `unclear`, the skill treats it as fail.

Cover what actually gets bad packages posted. The lecture named the
proof families: the environment is recorded, the steps are complete
and followable, the behavior shown matches the issue (not an adjacent
one), the outcome is stated honestly (an evidenced cannot-reproduce is
a pass, a confident wrong-target is not), and the words respect the
repo's conventions. A rubric that ignores a family will fail eval
packages designed around that family.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Environment recorded | the repro report's environment/version record, read against the issue's stated target version and platform (repo-facts block in eval; issue thread and repo docs in live mode) | the report states the exact tool/library version used to attempt reproduction, and the OS/platform too when the issue's behavior is platform-specific; if that version differs from the issue's stated target, the report says so explicitly rather than substituting silently | required |
| Steps followable | the repro report's steps section | a stranger holding only the stated environment and public, shareable inputs could re-run the exact sequence to reach the trigger; steps that depend on a private or unshared resource (an internal repo, an unshared config, credentials), or that name only a general setup ("configure the project") instead of the specific action that triggers the bug, fail this | required |
| Behavior shown matches the issue | the artifact (output/log/screenshot excerpt), read against the issue's stated symptom | if the report claims the bug reproduced, the shown artifact exhibits the same symptom the issue names (same error type, same exit code, same visible failure) -- not a different failure from a different trigger, and not a graceful or still-running result mistaken for a crash; if the report honestly states it could not reproduce, this check passes as long as a real attempt's own artifact is shown and the report names what differed from the issue's setup | required |
| Outcome stated honestly | the report's stated conclusion, read against what its own shown artifacts support | the conclusion never claims more certainty than the shown evidence supports -- a root-cause diagnosis, a "verified" claim, or a reproduction claim with no artifact behind it fails regardless of confidence or polish; an honestly-qualified cannot-reproduce, or a modest claim that matches its artifact, passes | required |
| Comms respect the repo's conventions | the claim comment and repro comment text, read against the repo's stated contribution/AI-use policy and against the issue itself | the claim comment names the issue's specific symptom (not a generic "assign me" or "+1") and does not promise a fixed delivery timeline or guarantee an outcome; and, if the repo's stated policy requires disclosing AI-assisted work, the comment discloses it -- silence only passes when the repo's policy does not require disclosure | required |
| Names a concrete next step | the closing line(s) of the repro report or claim comment | states what happens next (a fix approach, a specific question for maintainers, or a stated intent to open a PR) rather than ending with no forward motion | preferred |

## Verdict rule

Accept (ready to post) if every required check passes. Preferred checks never
change the verdict. Unclear counts as fail: proof that cannot be verified is
proof that is not ready to post, and a claim-only draft's not-yet-applicable
repro checks are excluded from this rule entirely (per SKILL.md), not treated
as unclear.
