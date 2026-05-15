---
name: tdd
description: Implement one approved slice or bugfix using test-driven development with a red-green-refactor loop. Use when the user asks to build from a slice markdown file, implement a docs/issues slice, fix a bug test-first, use TDD, red-green-refactor, tracer-bullet implementation, or behavior-first tests.
---

# Test-Driven Development

## Your role in the chain

This skill is step 5 in a five-step planning-and-implementation chain:

1. **Plan** - rough feature description (informal, no skill required)
2. **Grill** - stress-test the plan, sharpen terminology, capture decisions
3. **PRD** - modules, user stories, acceptance criteria, scope boundaries
4. **Slice into issues** - vertical tracer-bullet decomposition
5. **TDD (this skill)** - implement one approved slice at a time

Everything outside step 5 is out of scope. Do not re-grill the problem space. Do not rewrite the PRD. Do not redefine modules. Do not split or reorder slices unless the user explicitly asks. Your job is to take one buildable unit of work and drive it to tested, working code.

## Source of truth

Prefer a slice artefact produced by `to-issues`, usually `docs/issues/<slice_id>.md`.

Read the slice frontmatter and body before editing code:

- `slice_id`
- `parent`
- `slice_type`
- `modules_touched`
- `acceptance_criteria`
- `blocked_by`
- "What to build"
- "Acceptance criteria"

If the parent PRD is referenced and available, read only the relevant sections: Modules, User Stories and Acceptance Criteria, Implementation Decisions, Testing Decisions, and Out of Scope. Also read relevant shared-language docs and ADRs so tests use the project's vocabulary and respect recorded decisions.

If no slice exists, this skill can still handle a small bugfix or feature request, but keep the TDD plan narrow: identify the public behavior, write one failing test, make it pass, refactor, and stop. Do not backfill PRD or slicing work.

## Slice status

If working from a local markdown slice with frontmatter, preserve all existing fields and update only `status`:

- Move `ready` to `in_progress` when beginning implementation.
- Move `in_progress` to `done` only after tests and verification pass.
- Move to `blocked` only when the slice cannot proceed without a specific missing decision, dependency, or acceptance-criteria clarification.

If `blocked_by` references unfinished slices, stop and ask whether to implement the blocker first or override the dependency. Do not silently ignore dependency order.

## HITL and AFK

- For an `AFK` slice, proceed unless the slice is missing essential information.
- For a `HITL` slice, inspect why it is HITL. Ask only for the named human decision or judgement call. After that decision is resolved, continue normally.
- If the slice is HITL only because the acceptance criteria are vague, say that the slice should go back to PRD/slicing for clarification instead of inventing behavior.

## Test philosophy

Tests should verify behavior through public interfaces, not implementation details. Good tests exercise real code paths through the same API, UI, command, route, or module surface that callers use. They describe what the system does, not how it is arranged internally.

Avoid tests that mock internal collaborators, call private methods, assert internal call order, or verify state through a back door. A test that fails after a harmless refactor was probably coupled to implementation.

Read [tests.md](./tests.md) for test examples and [mocking.md](./mocking.md) when a system boundary needs a fake.

## Anti-pattern: horizontal TDD

Do not write every test first and then write all implementation. That turns RED into "speculate about all tests" and GREEN into "fill in a batch of code."

Use vertical tracer bullets instead:

```text
WRONG:
  RED:   test1, test2, test3, test4
  GREEN: impl1, impl2, impl3, impl4

RIGHT:
  RED -> GREEN: test1 -> impl1
  RED -> GREEN: test2 -> impl2
  RED -> GREEN: test3 -> impl3
```

Each test should respond to what the last cycle revealed.

## Workflow

### 1. Orient

Find the relevant test commands, test style, fixtures, and nearby examples. Prefer the repository's existing test framework and naming conventions.

Before writing tests, identify:

- The slice or bug being implemented
- The public interface to exercise
- The acceptance criteria or observed bug behavior to prove
- The smallest first tracer bullet
- Any system boundaries that need controlled fakes

If this information is already in the slice, do not ask the user to confirm it again. Ask only when the source artefact is missing, contradictory, blocked, or outside the current repo's visible behavior.

### 2. Red

Write exactly one failing test for one observable behavior. Run the narrowest relevant test command and confirm that it fails for the expected reason.

If the test passes immediately, inspect whether behavior already exists. Do not write implementation just to satisfy the ritual.

### 3. Green

Write the smallest production change that makes the current test pass. Do not implement future acceptance criteria early. Run the narrow test again.

If the implementation exposes a bad interface, prefer a small interface adjustment over adding brittle test setup. Use [interface-design.md](./interface-design.md) and [deep-modules.md](./deep-modules.md) as guides.

### 4. Repeat

Move through the slice one behavior at a time:

```text
RED:   next behavior fails for the expected reason
GREEN: minimal code makes it pass
```

Keep each cycle tied to a specific acceptance criterion from the slice where possible.

### 5. Refactor

Only refactor when tests are green. Look for duplication, shallow modules, awkward interfaces, and existing code the new behavior reveals as problematic. See [refactoring.md](./refactoring.md).

Run tests after each meaningful refactor.

### 6. Verify the slice

Before finishing:

- Run the targeted tests for the changed area.
- Run broader tests, lint, typecheck, or build when the repository makes those commands clear and the blast radius justifies it.
- Confirm every acceptance criterion claimed by the slice is implemented or explicitly call out any remainder.
- If working from a markdown slice, update its `status` according to the slice status convention above.

Do not mark unrelated slices or parent PRDs complete.

## Per-cycle checklist

```text
[ ] Test describes behavior, not implementation
[ ] Test uses a public interface
[ ] Test fails for the expected reason before implementation
[ ] Code is minimal for this behavior
[ ] No future acceptance criteria were implemented speculatively
[ ] Tests are green before refactoring
```

## Final report

Report the slice or behavior implemented, the tests added or changed, commands run, and any acceptance criteria not completed. Keep it concise and concrete.
