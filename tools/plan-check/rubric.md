# Rubric: is this plan ready to post and build from?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever
checks you define here (via your procedure.md). It ships empty on
purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four
   columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where in the package. Name
     the part (the plan's scope statement, the test plan read against
     the repro evidence's steps, the plan comment read against the
     thread highlights, the repo-facts block) or a location from your
     references/evidence-guide.md. "The plan" is not a source; "the
     plan's stated cause read against what the repro evidence shows"
     is.
   - Pass condition: a decision rule about the OUTCOME that someone
     else could apply and get your answer. Judge the thing itself (is
     this one bounded change? could a stranger start executing it?),
     never the write-up's shape (how many sections it has, how long it
     is, whether it uses headings). Structure-shaped checks are what
     make graders disagree with themselves.
   - Weight: `required` (a fail here holds the package) or `preferred`
     (never changes the verdict).

2. A verdict rule below the table: how the check grades combine into
   accept (ready) or reject (hold), including how `unclear` is
   treated. The verdict space is binary. If you write no rule for
   `unclear`, the skill treats it as fail.

Cover what actually gets bad plans posted. The lecture named the
failure families: the diagnosis ignores or contradicts the reproduced
evidence, the change is unbounded (scope creep), the plan targets the
symptom while the evidence points at the cause, a stranger could not
start executing it, the test plan proves nothing observable, the
unknowns are dressed up as certainty, and the comment ignores what the
thread or the repo's stated conventions ask. A rubric that ignores a
family will fail eval packages designed around that family.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| diagnosis-follows-evidence | The plan's stated cause (its diagnosis, or the sentence that says why the bug happens), read against what the repro evidence block actually shows: its steps, its control runs, its timings or outputs, and its "Actual" line. | Passes if the stated cause explains the behavior the repro evidence shows, including any control run (the cause must predict both the failing run and the one that worked). Fails if the cause contradicts the repro evidence (the evidence shows the behavior with the blamed component taken out of the loop, or without the blamed condition), if it adopts a cause from the thread that the repro evidence rules out, or if the plan never says what causes the behavior at all. A cause stated as a hypothesis still passes if the evidence is consistent with it. | required |
| fixes-the-cause | The plan's change (its approach or numbered changes), read against the plan's own diagnosis and the repro evidence's "Actual" line. | Passes if the change acts on the mechanism the diagnosis names, so that if the diagnosis is right the repro's failing step would stop failing. Fails if the change only hides or works around the symptom while leaving the named cause in place (catching and swallowing the error, adding a retry or timeout, special-casing the one input from the repro, telling users to avoid the trigger), unless the plan says outright that it is a workaround and why. | required |
| scope-is-bounded | The plan's in-scope and out-of-scope statements and the files or areas its approach touches, read against the one behavior the issue and repro evidence describe. | Passes if every change the plan proposes is needed to fix the reproduced behavior, or is a test or doc update for that same fix. Fails if the plan also bundles unrelated work into the same change: refactoring or rewriting a subsystem, renaming or reformatting files the fix does not need, fixing other bugs or adding features, upgrading dependencies, or "while I'm in there" cleanup. Saying a nearby problem is out of scope is fine; doing it is not. | required |
| stranger-can-start | The plan's approach: the files, functions, or areas it names and the steps it lists, read as instructions for someone who has never talked to the author. | Passes if a contributor who knows the language could start the first edit from the plan alone: it names where the change goes (a file, function, module, or clearly identified code area) and what the change is. Fails if the plan is an intention rather than a plan ("poke around the editor code", "figure out where X lives", "fix it so it works"), or if the location of the change is left for the reader to discover. | required |
| test-plan-observes-the-fix | The plan's test plan, read against the repro evidence's steps and its "Expected" line. | Passes if the test plan re-runs the reproduced trigger (the repro steps, or a test that drives the same inputs through the real code) and names the specific observable result that will show the fix worked (the output, color, timing, error absence, or value the repro's "Expected" line names). Fails if the test plan is only "run the test suite", "make sure nothing regresses", or "check it works", or if it checks something other than the reproduced behavior. Extra regression checks on top are fine. | required |
| certainty-is-honest | The plan's and the plan comment's claims of completed work, outcome, and completeness, read against what the repro evidence and thread actually establish. | Passes unless the plan or comment does one of these: (1) rests its diagnosis or its fix on work the package does not show, where the repro evidence does not support that claim either (for example "I traced this to X" when the repro evidence points elsewhere or shows nothing about X). Incidental side remarks about local checks that the diagnosis does not depend on are not overconfidence; (2) guarantees more than the reproduced behavior ("fixes all cases", "no risk", "cannot break anything"); or (3) answers a maintainer's stated concern in the thread (for example "the fix is hard" or "this is intended behavior") by promising a simple fix without addressing it. A plan stating its working cause plainly is not overconfidence: whether that cause is supported is graded by diagnosis-follows-evidence, not here. Details openly deferred to the build ("exact function to be pinned after tracing") are honest. A plan without a risks section is not a fail by itself. | required |
| comment-fits-thread-and-repo | The candidate plan comment, read against the thread highlights (especially comments from OWNER, MEMBER, or COLLABORATOR accounts) and the repo-facts block's contribution policy, including any AI-use or disclosure rule. | Passes if the comment (a) says something specific to this issue's plan (the cause, the file or area, or the approach), not boilerplate; (b) does not contradict or ignore what a maintainer has said in the thread (a stated design decision, a requested approach, a "please discuss first", a "won't fix" or "working as intended" note); and (c) meets every requirement the contribution policy or bug-report facts state for comments or PRs. When the policy requires disclosing AI use, the comment itself must contain an explicit disclosure statement (the tool and extent of assistance, or a plain statement that none was used); a comment that is silent about AI use fails (c), even if it is otherwise excellent, because silence is not disclosure. When the repo states a precondition for PRs (a vouch or approval flow for first-time contributors, discuss or get assigned first, limited review), a comment that mentions a PR or patch must acknowledge that precondition. Fails if any of (a), (b), or (c) fails. Where the policy states no requirement, (c) passes automatically. | required |
| risks-named | The plan's risks or unknowns section, or any sentence that names what could go wrong. | Passes if the plan names at least one concrete risk, unknown, or side effect of the change (another caller of the same code, a platform it was not tested on, a behavior change users might notice). | preferred |

## Verdict rule

<!-- State how the grades above combine into accept or reject, and how
unclear is treated. Example shape (write your own): "accept if every
required check passes; preferred checks never change the verdict;
unclear counts as fail." -->

Accept if every `required` check passes. A `fail` on any `required`
check rejects the package. An `unclear` on a `required` check counts
as a `fail`: a plan the package cannot verify is not ready to build
from. `preferred` checks never change the verdict; they only say how
strong an accepted plan is.
