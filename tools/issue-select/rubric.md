# Rubric: is this a good first issue?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever checks
you define here. It ships empty on purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where. Name the source
     (repo-facts block, issue body, comment thread, or the locations in
     references/evidence-guide.md). "The repo" is not a source; "the last
     5 default-branch commit dates" is.
   - Pass condition: a condition someone else could apply and get your
     answer. Prefer thresholds with numbers ("a maintainer commented
     within 30 days") over adjectives ("maintainer is responsive").
   - Weight: `required` (a fail here rejects the issue) or `preferred`
     (never changes the verdict; a nice-to-have that helps rank the
     issues you accept).

2. A verdict rule below the table: how the check grades combine into
   accept or reject, including how `unclear` is treated. The verdict
   space is binary. If you write no rule for `unclear`, the skill treats
   it as fail.

Cover what actually kills first contributions. The lecture named four
families: the maintainer is alive, the repo is in use, the scope fits a
newcomer, and nobody else is already on it. A rubric that ignores a family
will fail eval issues designed around that family.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Maintainer Alive? | "last 5 default-branch commits" under Repo facts; commit author names in the same list; "maintainer first-response sample" under Repo facts; the Comments section: each comment shows its author_association | Passes if a default-branch commit dated within 30 days of the capture date landed a pull request (subject contains "Merge pull request" or a "(#1234)" reference). The committer need not be separately confirmed as a maintainer — landing a PR on the default branch is maintainer action. | Required |
| Unclaimed? | "this issue: assignees:" under Repo facts; "linked PRs:" with state per PR, plus any PRs mentioned in the Comments section; the Comments section; issue open date vs capture date | Nobody has been assigned this issue | Required |
| Repo Alive? | "last push to any branch" under Repo facts; "latest release" under Repo facts; "archived:" on the repo line; stars on the repo line | There has been a push to any branch within the last 30 days | Required |
| Scope Fits? | Issue body and thread; labels such as `good first issue`; closed PRs linked from the issue | Passes if the issue asks for one coherent outcome, even when that spans several files, steps, or numbered sub-points. Fails if any of these hold: it is explicitly an umbrella or tracking issue whose sub-items are meant to be split into separate issues or PRs; the design is still being debated and no maintainer has settled it; a maintainer says the fix touches core internals; it is a pure usage question rather than a contribution; it has been open for years with several closed, unmerged PRs; it is a feature request whose premise is still an open product decision — no maintainer has endorsed it (no maintainer comment, no label accepting it) and key specifics are still undecided (for example "TBD", or no agreed spec);. Length, formatting, a numbered list, or touching multiple files are not by themselves grounds to fail. | Required |
| Allowed to Contribute? | the "contribution policy" line under Repo facts; quoted or summarized on the same line; the same line | Fails if the contribution policy explicitly bans AI-generated or AI-assisted contributions. Passes in every other case, including when the policy is silent on AI tooling and when it only sets conditions (disclosure, human review, testing, understanding your own contribution). | Required |

## Verdict rule
Accept if every required check passes and if it is unclear, just count it as a fail. Preferred checks never change the verdict.
<!-- State how the grades above combine into accept or reject, and how
unclear is treated. Example shape (write your own): "accept if every
required check passes; preferred checks never change the verdict, they
rank accepted issues; unclear counts as fail." -->
