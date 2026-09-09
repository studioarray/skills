# Backend boundaries

## Match the architecture to the behavior

DDD focuses on the domain model, collaboration with domain practitioners, and a shared language within bounded contexts. Hexagonal architecture separates an application's behavior from its external integrations. Neither requires a particular folder tree.

Follow the repository's existing boundaries. For a simple CRUD operation or health endpoint, a direct handler using the existing persistence API may be sufficient. Introduce a domain model or mapping when it protects a meaningful invariant or isolates a dependency. A separate type that merely copies an intentional shared contract adds maintenance work.

## Place rules and orchestration

When the application has domain rules, keep invariants, calculations, and state transitions in domain-shaped functions, classes, or modules. Express them in project language and make them testable without HTTP requests, database clients, or SDK sessions.

Application code coordinates the use case: obtain inputs, call domain behavior, persist results, and publish events. Keep a business rule with its domain owner even when the application decides when to call it. Preserve the existing transaction and failure semantics when moving code across boundaries.

Adapters translate external formats and errors into contracts the application understands. Keep infrastructure-specific types out of domain policy. Reuse an intentionally shared DTO when it already is the appropriate contract; translate when the external representation and domain meaning differ. For untrusted input, apply the runtime validation guidance in [type and test hygiene](./type-and-test-hygiene.md#validate-at-trust-boundaries).

For example, an HTTP handler can parse a refund request and invoke the use case. The refundable-amount calculation belongs with refund policy; the payment SDK call belongs at the integration boundary. This does not require an interface for every function.

## Introduce ports deliberately

A port earns its place when callers need a stable contract for an external dependency or a policy must run independently of infrastructure. A function parameter can be enough; use an interface when several related operations form a real contract.

Test domain rules through their public behavior. Test adapters where translation, persistence, or failure semantics matter. Fakes should implement the same relevant contract as production; add integration checks where a fake cannot establish that contract.

## Sources

- [Eric Evans, DDD reference](https://www.domainlanguage.com/wp-content/uploads/2016/05/DDD_Reference_2015-03.pdf)
- [Alistair Cockburn, hexagonal architecture](https://alistair.cockburn.us/hexagonal-architecture)
