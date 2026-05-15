# CONTEXT.md Format

Shared-language artefacts are the durable handoff from grilling to PRD-writing. They should be easy for humans to read and stable enough for downstream skills to cite.

## Structure

```md
---
type: context
context_id: {kebab-slug}
title: {Context Name}
status: active
updated: {YYYY-MM-DD}
---

# {Context Name}

{One or two sentence description of what this context is and why it exists.}

## Language

**Order**:
{A concise description of the term}
_Avoid_: Purchase, transaction

**Invoice**:
A request for payment sent to a customer after delivery.
_Avoid_: Bill, payment request

**Customer**:
A person or organization that places orders.
_Avoid_: Client, buyer, account

## Conceptual boundaries

- **Customer** owns identity and contact details; other contexts should refer to customers by ID.
- **Invoice** is a billing concept, not an ordering concept.

## Relationships

- An **Order** produces one or more **Invoices**
- An **Invoice** belongs to exactly one **Customer**

## Constraints

- Invoices cannot be generated until fulfillment is confirmed.
- Customer-facing copy must use "Invoice", never "Bill".

## Example dialogue

> **Dev:** "When a **Customer** places an **Order**, do we create the **Invoice** immediately?"
> **Domain expert:** "No — an **Invoice** is only generated once a **Fulfillment** is confirmed."

## Ambiguities

- **resolved**: "account" was used to mean both **Customer** and **User**. Resolution: these are distinct concepts.
- **open**: "cancellation" may mean voiding an **Order** or refunding an **Invoice**. Needs domain-owner confirmation before PRD-writing.
```

## Frontmatter

- `type`: always `context`
- `context_id`: stable kebab-case identifier; references should use this, not the filename
- `title`: human-readable context name
- `status`: `active`, `draft`, `deprecated`, or `superseded`
- `updated`: last meaningful content update in `YYYY-MM-DD`

## Rules

- **Be opinionated.** When multiple words exist for the same concept, pick the best one and list the others as aliases to avoid.
- **Flag conflicts explicitly.** If a term is used ambiguously, call it out in "Ambiguities" with a clear `open` or `resolved` status.
- **Keep definitions tight.** One sentence max. Define what it IS, not what it does.
- **Show relationships.** Use bold term names and express cardinality where obvious.
- **Capture conceptual boundaries.** Record ownership, responsibility, and adjacent-but-distinct domain areas when they matter. Do not call these implementation modules.
- **Record constraints.** Include domain rules, non-negotiables, and external constraints that downstream PRD-writing must respect. If a constraint is a hard-to-reverse trade-off, consider an ADR instead.
- **Separate open from resolved ambiguity.** Use explicit `open` or `resolved` labels so downstream skills know whether they can proceed.
- **Only include terms specific to this project's context.** General programming concepts (timeouts, error types, utility patterns) don't belong even if the project uses them extensively. Before adding a term, ask: is this a concept unique to this context, or a general programming concept? Only the former belongs.
- **Group terms under subheadings** when natural clusters emerge. If all terms belong to a single cohesive area, a flat list is fine.
- **Write an example dialogue.** A conversation between a dev and a domain expert that demonstrates how the terms interact naturally and clarifies boundaries between related concepts.

## Single vs multi-context repos

**Single context (most repos):** One `CONTEXT.md` at the repo root.

**Multiple contexts:** A `CONTEXT-MAP.md` at the repo root lists the contexts, where they live, and how they relate to each other:

```md
---
type: context-map
context_map_id: context-map
status: active
updated: {YYYY-MM-DD}
---

# Context Map

## Contexts

- `ordering` — [Ordering](./src/ordering/CONTEXT.md): receives and tracks customer orders
- `billing` — [Billing](./src/billing/CONTEXT.md): generates invoices and processes payments
- `fulfillment` — [Fulfillment](./src/fulfillment/CONTEXT.md): manages warehouse picking and shipping

## Relationships

- **Ordering → Fulfillment**: Ordering emits `OrderPlaced` events; Fulfillment consumes them to start picking
- **Fulfillment → Billing**: Fulfillment emits `ShipmentDispatched` events; Billing consumes them to generate invoices
- **Ordering ↔ Billing**: Shared types for `CustomerId` and `Money`

## Open questions

- **open**: Whether customer credit limits belong to `billing` or a separate risk context.
```

The skill infers which structure applies:

- If `CONTEXT-MAP.md` exists, read it to find contexts
- If only a root `CONTEXT.md` exists, single context
- If neither exists, create a root `CONTEXT.md` lazily when the first term is resolved

When multiple contexts exist, infer which one the current topic relates to. If unclear, ask.
