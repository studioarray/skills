# Backend DDD And Hexagonal Placement

Use this reference when backend TypeScript touches domain behavior, use-case orchestration, external systems, persistence, transport, SDKs, or package boundaries.

## Placement Discipline

DDD and hexagonal architecture are placement disciplines. The goal is not a fixed folder tree. The goal is that behavior lives with the concept that owns it, and outside-world details stay at the edge.

Good backend TypeScript usually has these pressures:

- Domain rules should be expressed in project language and testable without frameworks.
- Application code should coordinate a use case and call domain behavior.
- Adapters should translate external systems into project-owned shapes.
- Transport and persistence DTOs should not become domain models by accident.
- Shared protocol packages should contain stable contracts, not application orchestration.

## Domain Code

Domain code owns invariants, state transitions, calculations, policy rules, and vocabulary that business/product maintainers would recognize.

Domain code should not import:

- HTTP framework request or response objects
- database clients, ORM models, or query builders
- queues, WebSocket objects, or SDK sessions
- React or browser APIs
- transport DTOs unless those DTOs are intentionally the domain contract

Prefer small public functions, classes, or modules that accept domain-shaped inputs and return domain-shaped results.

## Application Code

Application code coordinates a use case.

It may:

- load inputs through ports or adapters
- call domain behavior
- decide command acceptance or workflow order
- publish project-owned results or events
- handle errors in terms the application owns

It should not quietly absorb raw SDK mapping, storage schemas, rendering logic, or low-level transport rules.

## Adapters

Adapters translate between the outside world and project-owned types.

Examples:

- HTTP handlers translate requests into commands and responses.
- WebSocket modules translate socket events into protocol commands and frames.
- Persistence modules translate rows/documents into domain or application shapes.
- SDK adapters translate raw SDK events into project-owned events.

Keep raw external data local to the adapter. If a shape is genuinely unknown, use `unknown` at the adapter boundary and narrow promptly.

## Interfaces And Ports

Use an interface or port when it protects a real boundary:

- an external system can fail or vary
- a policy needs to be tested without the real dependency
- production and tests need different concrete adapters
- a package boundary needs a stable public contract

Do not make an interface just to wrap every helper. A boundary should reduce knowledge for callers or localize change.

## Structure Heuristics

Prefer names from the domain or product context:

- `agent-target-runtime` is clearer than `status-utils`
- `conversation-feed-projection` is clearer than `mapper`
- `agent-session-adapter` is clearer than `service`

Split files when separate concepts have separate change pressure. Keep files together when a module is deep and cohesive.

Good signs:

- a caller can understand the public interface without reading the implementation
- raw external shapes stop at an adapter
- domain behavior can be tested without infrastructure
- application orchestration reads like a use case
- adding another adapter would not require editing domain code

Warning signs:

- framework types appear in domain files
- adapter details leak through public return types
- `service` or `manager` files own several unrelated policies
- a protocol package starts coordinating runtime behavior
- tests need to mock several internal collaborators to prove one behavior
