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

<!-- Where the plan states its cause, and where the repro evidence
pins down the behavior that cause must explain. What it means for a
diagnosis to follow from the evidence rather than contradict or
ignore it. -->

- Where it lives (eval): the cause is in the candidate plan's
  "Diagnosis" or "Cause" paragraph, or the first sentence that says
  why the bug happens. The behavior it must explain is in the "Repro
  evidence" block: its numbered steps, any "Control:" run, timings or
  outputs, and the "Actual:" line.
- Where it lives (live): the cause is in the draft `plan.md`'s
  diagnosis. The behavior is in the student's posted repro comment on
  the issue, or the repro evidence the drafts quote.
- What good looks like: the cause predicts every run the repro shows,
  including the control. If the repro shows the bug still happening
  with the blamed component out of the loop (for example, with no
  pager, or with the setting off), the cause is wrong. A cause copied
  from a thread comment is only good if the repro evidence agrees
  with it.

## Scope

<!-- Where the plan bounds itself: the in-scope statement, the
not-in-scope line, the files or areas named. What one bounded change
looks like next to a drive-by rewrite. -->

- Where it lives (eval): the plan's "Scope" / "In:" and "Out:" lines,
  plus every file and step listed under "Change", "Changes", or
  "Approach".
- Where it lives (live): the same sections of the draft `plan.md`.
- What good looks like: one change to the code that produces the
  reproduced behavior, plus its test. A bounded plan can name
  neighbouring problems as out of scope. A drive-by plan also
  refactors, renames, reformats, upgrades, or fixes a second bug.

## Executability

<!-- Where the plan says what will actually be done: files or areas,
approach, order of work. What it means for a stranger to be able to
start executing without asking the author anything. -->

- Where it lives (eval): the plan's "Change" / "Approach" steps and
  the file paths, functions, or modules they name.
- Where it lives (live): the draft `plan.md`'s files-to-touch and
  approach sections.
- What good looks like: a stranger could open the named file and make
  the first edit without asking anything. "Poke around", "figure out
  where X lives", and "make it work" are intentions, not steps.

## Test plan

<!-- Where the plan says how success will be observed, and how that
maps onto the repro evidence's steps and artifacts. What a decisive
test plan names that a vague one does not. -->

- Where it lives (eval): the plan's "Test" / "Test plan" section,
  read against the repro evidence's steps and its "Expected:" line.
- Where it lives (live): the draft `plan.md`'s test plan, read against
  the student's repro steps.
- What good looks like: re-run the repro trigger (or a test that
  drives the same inputs through the real code) and name the exact
  result that will change: the color flips at step 3, the request
  returns 200, the error text is gone. "Run the test suite" or
  "nothing regresses" alone never observes the fix.

## Honesty

<!-- Where claims meet uncertainty: risks, unknowns, and deviations.
How to tell stated unknowns from false confidence, and where an
honest mid-build deviation gets recorded. -->

- Where it lives (eval): every settled claim in the plan and the
  comment ("I traced this to", "this fixes", "should be easy"), any
  risks or unknowns section, and any maintainer note in the thread
  highlights about difficulty or intended behavior.
- Where it lives (live): the draft `plan.md`'s risks and unknowns,
  its `## Deviations` section after the build, and the draft comment.
- What good looks like: claims match what the repro and thread
  support, and guesses are labeled as guesses. A plan that says "I
  traced this" with no trace shown, or promises a simple fix where a
  maintainer said the fix is hard, is overconfident. A mid-build
  change is honest when it is written under `## Deviations` in
  `plan.md`.

## Comms

<!-- Where the words meet the thread and the repo: the plan comment
read against the issue's maintainer signals (thread highlights, or
the live thread) and against the repo-facts block's stated templates,
contributing asks, and contribution policy (including AI-use
disclosure requirements). What thread-aware looks like next to
boilerplate. -->

- Where it lives (eval): the "Candidate plan comment" section, read
  against the "Thread highlights" (OWNER, MEMBER, COLLABORATOR
  comments first) and the "Repo facts" block's contribution-policy
  line, including any AI-use policy.
- Where it lives (live): the draft `comment.md`, read against the
  live issue thread and the repo's CONTRIBUTING / README / AI policy.
- What good looks like: the comment names this issue's cause, file,
  or approach; it responds to what maintainers already said instead
  of contradicting or ignoring it; and it meets every stated policy
  requirement, such as disclosing AI assistance or writing the
  comment in the author's own words. A disclosure requirement is met only by a disclosure sentence in the comment itself; a comment that never mentions AI use does not meet it. A stated PR precondition (vouch flow, discuss first) is met only if a comment that mentions a PR acknowledges it. Boilerplate ("I'll fix this, PR
  soon!") or a comment that talks past a maintainer's decision fails.
