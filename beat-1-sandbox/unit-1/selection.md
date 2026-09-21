# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/61

**Verdict output**

All five required checks pass on the live evidence.

- `Maintainer Alive?` **pass** — most recent default-branch commit `2026-09-16` (`chore: track five more manifest entries against the tracker`, by `Aburke225`), 5 days before the capture date 2026-09-21
- `Unclaimed?` **pass** — `assignees: NONE`, 0 comments, no linked PRs
- `Repo Alive?` **pass** — last push to any branch `2026-09-16T21:48:27Z`, 5 days before capture; `archived: false`
- `Scope Fits?` **pass** — one coherent outcome: a single probe in `api/routes/health.py` with the fix named in the body and a reproduction; labelled `bug`, `good first issue`, `api`, `tier-1`; no umbrella structure, no unsettled design, no open product decision
- `Allowed to Contribute?` **pass** — `docs/CONTRIBUTING.md` contains no statement on AI, generative tooling, or generated contributions (silence passes)

Verdict rule: every required check passes, nothing graded `unclear` → **accept**.

**The verdict must record `accept` for this issue.** Choose an issue your own skill
accepts. If your skill rejects every candidate you try, that is a signal about your
rubric rather than about the issues: revise it and re-run - retries are unlimited and a
partial re-run costs about $0.20 - or run the skill on different candidates. Output
recording `reject` for the issue you chose earns no credit for this field.

```
{
  "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/61",
  "checks": [
    {"name": "Maintainer Alive?", "grade": "pass", "evidence": "Most recent default-branch commit 2026-09-16 by Aburke225 ('chore: track five more manifest entries against the tracker'), 5 days before capture date 2026-09-21"},
    {"name": "Unclaimed?", "grade": "pass", "evidence": "assignees: NONE; 0 comments; no linked PRs"},
    {"name": "Repo Alive?", "grade": "pass", "evidence": "Last push to any branch 2026-09-16T21:48:27Z, 5 days before capture; archived: false"},
    {"name": "Scope Fits?", "grade": "pass", "evidence": "One bug in api/routes/health.py with the fix named in the body ('SQLAlchemy 2.x requires textual SQL to be wrapped in sqlalchemy.text()') and a reproduction given; labelled good first issue, tier-1"},
    {"name": "Allowed to Contribute?", "grade": "pass", "evidence": "docs/CONTRIBUTING.md contains no statement on AI, generative tooling, or generated contributions"}
  ],
  "verdict": "accept"
}
```

---

## Eval iterations

Quote source text directly in each field below. Paraphrase does not satisfy them.

**Run history**

In order:

1. `--limit 3` — no score; 2 of 3 items errored. Not a rubric result: `run_eval.py` passed the prompt to `claude` with `text=True` and no `encoding=`, so Python used Windows cp1252 and the writer thread died on `UnicodeEncodeError: 'charmap' codec can't encode character '✨'`. Fixed by adding `encoding="utf-8"`.
2. `--limit 3` — **2/3**
3. `--limit 3` — **3/3**
4. `--only issue-05,issue-10,issue-15,issue-20` — **4/4**
5. `--only issue-12` — **1/1**
6. full run — **13/20**
7. `--only issue-04,issue-06,issue-09,issue-11,issue-14,issue-01,issue-19` — **5/7**
8. full run — **17/20**
9. full run — **19/20**
10. full run — **19/20** ← the committed `eval-run.txt`

**Issue analysis**

`issue-18`. My rubric decided **accept**; the gold label is **reject** (category `claimed`, gold note: "no assignee, but open linked PRs and a stack of claim comments").

My rubric produced `accept` because the pass condition of my `Unclaimed?` check reads only:

> Nobody has been assigned this issue

The bundle's repo-facts line begins `this issue: assignees: none`, so the check passed on the first clause it was told to test. The same line continues with claims the condition never asks about:

> this issue: assignees: none; linked PRs: excalidraw/excalidraw#9685 (open); excalidraw/excalidraw#10371 (open); excalidraw/excalidraw#11021 (closed); excalidraw/excalidraw#11460 (closed); excalidraw/excalidraw#11656 (open); LennyMalcolm0/excalidraw#314 (open)

plus an 11-comment thread with MEMBER and COLLABORATOR participation. I had already widened this check's Evidence column to include "linked PRs:" and the Comments section after `issue-03` failed the same way, but I widened the evidence without widening the pass condition. That was enough for `issue-03` and `issue-13`, where the grader read the fuller evidence list and inferred the intent, and not enough for `issue-18`.

**Check rationale**

`Maintainer Alive?`, quoted as currently written in `tools/issue-select/rubric.md`:

> Passes if the most recent default-branch commit is dated within 30 days of the capture date. Any commit counts - landing a pull request and pushing directly to the branch both show maintainer action; the committer need not be separately confirmed as a maintainer.

It reached this form through two revisions, each forced by a specific failure.

It began as "A maintainer has done a commit or comment within the last 30 days". In one full run that wording graded `issue-01` and `issue-09` differently even though both bundles are conda/conda with an identical repo-facts block - `issue-01` passed and `issue-09` failed. The bundles never label which committers are maintainers, and the maintainer first-response sample for that repo is weak (one response at 32.9 days, four threads with no maintainer comment), so the condition was asking for a judgment the evidence could not settle and the grade was effectively a coin flip.

Replacing the judgment with a date comparison removed the inconsistency. The clause about the committer not needing to be confirmed as a maintainer is there deliberately: it is the part that stops the check from sliding back into the unanswerable question.

**Trade-offs**

[What the quoted check gives up. Any one of these is a complete answer: an issue whose
result it changes, a canary you re-ran with `--only`, a case you accept it will miss, or a
stated reason nothing changed elsewhere. "Nothing changed, and here is how I know" earns
the point in full when the reason follows.]

---

## Selection rationale

An issue whose result it changes - three of them, in live mode.

The intermediate version of this check read:

> Passes if a default-branch commit dated within 30 days of the capture date landed a pull request (subject contains "Merge pull request" or a "(#1234)" reference). The committer need not be separately confirmed as a maintainer — landing a PR on the default branch is maintainer action.

That scored 19/20 on the eval set, and then rejected **every** live candidate I ran it on (#62, #61, #72). Path Review is maintained by direct pushes to `main`: its three most recent default-branch commits are `chore:` pushes dated `2026-09-16`, five days before capture, and the last commit whose subject contains "Merge pull request" is `2026-07-18`, 65 days earlier. A maintainer was plainly active; the check was keyed to a commit-message convention this repo does not use. Dropping the PR-landing requirement flipped all three candidates from `reject` to `accept`.

What the current wording gives up is the ability to tell those two situations apart. A repo where maintainers review and merge community pull requests and a repo where one owner pushes chores to `main` now score identically on this check, so it can pass a project where no outside contribution ever gets reviewed. I checked that this did not cost me anything on the eval set: the three `dead-repo` items have newest default-branch commits of `2023-12-09` (issue-02), `2025-02-10` (issue-07) and `2021-11-13` (issue-17) against a capture date of `2026-08-05`, all years outside a 30-day window, so no dead repo is rescued by the looser rule. The full run after the change scored 19/20 with `dead-repo 3/3` intact.

**Selection rationale**

Answer all three:

1. The issue's fit to your interests and to the time available.
Issue #61 is a FastAPI route plus the SQLAlchemy 2.x `text()` idiom, against the Python/FastAPI focus in your scope.md profile. It is labelled `tier-1`.
2. What the verdict identified correctly, and what you weighed that the rubric could not.
The rubric confirmed liveness, claim state, scope and policy. It has no way to judge whether SQLAlchemy 2.x is worth more of your learning time than the passlib error handling in #72, or how much a seeded course bug will actually teach you.
3. The anticipated difficulty in claiming it.
A bit hard

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
