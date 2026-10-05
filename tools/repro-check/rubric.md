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
| environment-recorded | The repro report's environment record (the lines naming OS, versions, install method, or code state), read against the issue's own stated target environment and against any condition the issue says the behavior depends on (for example a build profile, a platform, or a version boundary). | Passes if the report names the platform it ran on, the version or code state of the thing under test, and every condition the issue identifies as changing the behavior — or if that information is identifiable from the report's own artifacts (a printed version string, a source path, a build-specific message in the output). A difference from the issue's reported environment passes only if the report says so in its own words; a difference the reader has to notice for themselves fails. Fails if no environment record is present and the artifacts carry none either. | required |
| steps-rerunnable | The commands, input files, and inputs the report shows, read as a sequence from starting state to the moment the trigger fires, against the reproduction the issue describes. | Passes if a stranger with the named environment could execute every step as written and arrive at the trigger: each command appears in runnable form, every input file's contents are shown, and no step stands in for work the reader has to invent ("set up the project", "configure as usual", "then trigger the bug"). For an interactive or TUI reproduction, the keystrokes and the panel they are pressed in, named in order, count as shown. Fails if the trigger is never shown being invoked at all. | required |
| behavior-matches-issue | The report's artifacts (output excerpts, tracebacks, logs, exit statuses, screenshots) read against the specific behavior the issue reports: its error text, failure class, and exit status. | Passes if the artifact shows the behavior the issue reports. The failure in the artifact must be the same class of failure the issue names — a panic for a reported panic, a silent no-op for a reported silent no-op, the reported error text or an obvious variant of it. Fails when the artifact shows an adjacent failure instead: a different error class, a graceful validation error standing in for a reported crash, or a failure produced by an invocation that differs from the one the issue names. | required |
| honest-outcome | The report's stated conclusion and its claim-comment summary, read against the artifacts the report itself shows. | Passes if every assertion the report makes is backed by an artifact in the report. A reproduction that failed passes this check when it says so and shows what it ran instead. Fails if the conclusion asserts more than the artifacts carry: calling a non-matching artifact a confirmation, claiming a frequency, scale, or cause nothing shown supports, or naming a root cause the run did not exercise. | required |
| conventions-and-disclosure | The repo-facts block's contribution-policy line (including any stated AI-use or AI-disclosure policy) and its bug-report template asks, read against what the claim comment and the repro report actually contain. | Passes if, where the repo's stated policy requires disclosing AI assistance on contributions or comments, a posted comment in the package discloses it, and if the substance of each field the repo's bug-report template asks for appears somewhere in the package. Fails if a stated disclosure requirement is unmet by every comment in the package. Judged on substance, not on using the template's headings or wording. | required |
| claim-is-specific-and-modest | The candidate claim comment, read against the issue body and thread. | Passes if the claim names something only a reader of this issue could name (the file, symbol, command, error text, or version boundary the issue turns on) and commits only to investigating and reporting back. Fails on interchangeable boilerplate or agreement with no content of its own ("+1", "same here", "I'd like to work on this"), and fails on a committed outcome the writer cannot guarantee from a claim — a fix, a pull request, or a date — or on a cause stated as settled before any reproduction exists. Naming the approach the writer intends to investigate is not such a promise. | required |
| control-run | The report's artifacts, looked at for a second run that differs from the failing one in one deliberate way (a neighbouring input, a version without the bug, the documented working path). | Passes if the report shows a run that isolates the trigger by contrast, demonstrating the failure is caused by the condition the issue names rather than by the setup as a whole. | preferred |

## Verdict rule

Accept if every `required` check passes. A fail on any `required` check
rejects the package. `unclear` on a `required` check counts as a fail,
with one exception the skill already defines: checks reported as
`not yet applicable: claim-only draft` are left out of the verdict
entirely, so a claim-only draft is accepted when the required checks
that can be graded all pass. `preferred` checks never change the
verdict; they say how strong an accepted package is.

<!-- State how the grades above combine into accept or reject, and how
unclear is treated. Example shape (write your own): "accept if every
required check passes; preferred checks never change the verdict;
unclear counts as fail." -->
