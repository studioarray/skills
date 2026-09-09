---
name: typescript-craft
description: Guide module boundaries, type safety, React composition, and behavioral tests when implementing, refactoring, or reviewing backend, Node.js, or React TypeScript code. Use for TypeScript code changes and design reviews, following the repository's architecture and the requested scope.
---

# TypeScript Craft

Write TypeScript with clear ownership, useful public interfaces, and behavior that can be verified. Choose structure for the change in front of you.

## Establish context and scope

Read repository instructions, the relevant implementation and callers, and nearby tests. Inspect package scripts, the TypeScript configuration and version, and the runtime or framework conventions that affect the change. Use the repository's existing libraries and patterns when they fit.

Preserve the requested mode:

- For implementation or refactoring, change code and resolve concrete maintainability issues within scope. Keep an active TDD workflow's test-first sequence.
- For reviews, report actionable findings with locations, consequences, and suggested fixes. Edit only when fixes are requested. Distinguish correctness problems from design preferences.
- For design advice, explain the boundary and tradeoff relevant to the user's decision.

## Choose cohesive boundaries

Keep behavior near the domain or product concept that owns it. Use names that identify the policy or operation; follow established terminology before inventing a new name.

Extract a module when it isolates an independently changing policy, hides a meaningful dependency, or supports actual reuse. Its interface should reduce what callers need to know. A cohesive file can be long; several short files can still expose too much detail.

Keep private helpers and simple local calculations together with their callers. When a file mixes unrelated policies, split around those policies. A coordinating module can own a workflow; move independently meaningful rules or external translation to the concept that owns them.

Export deliberate contracts. Keep implementation details private until another caller needs the concept. Add ports, wrappers, providers, or separate models only when they solve a concrete ownership, dependency, or reuse problem. Preserve intentionally shared contracts.

## Consult the relevant guidance

Read only the references relevant to the change:

- For domain rules, use-case orchestration, persistence, transport, SDKs, or package boundaries, read [backend boundaries](./references/backend-ddd-hexagonal.md).
- For React components, shared state, hooks, Effects, rendering, or UI tests, read [React composition](./references/react-composition.md).
- For types, untrusted input, public contracts, or test design, read [type and test hygiene](./references/type-and-test-hygiene.md).

## Verify and finish

Run the repository's applicable type check and relevant lint, tests, and build checks. Use its project-reference or framework workflow where configured. Successful transpilation or runtime tests alone do not establish type correctness.

Check that the requested behavior works, the changed code has clear ownership, and tests protect observable behavior. Resolve concrete issues within scope; report larger architectural concerns separately.

Finish with what changed or what the review found, the checks actually run, and any failures or verification limits. Stop when the requested work and its applicable checks are complete.
