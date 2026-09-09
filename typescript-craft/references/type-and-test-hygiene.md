# Type and test hygiene

## Model the contract

Use discriminated unions for mutually exclusive modes and meaningful state transitions. Keep independent settings independent. Where every variant needs handling, use exhaustive checking so a new variant exposes missing cases.

Use explicit types for deliberate public contracts and inference for clear local values. Reuse an existing source of truth for a shape. Reserve `Record<string, unknown>` for genuinely dynamic records.

Named aliases improve vocabulary, but `type UserId = string` and `type OrderId = string` remain interchangeable. When that interchangeability must be rejected, use the project's established brand or wrapper pattern at the relevant boundary. A brand still needs a justified construction path.

Use `readonly` contracts when mutation is not part of the operation. They restrict writes through that typed reference; they do not freeze runtime objects, recursively protect nested values, or prevent mutation through another alias.

## Validate at trust boundaries

Treat untrusted JSON, requests, stored data without established guarantees, and external messages as unknown until checked. Use the project's existing parser or focused runtime checks, then expose the validated contract to internal code. Validate what the receiving operation relies on and define how invalid input is rejected or handled.

Type annotations, `as`, and non-null assertions do not perform runtime validation. Use narrowing backed by actual checks; an assertion or user-defined type predicate is only as sound as the evidence supporting it. Keep unavoidable assertions around a verified library limitation narrow and explain the invariant that makes them safe.

Reuse validation guarantees already established by the framework or a trusted internal caller. Trust boundaries need checks; crossing a file boundary alone does not require another parse. A small payload may need only a few checks, not a new schema dependency.

For example, treating `JSON.parse(text) as Order` as validation leaves malformed fields unchecked. Parse into `unknown`, check the fields and invariants required by the operation, and handle invalid JSON and invalid shapes through the existing error contract.

## Respect compiler and runtime semantics

Use the installed TypeScript version and the repository's module, import, and runtime conventions. Prefer strict checking for new projects. `noUncheckedIndexedAccess` and `exactOptionalPropertyTypes` add checks beyond `strict`; evaluate them when configuring a project, without turning an unrelated change into a compiler migration.

Handle nullable values, missing indexed entries, and optional fields according to their actual contracts, even where current compiler settings are permissive. Node's native type stripping and other transpilation-only workflows need a separate type check.

## Test meaningful behavior

Test through a module, API, command, or UI contract. Include invalid input and failure paths relevant to the change. Choose integration checks when behavior depends on a real adapter or framework; a fake alone cannot verify that integration.

Keep helpers private rather than exporting them solely to test implementation details. Mock genuine system boundaries when needed. Assert call order only when the order itself is required behavior.

Use fixtures or builders when they remove repeated, irrelevant setup. Keep fixtures focused on data and setup; exercise production code for behavior. Prefer focused assertions over snapshots that conceal the important result. Split test files by distinct behavior or module responsibility when that improves navigation.

## Sources

- [TypeScript, narrowing and exhaustive checks](https://www.typescriptlang.org/docs/handbook/2/narrowing.html)
- [TypeScript, aliases, inference, and assertions](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html)
- [TypeScript, readonly properties](https://www.typescriptlang.org/docs/handbook/2/objects.html#readonly-properties)
- [TypeScript, compiler options](https://www.typescriptlang.org/tsconfig/)
- [Node.js, TypeScript execution](https://nodejs.org/api/typescript.html#type-stripping)
