---
name: grill-with-docs
description: Grilling session that challenges a plan against the existing domain model, sharpens terminology, and updates durable shared-language artefacts (CONTEXT.md, CONTEXT-MAP.md, ADRs). Use when the user wants to stress-test a plan before PRD-writing, capture domain language and decisions, or prepare upstream context for to-prd without drifting into PRD, slicing, or implementation work.
---

# Grill With Docs

## Your role in the process

This skill is part of a planning-and-implementation process:

- **Plan** — rough feature description (informal, no skill required)
- **Grill (this skill)** — stress-test the plan, sharpen terminology, capture decisions
- **PRD** — modules, user stories, acceptance criteria, scope boundaries
- **Slice into issues** — vertical tracer-bullet decomposition
- **TDD** — implement one slice at a time

Everything outside grilling is out of scope. Do not create the initial plan from nothing; grilling needs at least a rough idea, proposal, or design to challenge. Do not write a PRD. Do not define implementation modules, user stories, acceptance criteria, build order, issue slices, tests, or code. Your job is to create the shared language and decision record that downstream skills can consume even if conversation context is lost.

## Operating posture

Interview the user relentlessly about the plan until there is a shared understanding. Walk down each branch of the design tree, resolving dependencies between decisions one by one. For each question, provide your recommended answer.

If there is no rough plan or design in the conversation, ask the user for one and stop. Do not brainstorm the plan on their behalf inside this skill.

Ask questions one at a time, waiting for feedback before continuing. If a question can be answered by exploring the codebase, explore the codebase instead.

Keep the intensity. Challenge vague language, hidden assumptions, and overloaded terms directly, but make the output durable and markdown-first.

## Artefacts

Create and update artefacts lazily. Do not create files just to satisfy a template.

- **`CONTEXT.md`** — the shared-language document for a single context. Use the format in [CONTEXT-FORMAT.md](./CONTEXT-FORMAT.md).
- **`CONTEXT-MAP.md`** — the root-level map for a repo with multiple contexts. Use the context-map format in [CONTEXT-FORMAT.md](./CONTEXT-FORMAT.md).
- **ADRs** — decision records for hard-to-reverse, surprising, trade-off-driven decisions. Use the format in [ADR-FORMAT.md](./ADR-FORMAT.md).

Prefer the repo's existing conventions. If the user names a different docs location, use it. Otherwise keep the defaults simple:

- Single-context repos: root `CONTEXT.md`
- Multi-context repos: root `CONTEXT-MAP.md`, with each context owning its own `CONTEXT.md`
- ADRs: `docs/adr/` near the relevant context, or root `docs/adr/` for system-wide decisions

Do not assume GitHub, Linear, Claude-specific bootstrapping, or any other toolchain.

## Domain awareness

During codebase exploration, also look for existing documentation:

### File structure

Most repos have a single context:

```
/
├── CONTEXT.md
├── docs/
│   └── adr/
│       ├── 0001-event-sourced-orders.md
│       └── 0002-postgres-for-write-model.md
└── src/
```

If a `CONTEXT-MAP.md` exists at the root, the repo has multiple contexts. The map points to where each one lives:

```
/
├── CONTEXT-MAP.md
├── docs/
│   └── adr/                          ← system-wide decisions
├── src/
│   ├── ordering/
│   │   ├── CONTEXT.md
│   │   └── docs/adr/                 ← context-specific decisions
│   └── billing/
│       ├── CONTEXT.md
│       └── docs/adr/
```

Create files lazily — only when you have something to write. If no `CONTEXT.md` exists, create one when the first term is resolved. If no `docs/adr/` exists, create it when the first ADR is needed.

## During the session

### Challenge against the glossary

When the user uses a term that conflicts with the existing language in `CONTEXT.md`, call it out immediately. "Your glossary defines 'cancellation' as X, but you seem to mean Y. Which is it?"

### Sharpen fuzzy language

When the user uses vague or overloaded terms, propose a precise canonical term. "You're saying 'account' — do you mean the Customer or the User? Those are different things."

### Probe beyond bounded contexts when needed

Bounded contexts matter, but they are not the only useful output of grilling. Capture any domain areas, conceptual boundaries, invariants, constraints, or unresolved ambiguities that downstream PRD-writing will need. Do not call these implementation modules; modules are defined later by the PRD skill.

### Discuss concrete scenarios

When domain relationships are being discussed, stress-test them with specific scenarios. Invent scenarios that probe edge cases and force the user to be precise about the boundaries between concepts.

### Cross-reference with code

When the user states how something works, check whether the code agrees. If you find a contradiction, surface it: "Your code cancels entire Orders, but you just said partial cancellation is possible — which is right?"

### Update CONTEXT.md inline

When a term, relationship, boundary, constraint, or ambiguity is resolved, update the relevant `CONTEXT.md` right there. Do not batch these up — capture them as they happen. Use the format in [CONTEXT-FORMAT.md](./CONTEXT-FORMAT.md).

Don't couple `CONTEXT.md` to implementation details. Only include terms that are meaningful to domain experts.

### Offer ADRs sparingly

Only offer to create an ADR when all three are true:

1. **Hard to reverse** — the cost of changing your mind later is meaningful
2. **Surprising without context** — a future reader will wonder "why did they do it this way?"
3. **The result of a real trade-off** — there were genuine alternatives and you picked one for specific reasons

If any of the three is missing, skip the ADR. Use the format in [ADR-FORMAT.md](./ADR-FORMAT.md).

### Track unresolved ambiguity explicitly

If an ambiguity is discovered but not resolved, record it as open in `CONTEXT.md`. Do not hide it in prose. Downstream PRD-writing should be able to tell whether it can proceed, ask a bounded verification question, or stop.

### Stop before PRD work

If the user starts asking for stories, acceptance criteria, issue slices, or implementation tasks, name the boundary and suggest moving to the relevant next step. You may summarize the grilled-out plan in plain language, but do not publish a PRD-shaped artefact from this skill.

## Handoff checklist

Before ending the session, make sure the durable handoff is complete:

- Relevant `CONTEXT.md` files have frontmatter and stable `context_id`s.
- `CONTEXT-MAP.md` exists if multiple contexts are involved.
- Resolved terms, relationships, conceptual boundaries, constraints, and ambiguities are captured.
- Open ambiguities are explicitly marked as open.
- ADR-worthy decisions have ADRs with stable `adr_id`s; non-ADR decisions are not forced into ADRs.
- No PRD modules, user stories, acceptance criteria, issue slices, tests, or code have been produced by this skill.

End with a concise handoff note listing the artefacts changed and any open questions that must be resolved before `to-prd`.
