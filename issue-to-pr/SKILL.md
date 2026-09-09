---
name: issue-to-pr
description: Coordinate implementation of a GitHub issue and its sub-issues through a PR, then use pr-review-orchestration for QA and fixes. Use with an issue URL when a reviewed implementation is wanted.
---

# Issue to PR

Invoke with `$issue-to-pr <GitHub issue URL>` or the same name written as a slash prompt. Other agents and skills may invoke it too.

Be the coordinator. Delegate technical investigation, implementation, and tests. This workflow includes creating or updating a PR, commits, pushes, and review comments. Stop at merge readiness; merging or manually closing issues needs a separate user instruction.

## Understand the work

Read the issue, comments, actual sub-issues and their descendants, and relevant linked PRs. Treat incidental issue mentions as context. Establish the acceptance criteria, dependencies, and work already completed. Report gaps if you cannot discover the child issues.

Prefer one PR for the issue tree, reusing a suitable existing PR when possible. Split the work when repository structure or dependencies call for it. Use a suitable branch and checkout that preserve existing work, and follow repository instructions.

Resolve `ponytail:ponytail`, `codebase-design`, and `pr-review-orchestration` from the current skill catalog. Keep brief notes on issue coverage, branches, PRs, tests, and blockers so the review handoff retains the original scope.

## Delegate implementation

Use GPT-6 Astra medium: `model="gpt-6-astra"`, `reasoning_effort="medium"`, `fork_turns="none"`. Start with one implementation agent and give it the issue scope, acceptance criteria, checkout, branch, repository instructions, and skill paths. Report unavailable tools or model access instead of taking over the coding yourself.

> Implement this issue and its in-scope sub-issues using `ponytail:ponytail` and `codebase-design`. Investigate existing behavior, work through dependencies, and keep the changes focused on the requirements.
>
> Follow repository testing guidance and CI workflows to validate the implementation. Report material ambiguity, blocked dependencies, or validation gaps, and continue independent work where possible.
>
> Commit and push to the assigned branch, preserving concurrent work. Return what was implemented against the acceptance criteria, commit SHA, test results, and anything incomplete.

## Open the PR and review it

Create or update the PR with issue links, a description of the implemented behavior, and validation results. Make partial work explicit. If everything was already implemented, report the evidence and existing PRs instead of creating an empty one.

Once implementation covers the intended scope, invoke `pr-review-orchestration` in this same coordinator. Pass the PR URL, checkout, full acceptance criteria, implementation report, and test history. Reuse available agents and leave room for QA's two nested reviewers. Let that skill own the review/fix loop. Run it for each PR if the work required several.

Account for the root issue and all in-scope children before finishing. A passing PR review alone does not establish full issue coverage. Send missing requirements back for implementation and renew QA after changes. Stop with a concrete blocker if progress stalls or a decision is needed.

Finish with issue coverage, PR links, validation, and merge readiness or remaining actions. Distinguish implemented work from merged and resolved issues.
