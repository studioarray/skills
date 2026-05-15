# ADR Format

ADRs are durable records of decisions that downstream skills can cite. Keep them short, but give each one stable frontmatter.

ADRs live in `docs/adr/` by default and use sequential numbering: `0001-slug.md`, `0002-slug.md`, etc. In a multi-context repo, ADRs may live near the relevant context if that is the repo's convention.

Create the `docs/adr/` directory lazily — only when the first ADR is needed.

## Template

```md
---
type: adr
adr_id: ADR-0001
title: {Short title of the decision}
status: accepted
created: {YYYY-MM-DD}
context_id: {context_id or system}
---

# {Short title of the decision}

{1-3 sentences: what's the context, what did we decide, and why.}
```

That's it. An ADR can be a single paragraph after the frontmatter. The value is in recording *that* a decision was made and *why* — not in filling out sections.

## Frontmatter

- `type`: always `adr`
- `adr_id`: stable identifier, usually `ADR-NNNN`; references should use this, not the filename
- `title`: human-readable decision title
- `status`: `proposed`, `accepted`, `deprecated`, or `superseded`
- `created`: creation date in `YYYY-MM-DD`
- `context_id`: the owning context's stable identifier, or `system` for cross-context decisions

## Optional sections

Only include these when they add genuine value. Most ADRs won't need them.

- **Supersedes / Superseded by** — useful when decisions are revisited
- **Considered Options** — only when the rejected alternatives are worth remembering
- **Consequences** — only when non-obvious downstream effects need to be called out

## Numbering

Scan the target ADR directory for the highest existing number and increment by one. Use the same number in the filename and `adr_id`.

## When to offer an ADR

All three of these must be true:

1. **Hard to reverse** — the cost of changing your mind later is meaningful
2. **Surprising without context** — a future reader will look at the code and wonder "why on earth did they do it this way?"
3. **The result of a real trade-off** — there were genuine alternatives and you picked one for specific reasons

If a decision is easy to reverse, skip it — you'll just reverse it. If it's not surprising, nobody will wonder why. If there was no real alternative, there's nothing to record beyond "we did the obvious thing."

### What qualifies

- **Architectural shape.** "We're using a monorepo." "The write model is event-sourced, the read model is projected into Postgres."
- **Integration patterns between contexts.** "Ordering and Billing communicate via domain events, not synchronous HTTP."
- **Technology choices that carry lock-in.** Database, message bus, auth provider, deployment target. Not every library — just the ones that would take a quarter to swap out.
- **Boundary and scope decisions.** "Customer data is owned by the Customer context; other contexts reference it by ID only." The explicit no-s are as valuable as the yes-s.
- **Deliberate deviations from the obvious path.** "We're using manual SQL instead of an ORM because X." Anything where a reasonable reader would assume the opposite. These stop the next engineer from "fixing" something that was deliberate.
- **Constraints not visible in the code.** "We can't use AWS because of compliance requirements." "Response times must be under 200ms because of the partner API contract."
- **Rejected alternatives when the rejection is non-obvious.** If you considered GraphQL and picked REST for subtle reasons, record it — otherwise someone will suggest GraphQL again in six months.
