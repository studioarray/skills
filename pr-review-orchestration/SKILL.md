---
name: pr-review-orchestration
description: Coordinate QA and fixes for a GitHub PR until merge readiness. Use with a PR URL, directly or from another implementation workflow.
---

# PR review orchestration

Invoke with `$pr-review-orchestration <PR URL>` or the same name written as a slash prompt. Other agents and skills may invoke it too.

Be the coordinator. Subagents handle review, implementation, and tests; you monitor and organize the work. This workflow includes PR comments, commits, and pushes. Stop at merge readiness unless the user separately asks to merge.

## Set up

Read the PR, linked requirements, discussion and inline comments, and CI results. Confirm its source branch and current base/head SHAs. Use a suitable checkout that preserves existing work, and follow the repository's instructions.

Use sub agents for QA, coding, and nested reviewers. Give agents the PR URL, checkout, reviewed SHAs, relevant requirements, and previous findings. Resolve the skills below from the current catalog so plugin updates do not break their paths. Report unavailable dependencies rather than silently changing the workflow.

Run QA and coding in successive phases. Leave room for the QA lead's two parallel `code-review` reviewers; reuse idle agents as needed. Keep enough progress notes to resume the loop without losing findings or validation results.

## QA brief

> Review this PR using `code-review` and `ponytail:ponytail-review`, including correctness and the latest regressions reported in comments. Use the supplied base SHA as the review's fixed point. Use linked issues, the PR description, and any supplied acceptance criteria as the spec. Missing tracker setup need not trigger setup work; report any missing spec and whether it prevents a useful verdict.
>
> Follow repository testing guidance and CI workflows to choose and run appropriate checks. Record local and CI results and any validation gaps.
>
> Post a consolidated PR comment with the reviewed base/head SHAs, actionable findings and evidence, optional suggestions, test results, and a clear pass, issues-found, or blocked verdict. Preserve the review skills' separate perspectives. Return the comment link and findings to the coordinator.

## Coding brief

> Fix the actionable QA findings using `ponytail:ponytail` and `codebase-design`. Follow repository testing guidance and CI workflows to validate the fixes. If a finding is disputed, return the evidence for resolution.
>
> Commit and push the fixes to this PR's source branch, preserving concurrent work. Add a PR comment describing what changed and how it was tested. Return the new commit SHA, comment link, and anything unresolved.

## Continue until ready

Give coding the actual latest QA report. After fixes are pushed, run QA again. Route technical disputes back to QA, and keep optional preferences from becoming endless rewrites.

Before declaring readiness, confirm QA covers the current base/head SHAs, required CI has passed, and no new regression reports or merge conflicts remain. Changed commits need renewed QA. Report outstanding human approvals or other merge restrictions separately.

Continue while making progress. If access, validation, a needed decision, or repeated unsuccessful fixes block the loop, report the evidence and next action rather than claiming success. Finish with the PR link, reviewed SHAs, QA/fix comment links, validation summary, and readiness or blockers.

When called from another skill, stay in the same coordinator and reuse its checkout, acceptance criteria, and progress notes. Return the review result to that workflow; it remains responsible for covering the whole issue scope.
