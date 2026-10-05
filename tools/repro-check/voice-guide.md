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

<!-- 2-3 lines. Who is talking when you comment on an issue: your
experience level stated plainly, what you are doing in this repo, what
readers can expect from you. This is the register your rules protect. -->

I am new to contributing to other people's repositories. I know Python
and FastAPI well enough to read a route and a SQLAlchemy call and say
what they do, and I am still learning how a project I did not write
expects to be approached. What a reader can expect from me is that
anything I state as fact, I ran, and anything I have not run yet I
label as a guess. If I turn out to be wrong, I say so in the thread
rather than quietly editing the comment.

## Rules I write by

<!-- 3-5 rules, drafted from the lecture's slide-12 moment. Each rule
needs a wrong/right pair from your own hand: one line you might
actually have written that breaks the rule, and the line you would
post instead. The pair is what makes a rule executable; a rule without
one is a wish.

Format each rule like this:

### Rule: <short name>

<The rule, one or two sentences.>

- Wrong: "<a line that breaks it>"
- Right: "<the line to post instead>"
-->

### Rule: I promise investigation, never outcomes

A comment from me commits to looking into something and reporting back.
It never commits to a fix, a pull request, or a date, because I do not
yet know what the fix costs.

- Wrong: "I'll have a PR up for this by the weekend."
- Right: "I'm going to try to reproduce this and I'll post what I find. If it turns out to be the `text()` wrapping the body suggests, I'll say so before attempting anything further."

### Rule: cause is a hypothesis until I have run it

I can say a line of code looks responsible. I may not say it *is*
responsible until I have changed it and watched the behavior change.
The phrase that keeps me honest is "looks like", and it earns its place
by being followed with what I actually observed.

- Wrong: "This is happening because SQLAlchemy 2.x requires textual SQL to be wrapped in `text()`."
- Right: "The body points at `text()` wrapping, and the traceback I got lands in the same call, so that is my working hypothesis — I have not confirmed it by changing it yet."

### Rule: I show the output instead of describing it

If I say something failed, the failure is pasted in. A described error
is an opinion; a pasted error is evidence, and it is also the thing
that lets someone correct me.

- Wrong: "I ran the health endpoint and it threw a SQLAlchemy error."
- Right: "`curl localhost:8000/health` returns 500; the traceback ends in `sqlalchemy.exc.ObjectNotExecutableError: Not an executable object: 'SELECT 1'` — full output below."

### Rule: no enthusiasm standing in for content

I do not open with praise for the project, apologise for being a
beginner, or add agreement that carries no information. If my comment
would read the same on any other issue, it is not ready to post.

- Wrong: "Great project! Sorry if this is a dumb question, I'm new to open source but I'd love to help out with this one if that's okay 🙏"
- Right: "I'd like to take this one. It's the `SELECT 1` probe in `api/routes/health.py`, which is a small enough surface that I can verify it properly as a first contribution."

### Rule: a failed attempt gets posted too

If I cannot reproduce, or I reproduce something different from what the
issue reports, that goes in the thread with what I ran. A
cannot-reproduce with evidence is useful to the maintainer; silence
after a claim is not.

- Wrong: (nothing posted, because the result was not the one I wanted)
- Right: "I could not reproduce this on `main` at `a1b2c3d`. Here is exactly what I ran and what I got instead — it may be environment, so I'd rather show it than guess."

## Things I never post

<!-- A short list. Promises you cannot keep, tones you refuse,
shortcuts you know you reach for when tired. The skill quotes this
list back at you when a draft crosses it. -->

- A date, or any form of "soon" that implies one.
- "I'll fix this" before I have reproduced it.
- A root cause I have not exercised, stated without "looks like" or an
  equivalent hedge.
- "+1", "same here", "any update on this?", or agreement with nothing
  of my own underneath it.
- A summary of someone else's reproduction in place of my own run.
- An apology for my experience level used as cover for not having
  checked something.
- Praise for the project as an opener.
