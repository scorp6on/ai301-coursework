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

<!-- Where the environment record lives, and what a sufficient one
looks like against the issue's stated target. -->

**Where it lives.** In an eval bundle: the opening lines of the
`## Candidate repro report` section, where a report states what it ran
on; the `## Issue` section, where the reporter states their own OS and
version (often as `Operating system:` / `Version:` lines or in the
body's first paragraph); and the `latest release` and
`bug reports: template asks for` lines in the `## Repo facts` block,
which say which environment fields this repo expects. Live mode: the
environment lines of the student's draft report, the issue body on the
issue thread, and the repo's issue template under
`.github/ISSUE_TEMPLATE/` or its bug-report form.

**What good looks like.** The platform, the version or commit of the
thing under test, and anything the issue names as changing the behavior
are all stated, so a reader can assemble the same conditions without
guessing. Where the report ran on something different from the issue's
environment — a newer release, another OS, a release build where the
issue describes a debug build — the report names the difference itself,
in a sentence like "filed against 4.53.2; I tested the current
release". The version of a tool printed inside a shown artifact counts
as a record of it. An absent environment record is a fail even when
the rest of the report is strong, because nobody can tell whether a
later reader's different result is a different bug or a different
machine.

## Steps

<!-- Where the reproduction steps live, and what makes them followable
by a stranger, starting state to trigger. -->

**Where it lives.** In an eval bundle: the body of the
`## Candidate repro report`, wherever it shows commands, input files,
or interactions — usually a fenced block of `$`-prefixed commands, a
`cat` of an input file, or a numbered list; read against the `Steps:`
or reproduction lines in the `## Issue` section. Live mode: the same
part of the student's draft, read against the issue body's steps and
against the repo's own setup documentation (README, CONTRIBUTING,
docs/) for whatever the report treats as already done.

**What good looks like.** The sequence runs from a state the reader can
actually create — a fresh `git init`, a clean clone at a named commit,
an input file whose contents are shown — through to the command that
fires the trigger, with every flag and argument present in runnable
form. A step that compresses real work into a phrase ("set up the
project", "configure the server as usual", "then trigger the bug") is
the failure to look for: it reads as a step and is not one. The trigger
must be shown being invoked, not just described; a report that narrates
pressing a key or running a command without showing the invocation and
its result leaves the reader to reconstruct the one thing that matters.
Short is fine. Four lines that someone can paste beat a numbered page
that assumes a working checkout.

## Behavior shown

<!-- Where the artifacts live (output excerpts, logs, screenshots),
and what it means for an artifact to show the issue's behavior rather
than an adjacent one. -->

**Where it lives.** In an eval bundle: the fenced artifacts inside
`## Candidate repro report` — command output, tracebacks, panic
messages, exit statuses, the result of a verifying command such as
`git stash list` or `git status --short`; read against the artifacts
and error text quoted in the `## Issue` section, and against anything
in `## Thread highlights` where a maintainer narrows what counts as the
bug. Live mode: the artifacts pasted into the student's draft, read
against the issue body's quoted output and any maintainer comment on
the thread.

**What good looks like.** The artifact shows the same *class* of
failure the issue reports, produced by the invocation the issue names.
A reported panic is shown as a panic; a reported silent no-op is shown
as the absence it is, by a verifying command whose empty or unchanged
output is the evidence; a reported error message appears in the output,
verbatim or as an obvious variant. The adjacent-failure case is the one
to hunt: a run that fails for a different reason and is narrated as the
bug. It shows up as a mismatch between the two sides — the issue
reports `panic: not a string` from a crash inside a decoder and the
artifact shows a clean `Error: unable to parse` with a syntax
complaint; the issue reports an overflow crash and the artifact shows
an argument-validation error exiting 1. When the report's own command
or input differs in a detail from the issue's (a changed separator, a
different range syntax, a prefix instead of an offset), that detail is
usually what produced the wrong failure, so compare the invocation as
closely as the output.

## Honesty

<!-- Where claims and their backing meet: how to tell a report that
says exactly what happened (including an honest cannot-reproduce) from
one that claims more than its evidence shows. -->

**Where it lives.** The gap between two places in the same bundle: the
report's concluding or analysis lines — `**Analysis.**`, `Actual:`,
"this confirms", a closing summary — and the artifacts shown above
them in the same `## Candidate repro report`. Also the summary sentence
of `## Candidate claim comment`, where a package often asserts a result
the report never produced. Live mode: the same two parts of the
student's drafts; nothing outside the drafts counts, because a reader
on the thread sees only what was posted.

**What good looks like.** Every sentence in the conclusion can be
pointed back to a line in the report. A report that reproduced says so
and shows it; a report that could not says that and shows what it ran
instead — an evidenced cannot-reproduce is a good outcome honestly
reported, not a failure of the package. The overclaim to catch is the
confident sentence sitting on top of evidence that does not support it:
"as demonstrated above, exactly the class of failure the issue
describes" written over a non-matching artifact; a frequency or
population claim ("happens constantly", "everyone I know has this")
with no artifact behind it; a root cause named by file and function
when the run never exercised it; "I ran it ten times" and "confirmed on
the reporter's version too" with no output from either. Claiming less
than the evidence shows is never a fail here.

## Comms

<!-- Where the words meet the repo: the claim comment against the
issue, the comments against the repo's stated templates and
contribution policy (including AI-use disclosure requirements), and
what specific-and-honest looks like next to boilerplate. -->

**Where it lives.** The `contribution policy (CONTRIBUTING.md)` line
and the `bug reports: template asks for` line in the `## Repo facts`
block, read against the full text of `## Candidate claim comment` and
`## Candidate repro report`. Live mode: the repo's `CONTRIBUTING.md`,
its `README`, its issue-template files, and any posted policy about
AI-assisted contributions, read against the student's draft comments;
the house rules in `scope.md` also apply in live mode.

**What good looks like.** On disclosure: where the repo's stated policy
requires contributors to disclose AI assistance in their contributions
or comments, a comment in the package says so plainly in its own
sentence. A policy that merely *mentions* AI — a maintainer noting that
AI-generated pull requests are hard to review, a guide that asks you to
understand your own patch — sets no disclosure requirement, and silence
sets none either; the fail is a stated requirement that no comment in
the package meets. On the template: the substance of each field the
repo asks for appears somewhere in the package, in whatever shape the
writer prefers — the headings and the wording are not the evidence, the
content is. On the claim comment: it names something only a reader of
this issue could name — the file, the symbol, the command, the error
text, the version boundary — and it commits to investigating and
reporting back, nothing further. Next to that, boilerplate is easy to
see: swap the issue number and the comment would fit any other thread.
The promise to catch is the one the writer cannot keep from a claim
alone — a fix, a date, or a cause stated with confidence before any
reproduction exists.
