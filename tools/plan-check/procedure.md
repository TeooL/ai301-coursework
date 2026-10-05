# Procedure: how this skill grades a plan package

<!--
THIS IS THE PART YOU WRITE, and it is a new kind of part. Weeks 1 and
2, SKILL.md carried a numbered workflow and you only wrote judgment
files. This week the workflow is gone from the frame: SKILL.md says
"execute procedure.md", and these are the operating steps you author.
The machinery is in your hands now.

Your operator swap is the design brief. When your executor stalled
because your rubric said WHAT to decide but not HOW to find the
evidence, that was a procedure gap. This file is where those gaps get
closed: a complete procedure lets someone who has never seen a plan
package before (a groupmate, or the skill itself) grade one exactly the
way you would.

Under each stage heading below, write the concrete steps for that
stage. The one-line note under each heading says what a complete
procedure must decide there. Write steps, not intentions: "read the
repro evidence before the plan, and note what behavior it pins down"
is a step; "understand the context" is a wish.
-->

## Read order

Read the parts in this order, and write down the notes named in each step
before moving on. The plan is read last on purpose: the evidence notes must
exist before the plan's confident wording is seen, so the diagnosis check
compares the plan against the evidence instead of reading the evidence
through the plan.

1. **The issue.** Read the title and body. Note, in one line, the reported
   symptom (the exact error, output, exit code, or visible behavior) and
   the expected behavior.
2. **The repro evidence.** Read every step. For each step, note what was
   run and what it showed. Label each step as one of: *failing run* (shows
   the bug), *control* (a variation that does not show the bug), or
   *probe* (debug output, a trace, an intermediate value). For each control
   and probe, note in one line what it rules in or rules out (for example:
   "step 2: same input without the flag parses fine, so the input parser
   is not the cause").
3. **The thread.** Read the thread highlights (live: the issue's comments).
   Note every comment by an OWNER, MEMBER, COLLABORATOR, or CONTRIBUTOR
   that names a culprit location, proposes or rejects an approach, or asks
   for something, and every open PR or prior-art link. Record the author,
   the association, and the direction in one line each. If there are none,
   note "no explicit direction".
4. **The repo facts.** Read the contribution policy. Note whether it
   requires disclosing AI use (yes/no, with the quoted words), and any
   other contributing asks.
5. **The candidate plan**, then **the candidate plan comment**. Read both
   in full before grading anything.

## Evidence gathering

Gather one evidence record per rubric check, from the parts named here.

1. **Diagnosis:** quote the sentence(s) where the plan states the cause.
   Next to the quote, list every repro step from Read order step 2,
   carrying its label and its rules-in/rules-out note.
2. **Scope:** list every change the plan proposes, from its in-scope
   statement, its approach, its file list, and any "also" or follow-up
   items. Tag each change as *fix* (needed to fix the reproduced
   behavior), *test* (needed to test that fix), or *extra* (anything
   else). Record explicit deferrals ("not in scope", "deferred",
   "separate PR") separately: they are not changes.
3. **Executability:** record each file, function, or code site the plan
   names, and the approach it commits to. Copy out any hedge that leaves a
   decision open: "or", "maybe", "somewhere", "not sure", "whichever",
   "investigate", "profile".
4. **Test plan:** quote the test plan. Record the observable outcome it
   names (or "none"), and which repro step it re-runs or which named
   regression test asserts it.
5. **Honesty:** quote the plan's risks, unknowns, and any deviation note.
6. **Comms:** quote the plan comment. For each direction recorded in Read
   order step 3, record whether the comment follows it, says why it
   departs, or does not mention it. Record whether the comment discloses
   AI use, next to the policy note from Read order step 4.

Live mode: read the issue and thread with `gh issue view <number> --repo
<repo> --comments`. The repro evidence is the student's own repro comment
on that thread, as the drafts quote it; if the drafts quote none, record
"no repro evidence quoted". Read the policy from `docs/CONTRIBUTING.md`,
any `AI_POLICY.md`, and `.github/` templates. Grade only what the drafts
contain, not other files in the working directory.

## Check execution

1. Run the checks in the rubric table's order, top to bottom.
2. Grade each check only from its evidence record. Re-read a part of the
   package only when the record is ambiguous, and then only that part.
3. Grade each check on its own. One check failing never changes how
   another is graded.
4. **Diagnosis:** for each repro step, ask "if the plan's cause were true,
   would this step have shown what it showed?" If any step answers no,
   grade fail and cite that step. Agreement with a thread comment does not
   count as support unless the repro evidence also supports it.
5. **Scope:** grade fail if any change is tagged *extra*, and cite it.
   Deferrals never count against scope.
6. **Executability:** grade fail if neither a file nor a specific code
   path within a named component is named, or if a recorded hedge leaves
   the approach, the layer, or the component undecided. A hedge that only
   defers the exact function name, after the approach and code path are
   fixed, is not a fail: a stranger can still start there.
7. **Test plan:** grade fail if the recorded observable is "none" or is
   only a whole-suite run, a no-regressions statement, or a feeling.
8. **Comms:** grade fail if any recorded direction is "does not mention
   it", or if the policy requires disclosure and the comment has none.
9. **Unclear:** grade unclear only when the part of the package the check
   needs does not exist at all and the check cannot be decided either way.
   A plan that is present but leaves something out (no file named, no
   observable outcome) is a fail, not unclear.
10. Each grade gets one evidence line: the quote or fact that decided it.

## Verdict assembly

1. Collect the grades of the required checks.
2. If any required check is fail or unclear, the verdict is `reject`.
   If every required check is pass, the verdict is `accept`.
3. Report preferred check grades, but leave them out of the verdict.
4. In the readable summary, for a reject, name each deciding check and
   quote its evidence line; for an accept, say that every required check
   passed.
5. Emit the fenced JSON block last, with every check (required and
   preferred) in rubric order.
