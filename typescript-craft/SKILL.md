---
name: typescript-craft
description: Use by default when implementing, refactoring, or reviewing backend TypeScript, Node TypeScript, or React TypeScript code. Guides maintainable first-pass code shape, DDD and hexagonal placement, React composition, TypeScript type hygiene, test surfaces, folder and file responsibility, and avoidance of monolithic mixed-responsibility files.
---

# TypeScript Craft

## Purpose

Write TypeScript that future maintainers will enjoy working in. Take the time to choose a shape you would be happy to maintain two years from now.

Passing tests are the floor. A good TypeScript change also leaves behind clear names, cohesive files, visible ownership, and module boundaries that make the next change easier.

When a TDD workflow is active, use this skill as the code-shape companion during implementation and refactor. The tests prove behavior; this skill keeps the resulting TypeScript pleasant to extend.

## Core Posture

- Treat maintainability as part of the feature, not polish after the feature.
- Make architecture notes true in the code's shape, not only in prose.
- Prefer explicit domain and product language over vague technical buckets.
- Keep responsibilities separate while they are still cheap to separate.
- Improve the code you are already touching when a small local refactor makes the change cleaner.
- Flag larger refactors as follow-up when they fall outside the task's natural scope.

## File And Module Shape

Prefer multiple cohesive files over one large file with a private-function village.

A file may be long when it owns one deep concept. Split it when it owns separate reasons to change:

- domain rules
- application or workflow orchestration
- external adapters
- transport, storage, SDK, or framework translation
- React state orchestration
- view-model shaping
- presentational rendering
- formatting or mapping that is reused across concepts

Colocation means cohesive locality. It does not mean everything related somewhere in your head belongs in one file.

Prefer folders named after domain or product concepts when the code is feature-shaped. Within a concept, split by responsibility using the repo's established style. Do not add more unrelated behavior to an already crowded file just because the file exists.

Give facades a budget. A facade may compose dependencies, but it should not quietly become the owner of several policies.

## Backend TypeScript

Put behavior where its concept lives.

- Keep domain rules away from framework, transport, storage, SDK, and React concerns.
- Let application code coordinate use cases instead of absorbing domain rules or adapter details.
- Let adapters translate outside-world shapes into project-owned types.
- Keep raw external shapes at the edge. Do not leak SDK, ORM, transport, or database DTOs into domain code.
- Use ports or interfaces when they protect a real boundary or make a policy testable.
- Make the ownership visible in file names, exports, and call direction.

For more detail, read [backend-ddd-hexagonal.md](./references/backend-ddd-hexagonal.md) only when a change touches domain rules, use-case orchestration, adapters, persistence, transport, SDK integration, or cross-package boundaries.

## React TypeScript

React code should be composed around ownership.

- Pages, routes, or containers own orchestration.
- Hooks own meaningful state workflows or effects.
- View-model modules shape raw data for rendering.
- Components render focused UI and emit events.
- Leaf-local UI state can stay local.
- Product areas deserve named components and files when they have independent reasons to change.

Prefer composition over configuration. If a component grows many optional props, boolean modes, or branches for unrelated flows, split it into focused components, explicit variants, slots, children, or a small provider.

For more detail, read [react-composition.md](./references/react-composition.md) only when a change touches React component structure, hooks, view models, component APIs, local state, or UI tests.

## Type And Test Hygiene

- Model meaningful states with discriminated unions instead of loose boolean clusters.
- Use `unknown` for genuinely unknown external input, then narrow promptly at the boundary.
- Use explicit project-owned types when the shape is known or owned by the project.
- Avoid broad optional bags when the caller actually has distinct modes.
- Export deliberate interfaces. Keep helpers private until another module genuinely needs them.
- Prefer precise names over `utils`, `helpers`, `manager`, or vague `service` names.

Tests should make public behavior easier to trust, not freeze private structure. If a test needs lots of setup or mocks internal collaborators, treat that as design feedback. Use builders and harnesses to reduce noise, but do not let harnesses become alternate implementations. Split huge test files when they cover distinct behaviors or modules.

For more detail, read [type-and-test-hygiene.md](./references/type-and-test-hygiene.md) only when a change introduces new types, public interfaces, fakes, harnesses, or notable test structure.

## Completion Standard

Before finishing, look at the code you changed as if you were the next maintainer.

- Does each changed file have one clear reason to exist?
- Did new behavior land near the concept that owns it?
- Are boundaries visible in names, files, and exports?
- Is any facade still thin?
- Are tests describing behavior through useful surfaces?
- Would the next adjacent change be easier because of this shape?

If the answer is no, keep refining within the task's scope.
