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

<!-- What gets read, in what order, before any check is graded, and
what to note down from each part while reading. A complete procedure
decides the order (issue first? repro evidence first?) and says why
the order matters for the checks that come later. -->

1. Live mode only: read `scope.md`. Confirm the issue URL is in the
   scoped repo; if it is not, or the `Repo:` line is still a
   placeholder, stop without grading. Note the house rules.
2. Read `rubric.md` and `references/evidence-guide.md`. Write down
   the list of check names and the verdict rule.
3. Read the issue (title, body, labels). Note in one line the
   behavior the issue reports.
4. Read the thread highlights. Note every comment from an OWNER,
   MEMBER, or COLLABORATOR account and what it asks for or decides
   (an approach, a "discuss first", "working as intended", "the fix
   is hard"). Note any cause a non-maintainer claims.
5. Read the repo-facts block. Note every requirement the contribution
   policy states (AI disclosure, own-words comments, ask before PR,
   review limits). If it states none, note "no stated requirement".
6. Read the repro evidence. Note: the failing step, every control run
   (and what it changed), the "Expected" line, and the "Actual" line.
   Read this before the plan so the plan is judged against the
   evidence, not the other way round.
7. Only now read the candidate plan, then the candidate plan comment.

## Evidence gathering

<!-- For each evidence family your rubric's checks name, the concrete
gathering move: which part of the package (or, live, which page or
thread location per your evidence guide) to pull the fact from, and
what to record. A complete procedure leaves no check whose evidence an
executor would have to hunt for. -->

For each check, pull exactly these facts and quote them:

1. diagnosis-follows-evidence: quote the plan's cause sentence. Quote
   the repro step or control run that most directly tests that cause
   (the run with the blamed component removed, or the run without the
   blamed condition). Record whether the failing behavior was still
   present in that run.
2. fixes-the-cause: quote the plan's change steps. Record which
   mechanism they act on and whether it is the one the diagnosis
   names.
3. scope-is-bounded: list every file, area, and change the plan
   proposes. Mark each one "needed for the fix", "test/doc for the
   fix", or "other".
4. stranger-can-start: quote where the plan says the first edit goes
   and what it is. If there is no location, record "no location".
5. test-plan-observes-the-fix: quote the test plan. Record whether it
   re-runs the repro trigger and which observable result it names.
   Compare that result to the repro's "Expected" line.
6. certainty-is-honest: quote every sentence in the plan and comment
   that states a cause, an outcome, or a guarantee as settled. For
   each, record what in the repro evidence or thread supports it, or
   "nothing".
7. comment-fits-thread-and-repo: quote the comment's most specific
   sentence. List the maintainer notes from Read order step 4 and the
   policy requirements from step 5, and for each record whether the
   comment meets, ignores, or contradicts it.
8. risks-named: quote any risk or unknown the plan names, or record
   "none".

Live mode: the issue, thread, and repo facts come from GitHub (`gh
issue view <URL> --comments`, the repo's CONTRIBUTING and README);
the repro evidence is the student's posted repro comment, or the
repro evidence the drafts quote; the plan and comment are the drafts.
Eval mode: use only the bundle text.

## Check execution

<!-- How one check runs against gathered evidence: in what order the
checks execute, what an executor does when evidence for a check is
genuinely absent, and when a check may be graded without re-reading
the whole package. A complete procedure makes two executors grade the
same package the same way. -->

1. Grade the checks in rubric table order, one at a time, using only
   the facts recorded for that check in Evidence gathering.
2. Apply the check's pass condition literally. If the facts meet it,
   grade `pass`; if they meet a fail condition, grade `fail`.
3. Grade `unclear` only when the evidence the check needs is truly
   absent from the package (for example the bundle has no repro
   evidence block). Do not grade `unclear` because a judgment is
   hard; decide it.
4. A part of the plan that is missing is evidence, not absence: a
   plan with no test plan fails test-plan-observes-the-fix; a plan
   with no cause fails diagnosis-follows-evidence.
5. Grade every check even after a required check fails, so the
   student sees every problem.
6. Each grade gets one line of evidence: the quote or fact that
   decided it.

## Verdict assembly

<!-- How the per-check grades become the final accept or reject:
apply your rubric's verdict rule, state how unclear grades enter it,
and say what gets quoted in the output for the deciding check. A
complete procedure produces the same verdict from the same grades,
every time. -->

1. Count `unclear` on any required check as `fail`.
2. If every required check is `pass`, the verdict is `accept`.
   Otherwise it is `reject`.
3. Ignore preferred checks for the verdict.
4. In the summary, name the first failing required check and quote
   the evidence that decided it. In live mode, add any voice-guide
   rule the comment breaks.
5. End with the JSON block from SKILL.md, with every check listed in
   rubric order. Nothing after it.
