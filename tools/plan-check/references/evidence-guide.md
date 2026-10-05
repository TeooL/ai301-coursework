# Evidence guide: where evidence lives in a plan package

<!--
THIS IS THE PART YOU WRITE (second week running: the judgment files
stay in your hands). The skill uses this guide as its map: for every
kind of evidence a rubric check names, this file says WHERE to find it
in a plan package and WHAT GOOD LOOKS LIKE when you do.

Under each family heading below, write:

- Where it lives: the exact places to look. In an eval bundle (which
  section of the package: the issue context, the repro-evidence block,
  the candidate plan's scope statement or test plan, the plan comment,
  the repo-facts block). In live mode (where on GitHub or in the
  draft: the issue thread, the student's posted repro comment, the
  repo's docs, the draft plan and comment).
- What good looks like: one or two sentences someone else could apply.
  Prefer observable conditions ("the stated cause cites behavior the
  repro evidence actually shows") over adjectives ("diagnosis is
  solid").

A rubric check whose evidence this guide cannot locate is a check
nobody else can execute, and this week that cuts twice: your
procedure.md tells the skill WHEN to gather each family, and this
guide tells it WHERE. Write the map you wish your executor had.
-->

## Diagnosis and grounding

Where it lives: the cause is stated in the candidate plan, usually in a
"Cause" or "Diagnosis" line at the top. The behavior it must explain is in
the repro-evidence block: the failing run, plus any control runs (the same
thing with one variable changed) and probes (`--debug` output, traces,
intermediate values). Live: the student's posted repro comment on the
issue, as quoted in plan.md.

What good looks like: the cause explains the failing step and does not
collide with any control or probe. A control that succeeds rules out
every cause it shares with the failing run, and a trace that shows where
the error is raised rules out causes that sit after that point. A
confident cause that a control contradicts is wrong however polished it
is, and so is a cause taken from the thread that the evidence does not
support.

## Scope

Where it lives: the plan's in-scope and not-in-scope lines, its approach
steps, its file list, and any "while I'm here", "also", or follow-up item.
Compare these against the issue body and the repro's expected-vs-actual
line, which say what actually needs to change.

What good looks like: every change is needed to fix the reproduced
behavior or to test the fix. A bounded plan can be one line in one file.
A drive-by rewrite adds refactors, migrations, new options or settings,
UI work, or CI changes the issue never asked for, even when the real fix
is in there too. Saying explicitly that related work is deferred, with a
reason, is a sign of good scope.

## Executability

Where it lives: the files, functions, or code sites named in the plan's
change or approach section, and the approach it commits to.

What good looks like: a stranger could open the named file or code path
and start the named change without asking the author anything. One chosen
approach, and a location at least as specific as a file or a named code
path within a component. Saying the exact function will be pinned down
from debug output is fine when the approach and the code path are already
decided. A plan that defers its real decisions to build time ("somewhere
in the input stack", "gocui? tcell? not sure", "upstream or vendored,
whichever is easier", "profile and optimize") cannot be started.

## Test plan

Where it lives: the plan's test or verification section, compared with
the repro evidence's failing step and its artifact.

What good looks like: it names the outcome that will be different after
the fix: the repro's failing step re-run with the expected output, exit
code, or visible state, or a named regression test asserting that
behavior. "Run the full test suite", "nothing else should break", or
"should feel fast" names nothing that would show this fix worked.

## Honesty

Where it lives: the plan's risks or unknowns section, any "not sure" or
"open question" sentence, and the Deviations section after a build.

What good looks like: what the evidence does not settle is called open
(a tool that may not accept a flag, a platform the student could not
test). A plan that states every guess as fact has hidden its unknowns.
A deviation is recorded honestly when plan.md says what changed and why,
not when the change only shows up in the diff.

## Comms

Where it lives: the candidate plan comment, read against the thread
highlights (live: the issue's comments) and the repo-facts contribution
policy (live: `docs/CONTRIBUTING.md`, `AI_POLICY.md`, `.github/`
templates).

What good looks like, in two parts:

- **Thread-aware.** If a maintainer or core contributor has named the
  culprit, proposed an approach, asked for testing, or if an open PR or
  prior art exists for the same fix, the comment engages it: follows it,
  builds on it, or says why it goes another way. A comment that proposes
  something unrelated as if the thread were empty ignores the people who
  will review it.
- **Policy-compliant.** If the repo's stated policy requires disclosing
  AI use, the comment discloses it, naming the tool and how much it did.
  If the policy is silent or only asks for a human in the loop, saying
  nothing about AI passes.
