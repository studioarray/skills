# Type And Test Hygiene

Use this reference when a TypeScript change introduces new types, public interfaces, fakes, harnesses, or notable test structure.

## Types

Types should express real states and reduce caller knowledge.

Prefer:

- discriminated unions for meaningful modes
- explicit project-owned types for known shapes
- value objects or named types when primitives are easy to mix up
- narrow public interfaces with domain-shaped inputs and outputs
- `readonly` inputs when mutation is not part of the contract

Avoid:

- broad optional bags that represent several modes
- parallel booleans that can contradict each other
- `Record<string, unknown>` outside genuine dynamic data
- type aliases that only rename noise
- leaking SDK, ORM, transport, or database shapes across ownership boundaries

Use `unknown` for genuinely unknown external input. Narrow it promptly at the boundary and convert it to project-owned types before the data crosses inward.

## Exports

Exports are part of the module interface.

Export deliberately:

- public functions/classes/types callers are meant to use
- stable DTOs or view models that define a boundary
- factories/builders that reduce test or call-site noise

Keep private:

- helper functions used by one module
- formatting details
- mapping internals
- temporary compatibility code

If another module needs a helper, consider whether the helper is really a public concept or whether the caller should use a deeper module interface instead.

## Names

Use names that reveal ownership.

Prefer:

- domain and product terms
- verbs for commands and transformations
- nouns for state, projections, adapters, and value concepts
- names that say what policy is owned here

Avoid vague names unless the repo has a strong convention:

- `utils`
- `helpers`
- `manager`
- `service`
- `common`
- `misc`

## Tests

Tests should make public behavior easier to trust.

Prefer:

- behavior through module, API, UI, or command surfaces
- realistic builders and fixtures
- fakes at true system boundaries
- one focused behavior per test
- test files split around distinct modules or behavior clusters

Avoid:

- mocking internal collaborators just to assert call order
- testing private helpers directly
- giant setup repeated across tests
- harnesses that reimplement production behavior
- snapshot tests that hide the behavior being protected

When tests are painful, treat that as design feedback. The right fix may be a smaller public interface, a better fixture builder, or a clearer module boundary.
