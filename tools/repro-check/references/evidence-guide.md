# Evidence guide: where proof lives in a reproduction package

<!--
THIS IS THE PART YOU WRITE (new this week: Unit 1 handed you this file
finished; the scaffolding fades). The skill uses this guide as its map:
for every kind of proof a rubric check names, this file says WHERE to
find it in a package and WHAT GOOD LOOKS LIKE when you do.

Under each family heading below, write:

- Where it lives: the exact places to look. In an eval bundle (which
  section of the package: the issue context, the repo-facts block, the
  claim comment, the repro report and its parts). In live mode (where
  on GitHub or in the draft: the issue thread, the repo's docs, the
  student's draft comment).
- What good looks like: one or two sentences someone else could apply.
  Prefer observable conditions ("the versions named match what the
  issue targets, or the difference is called out") over adjectives
  ("environment is thorough").

A rubric check whose evidence this guide cannot locate is a check
nobody else can execute; the rubric swap showed you what that feels
like. Write the map you wish your grader had.
-->

## Environment

Where it lives: the repro report's own environment line(s) -- tool/library
version, OS/platform when relevant, install method. In an eval bundle, this
is a labeled section of the report (often near the top); the target to
compare it against is the repo-facts block's version info and the issue
body's stated version. In live mode, the target is whatever the issue thread
or the repo's release notes say the bug is confirmed on; the candidate side
is the student's own draft report.

What good looks like: a specific version string (not "the latest" or "my
machine"), plus OS/platform when the issue is platform- or OS-specific. If
the environment used to attempt reproduction differs from the issue's
stated target, the report names the difference in words -- "tested on
1.5.3, the issue is confirmed on main" -- rather than silently reporting
results from the wrong version as if they answered the issue. No record at
all (nothing about version or platform, anywhere in the report) fails this
regardless of how convincing the rest of the report is.

## Steps

Where it lives: the repro report's numbered or narrated steps, read
alongside whatever inputs they reference (a config file, a command, a
fixture). In an eval bundle, check whether an input a step depends on is
actually shown in the bundle or only referenced ("our internal setup").
In live mode, check whether a step depends on the student's own machine,
private repo, or unshared config that a maintainer reading the comment
could not obtain.

What good looks like: every step is a concrete, copyable action (an exact
command, an exact file edit) rather than a stage name ("set up the
project," "configure the environment"). The step that actually triggers the
bug is present and specific -- not skipped in favor of "then I ran into the
issue." Every input the steps reference is either public (a package, a
public repo, a fixture included in the report) or fully reproduced inline;
a step that only works inside a private/unshared resource fails this check
even if the steps around it are clear.

## Behavior shown

Where it lives: the artifact -- an output excerpt, a log line, a stack
trace, a screenshot description -- quoted or shown in the repro report,
read side-by-side with the issue's own description of the symptom (error
message, exit code, visible behavior, expected-vs-actual).

What good looks like: the artifact exhibits the *same* symptom, not a
lookalike. Same error type and, where the issue names one, the same exit
code or same visible failure mode -- a graceful validation error is not the
same thing as a crash, and a process that is still running is not the same
thing as one that hung or died. Watch for a trigger that was quietly
changed (a different flag, a different input shape, a different syntax)
producing a different-but-plausible-looking failure. The one honest
exception: a report that states outright it could not reproduce, shows the
real attempt's own artifact, and names what was different about the setup
-- that is a pass on this check, because it shows the same rigor pointed at
a negative result.

## Honesty

Where it lives: the gap between the report's stated conclusion (a sentence
like "this confirms the bug" or "I could not reproduce this") and the
artifacts actually shown above it in the same report.

What good looks like: the conclusion claims exactly what the artifact
supports, no more. A claim with zero artifact behind it anywhere in the
report -- "I verified this," "guaranteed reproducible," a root-cause
diagnosis asserted without a transcript -- fails this check no matter how
confident or detailed the surrounding prose is. So does narrating an
artifact as showing something it does not (calling a graceful error a
crash; calling a still-alive process a crash; generalizing a result to a
version or platform that was never actually tested). An honest
cannot-reproduce, or a modest claim that matches its own evidence, passes.

## Comms

Where it lives: the claim comment's text on its own (read as a stranger on
the issue thread would read it, not knowing anything else about the
student), plus the repo's stated contribution policy or AI-use policy
(`CONTRIBUTING.md`, issue/PR templates, a dedicated `AI_POLICY.md` -- named
in the repo-facts block for eval, fetched live otherwise).

What good looks like, in two parts:

- **Specificity and honest intent.** The claim comment names the issue's
  actual symptom or fix approach in the student's own words, not a
  generic "can I work on this?" or "+1." It does not promise a fixed
  delivery date ("PR by Friday") or guarantee an outcome ("guaranteed
  fix") that the student cannot actually promise yet.
- **Disclosure.** If the repo's stated policy requires disclosing
  AI-assisted contributions, the comment discloses it in those terms. A
  policy that is silent, or that only asks for responsible use without
  requiring disclosure, means silence in the comment passes -- do not
  fail a comment for not disclosing when nothing required it to.

A repro report can be flawless on every other check and still fail here on
the claim comment alone; grade Comms independently of the report's quality.
