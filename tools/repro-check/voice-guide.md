# Voice guide: how I talk upstream

<!--
THIS IS THE PART YOU WRITE (new this week). Live mode reads this file
before any comment of yours goes out the door; eval mode ignores it
entirely, because your voice is yours and carries no gold labels.

This is not etiquette. "Be polite and concise" is advice for everyone
and therefore rules for no one. Write rules YOU need, in your own
words, each one concrete enough that the skill can hold a draft
against it and say which rule it breaks.

Three sections. Fill all three.
-->

## Who I am in threads

I'm a second-year CS student working through this repo as a course exercise
(AI301, Path Review) -- this is my first open-source contribution. I'm not
hiding that: readers can expect a comment that's honest about what I
actually ran and actually found, not one dressed up to sound more senior
than I am. If I'm unsure about something, I'll say so instead of guessing
past it.

## Rules I write by

### Rule: say what ran, not what should run

I only claim a result I actually produced. If I haven't run the fix or the
repro, I say that plainly instead of predicting the outcome.

- Wrong: "This should fix it."
- Right: "I haven't run this against the failing case yet -- I'll paste the
  output once I do."

### Rule: no dates I don't control

I don't promise a delivery timeline, because coursework and life both slip
and a broken promise upstream is worse than no promise. I commit to a next
action, not a date.

- Wrong: "I'll have a PR up by Friday."
- Right: "Next step on my end is writing a test that reproduces this, then
  I'll open a PR."

### Rule: quote the issue, don't paraphrase it into agreement

Before I claim I've reproduced something, I check my artifact against the
issue's *exact* wording -- error type, exit behavior, described symptom --
not just "something went wrong here too."

- Wrong: "Yep, seeing the same issue."
- Right: "I'm seeing a `KeyError` on the same input, which matches the
  traceback in the issue body."

### Rule: name what I don't know

If a step didn't work, or I'm not sure my environment matches the issue's,
I say so instead of smoothing over the gap.

- Wrong: "Works for me!"
- Right: "This didn't reproduce on my setup (Python 3.11, not the 3.9 the
  issue names) -- flagging the version difference rather than assuming
  it's unrelated."

## Things I never post

- I never say a fix works, or that I've "verified" something, without
  pasting the output that backs it up.
- I never promise a fixed date or a guaranteed outcome for work I haven't
  finished yet.
- I never post a "same as above, can confirm" comment without having run
  it myself in my own environment -- Path Review's house rule against
  piggybacking is also just how I want to work.
- I never let a comment go out with generic filler ("Thanks for reading!",
  "Hope this helps!") standing in for something specific to this issue.
