---
name: orchestrate-issues
description: Sequentially orchestrate implementation of multiple markdown issue files, especially docs/issues slices, by launching subagents for implementation and independent checking, updating issue status, committing one clean issue at a time, and cleaning up agents between issues. Use when the user asks Codex to orchestrate, run, execute, or implement a queue of issues with subagents and per-issue commits.
---

# Orchestrate Issues

## Role

Act as the orchestrator for a queue of issue files. Do not implement the issues yourself, do not perform the review yourself, and do not reshape the issue list. Your job is to sequence the work, steer subagents, maintain issue status, commit completed issues, clean up agents, and stop with a useful report when the run needs human input.

This skill is a continuation of the planning-and-implementation process:

- **Plan** - rough feature description
- **Grill** - stress-test and sharpen
- **PRD** - modules, user stories, acceptance criteria, scope boundaries
- **Slice into issues** - vertical tracer-bullet decomposition
- **TDD** - implement one approved slice at a time
- **Orchestrate issues (this skill)** - run multiple ready issues through implementation, checking, and commits

## Core Invariants

- Process one issue at a time.
- Launch one implementation agent, then one independent checker agent for the current issue.
- Do not start the next issue until the previous issue has a successful commit and the worktree is clean.
- Own only issue workflow status; do not edit issue content, acceptance criteria, or implementation details.
- Commit exactly one completed issue at a time.
- Close agents for an issue after the issue is committed or abandoned.
- Completing one issue is not a successful run if more eligible issues remain in the requested scope.
- If you suspect a problem, launch or reuse an agent to investigate or fix it instead of doing the substantive work yourself.

## Issue Scope

Default to `docs/issues/*.md` unless the user names another issue directory or specific files. Read the issue files and process eligible issues in the order that best respects filename order, `blocked_by`, and `status`.

Treat an issue as eligible when it is `status: ready` and its blockers, if any, are already done. Skip or report other statuses using judgment; do not turn ordinary queue bookkeeping into ceremony.

The default run target is all eligible ready issues in scope. If the user says "next ready issue" or similar, interpret that as the starting point of the queue and keep going after each successful commit. Process only one issue when the user explicitly says to run a single issue, just the next issue, or names a specific issue.

After each successful commit, re-read the issue files before choosing the next issue. A completed issue may unblock more ready work.

If the user names a specific issue, range, or subset, process only that subset.

## Worktree Guardrails

Before starting, check the worktree. If there are pre-existing uncommitted changes, stop and ask the user unless the user explicitly asked to resume this orchestration and the dirty state is clearly part of that run.

During an issue, dirty state is expected. After a successful commit, require a clean worktree before launching the next issue.

Operate on the current branch. Do not create, switch, or push branches unless the user explicitly asks.

## Status Ownership

The orchestrator owns the issue `status` field. Do not ask implementation agents or checker agents to edit issue status or workflow metadata.

Use only these status transitions:

- `ready -> in_progress` before launching the implementation agent.
- `in_progress -> review` after the implementation agent reports done.
- `review -> in_progress` when checker feedback requires changes.
- `review -> done` after the checker reports pass and just before committing.
- `in_progress` or `review -> blocked` only when stopping the run on a blocker.

Only update `status`. Do not add commit SHAs, timestamps, attempt logs, summaries, or other run metadata to issue files unless the user explicitly asks.

When prompting implementation agents, include a short note:

```text
The orchestrator owns issue status updates. The issue file may already be touched; do not edit its status or workflow metadata.
```

## Implementation Agent

Launch an implementation agent for the current issue after setting it to `in_progress`.

For `docs/issues/*.md` slices, ask the implementation agent to use the TDD skill by default unless the issue or repository conventions clearly point elsewhere. Tell the agent to implement only the current issue and return a compact report with:

- status: `done` or `blocked`
- changed files
- tests/checks run
- notes or blockers

If the issue touches backend TypeScript, Node TypeScript, or React TypeScript, also ask the implementation agent to use the `typescript-craft` skill as a companion to TDD. The worker should keep first-pass code maintainable: avoid private-function villages, preserve ownership boundaries, split product/domain responsibilities when they have separate reasons to change, keep TypeScript types honest, and flag larger refactors that fall outside the issue scope.

Tell the implementation agent to preserve any architecture notes in the issue file. If the slice touches UI, state orchestration, external adapters, or domain logic, it should avoid collapsing the whole feature into one monolithic file and should use the repo's existing composition patterns.

If the agent is blocked by missing project dependencies, let it install project-local dependencies from repository lockfiles when appropriate. For global tools, network installs, or sandbox escalation, follow the normal permission flow. If the dependency is clearly required for project success, do not abandon the issue merely because installation is inconvenient; escalate or ask the user as needed.

## Checker Agent

After the implementation agent reports done, set the issue to `review` and launch an independent checker agent. The checker should verify:

- the issue's acceptance criteria are satisfied
- relevant tests/checks pass, or unrun checks are explicitly justified
- the changed files are scoped to the current issue
- issue status/workflow metadata was not edited by the implementation agent
- architecture notes are honored, and the implementation does not introduce avoidable monolithic files, mixed-responsibility React components, broad boolean prop surfaces, or poorly isolated external adapters
- for backend TypeScript, Node TypeScript, or React TypeScript changes, the implementation follows `typescript-craft`: cohesive files, clear ownership, appropriate domain/application/adapter/UI placement, focused types, and behavior-oriented tests

Ask the checker for a clear verdict:

- `pass`
- `needs_changes`
- `blocked`

Treat vague approval as insufficient; ask for a concrete verdict before committing.

## Retry Loop

If the checker returns `needs_changes`, set the issue back to `in_progress` and send the feedback to the implementation path. Prefer the same implementation agent for the first follow-up because it already has task context. Use a fresh implementation agent when the same path is stuck or confused.

After each revision, run an independent check again.

Stop after three failed attempts on the same issue. A failed attempt includes checker `needs_changes`, checker `blocked`, or a commit failure that requires another fix cycle. When the third failure happens, set the issue to `blocked`, close agents that are no longer useful, and report the problem to the user with enough context to resume.

## Commit

Commit only after the checker returns `pass`.

Before committing:

1. Set the issue status to `done`.
2. Stage the implementation changes and the issue status change for the current issue.
3. Commit with a message that names the issue id or title.

If commit hooks, formatters, lint-staged, tests, or other commit-time checks fail, treat the failure as part of the current issue. Launch or reuse an agent to fix the issue-scoped cause, then run the checker again before retrying the commit. Follow the dependency escalation policy for missing tools.

Do not commit partial work for a blocked issue unless the user explicitly asks.

## Agent Cleanup

After a successful commit, close the implementation and checker agents for that issue. Do not keep completed issue agents open while processing later issues.

If an issue is abandoned or blocked, close any agents that are not needed for immediate diagnosis before reporting to the user.

## Final Report

At the end of a successful run, summarize completed issues and commits. Since tokens are no longer scarce at the end, include useful context from agent reports: checks run, notable files changed, and any caveats.

If the run stops early, report:

- issue path and status
- attempt count
- implementation and checker verdicts
- failed commands or missing dependencies
- what was tried
- current worktree state
- what decision or action is needed from the user
