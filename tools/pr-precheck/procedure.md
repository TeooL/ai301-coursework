# Procedure: how this tool grades a PR package

<!--
THIS IS THE PART YOU WRITE (second week running for the procedure).
Week 3 you wrote these steps for a plan package; this week the graded
object is a PR package, and the read that matters most is a
side-by-side: the diff against the plan, the evidence against the test
plan, the description against both. Your week-3 procedure is the
pattern; do not paste it unchanged, because its read order was built
for a different object.

Your rotation is the design brief again, and this week friction routes
three ways: a stall on WHAT to decide is a rubric gap, a stall on
WHERE to look is a procedure gap (this file), and a stall on what the
tool even reads or outputs is a frame gap (your SKILL.md). A complete
procedure lets someone who has never seen a PR package before grade
one exactly the way you would.

Under each stage heading below, write the concrete steps for that
stage. The one-line note under each heading says what a complete
procedure must decide there. Write steps, not intentions: "read the
plan's scope pair before opening the diff, and list the files the plan
names" is a step; "understand the change" is a wish.
-->

## Read order

Read the parts in this order, writing down the named notes before moving
on. The candidate PR is read last on purpose: the plan's promises and the
repo's asks must be written down before the description's confident
wording is seen, so the diff gets compared against the plan instead of
against what the description says about it.

1. **The issue.** Note in one line the reported failure and the expected
   behavior.
2. **The thread.** Note every comment by an OWNER, MEMBER, COLLABORATOR,
   or CONTRIBUTOR that gives direction (names a cause or a file, asks a
   question of the fixer, rejects an approach). If none, note "no
   explicit direction".
3. **The plan and its repro evidence.** Write down three lists:
   - *Promised changes*: each change the plan says it will make, with its
     file.
   - *Boundary*: everything the plan says is out of scope, deferred, or a
     known limit, plus every deviation note.
   - *Test plan*: each repro step or failure mode the plan says it will
     re-run, with the expected-after it names.
4. **The repo's stated asks.** From the repo-facts block (live: the PR
   template, `docs/CONTRIBUTING.md`, the AI policy, and the scope's house
   rules), write a checklist of every required item: template sections
   or checklist lines, the issue-reference form, required files (such as
   a changelog), and whether AI-use disclosure is required (yes/no, with
   the quoted words).
5. **The candidate PR:** the commit list, then the diff, then the test
   evidence, then the title and description last.

## Evidence gathering

Build one evidence record per rubric check.

1. **Diff matches the plan.** List every file and every hunk in the diff
   in one line each. Tag each hunk *planned* (matches a promised change),
   *deviation* (matches a deviation note), or *unplanned*. Then go through
   the promised-changes list and tag each one *in diff*, *deferred and
   disclosed* (say where: plan note or description), or *missing*. Finally,
   copy out every sentence in the description that claims fidelity or
   delivery ("exactly as planned", "no other changes", "also updates the
   docs") next to what the diff shows for it.
2. **Evidence is decisive.** Tag each test-plan item from Read order step
   3 as *bug failure mode* (a failing behavior the issue or repro evidence
   shows) or *new behavior/control* (something the fix adds, such as a
   warning or guard, or a control run). For each bug failure mode, record
   whether the test evidence shows it re-run with a before and an after,
   and quote both. For each new-behavior/control item, record its stated
   outcome. Record which path each shown run exercises
   (the failing path the issue describes, or a control or unchanged
   path). Record the repo test command named and its stated outcome, or
   "none named".
3. **Diff is free of debris.** For each hunk tagged anything but
   *planned*, plus every added line in *planned* hunks, record any debug
   output, commented-out code, dead function, unused stub, or line whose
   only change is formatting, import order, or indentation. Quote the
   line. Record commit messages as written.
4. **Repo's stated asks are met.** For each item on the checklist from
   Read order step 4, record *present with content*, *present but empty or
   placeholder*, or *absent*, with the quote or file that shows it. For
   each thread direction from Read order step 2, record whether the
   description engages it.

Live mode: get the diff with `git diff main...HEAD` and the commits with
`git log --oneline main..HEAD`, both from the working copy. Read the plan,
draft, and evidence files the request names. Get the issue and thread
with `gh issue view <number> --repo <repo> --comments`. Read the asks from
`.github/PULL_REQUEST_TEMPLATE.md`, `docs/CONTRIBUTING.md`, and
`scope.md`'s house rules.

## Check execution

1. Run the checks in the rubric table's order, top to bottom.
2. Grade each check only from its evidence record. Re-read a part of the
   package only when a record is ambiguous, and only that part.
3. Grade each check on its own; one check's result never changes how
   another is graded. The same hunk can fail two checks (an unplanned
   rewrite is drift; a commented-out line inside it is also debris).
4. **Diff matches the plan:** fail if any hunk is *unplanned* and adds
   behavior (a new option, flag, setting, rename, rewrite, or a file the
   plan never names), if any promised change is *missing*, or if a
   recorded description claim does not match the diff. Cite the hunk or
   the claim. A change tagged *deferred and disclosed* never fails this
   check.
5. **Evidence is decisive:** fail if any bug failure mode has no shown
   before and after (unless it is deferred and disclosed), if the shown
   runs exercise only a control or unchanged path, or if the repo test
   command is "none named". A new-behavior/control item fails only if its
   outcome is not stated at all. Cite the missing item.
6. **Diff is free of debris:** fail if any debris line was recorded.
   Quote one. Comments that explain the fix are not debris.
7. **Repo's stated asks are met:** fail if any required checklist item is
   *absent* or *present but empty*, if disclosure is required and absent,
   or if a thread direction is not engaged. An ask the repo never states
   is not on the checklist and cannot fail this check.
8. **Commits describe the change** (preferred): fail if any commit message
   is only "wip", "fix", "fmt", or "cleanup".
9. **Unclear:** grade unclear only when the part of the package a check
   needs does not exist at all (no diff, no test evidence section). A
   part that exists but leaves something out is a fail, not unclear.
10. Give each grade one evidence line: the quote or fact that decided it.

## Verdict assembly

1. Collect the grades of the four required checks.
2. If any required check is fail or unclear, the verdict is `reject`. If
   all four pass, the verdict is `accept`.
3. Report the preferred check's grade, but leave it out of the verdict.
4. In the readable summary, for a reject, name each deciding check and
   quote its evidence line; for an accept, say every required check
   passed and name any disclosed shortfall the PR carries.
5. Live mode: add any voice-guide rule the title or description breaks,
   quoting the rule.
6. Emit the fenced JSON block last, with all five checks in rubric order.
