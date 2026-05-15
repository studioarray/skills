---
name: to-prd
description: Synthesise a PRD from upstream context (shared-language docs, ADRs, conversation, plans) and publish it as a markdown file. Use when the user wants to turn a grilled-out plan into a durable, sliceable spec.
---

# To PRD

## Your role in the chain

This skill is one step in a five-step planning-and-implementation chain:

1. **Plan** — rough feature description (informal, no skill required)
2. **Grill** — stress-test the plan, sharpen terminology, capture decisions
3. **PRD (this skill)** — synthesise modules, user stories, acceptance criteria, scope boundaries
4. **Slice into issues** — cut the PRD into vertical tracer-bullet slices
5. **TDD** — implement one slice at a time

Everything outside step 3 is out of scope for this skill. Do not invent the initial plan (that's informal step 1). Do not re-discover or stress-test the problem (that's grilling). Do not decide build order (that's slicing). Do not write code (that's TDD). Your single job is to turn upstream artefacts into a durable, sliceable specification.

## Synthesis-first posture

Do NOT interview the user about the problem space. Work from what's already available:

- The current conversation context
- Any shared-language documentation in the repo (domain glossary, CONTEXT.md, or equivalent)
- Architectural decision records (ADRs) or equivalent decision logs
- Any rough plan, brief, or grilled-out conversation history

If planning and grilling haven't happened yet, the right response is to say so and stop — not to backfill them by interviewing. A weak PRD on top of a missing foundation is worse than no PRD.

You MAY ask the user up to **three** targeted questions, but only of the kinds named below in the process. Anything else is out of scope.

## Process

### 1. Read upstream artefacts

Explore the repo. Locate and read any shared-language documentation, ADRs, or equivalent. Read the conversation context. Use the project's vocabulary throughout the PRD — do not redefine terms that already exist in the glossary, and do not re-litigate decisions that already exist as ADRs. Reference them by identifier.

### 2. Sketch the modules

Sketch the major modules you will need to build or modify. Actively look for opportunities to extract deep modules that can be tested in isolation.

A deep module is one which encapsulates a lot of functionality behind a simple, testable interface which rarely changes. (As opposed to a shallow module, where the interface is nearly as complex as the implementation.)

**Checkpoint 1 (counts toward question budget):** Present the module sketch to the user. Ask whether the shape matches their expectations, and which modules they want tested. One question, not a conversation.

### 3. Generate user stories with acceptance criteria

Write a comprehensive numbered list of user stories. Each story should be in the format:

> As an `<actor>`, I want `<feature>`, so that `<benefit>`

For each story, write a short list of acceptance criteria — testable conditions that must be true for the story to count as delivered.

**Grouping rule:** ACs attach per story by default. Group multiple stories under a shared AC list only when the stories are tightly coupled and would be implemented together (e.g. "view list", "sort list", "filter list" might share ACs about list rendering). Never collapse the whole PRD into one AC list.

Cover routine flows that grilling rarely surfaces: empty states, error states, loading states, permission/auth edges, accessibility basics, and any obvious adjacent paths (cancel, undo, retry).

**Checkpoint 2 (counts toward question budget):** Surface the story list to the user with one targeted question — "Have I missed any flows? Particularly empty/error states or edges around [the 1–3 stories most likely to have hidden edges]." This is a coverage check, not an interview.

**Checkpoint 3 (counts toward question budget, use only if needed):** For any story where the ACs are genuinely ambiguous and you're guessing, flag those stories specifically and ask the user to confirm or refine. Skip this checkpoint entirely for mechanical stories where the ACs are obvious.

### 4. Write the PRD

Use the template below. The PRD should read as a definitive statement of what's being built, not a record of a conversation.

### 5. Publish

Publish the PRD as a markdown file in the repo, unless the project specifies a different convention (e.g. an issue tracker, a tickets folder with its own format, or a wiki).

**Default convention:** write to `docs/prds/<prd_id>.md` where `prd_id` is a short kebab-slug derived from the PRD's solution in a few words. If `docs/prds/` doesn't exist, create it. If the repo already has a different markdown convention for specs (e.g. `specs/`, `rfcs/`, `planning/`), use that instead.

**Override:** if the repo's shared-language docs, an AGENTS.md, a CLAUDE.md, or similar configuration specifies a different publishing target, follow that.

If unsure which convention applies, ask the user once (this does NOT count toward the question budget — it's plumbing, not synthesis).

## PRD template

<prd-template>

```yaml
---
type: prd
prd_id: <kebab-slug matching the filename, without .md>
title: <human-readable title>
status: draft
created: <YYYY-MM-DD>
modules:
  - <ModuleName>
  - <ModuleName>
related_adrs:
  - <adr identifier or path>
---
```

# <Human-readable title>

## Problem Statement

The problem the user is facing, from the user's perspective. One or two paragraphs.

## Solution

The solution to the problem, from the user's perspective. One or two paragraphs.

## Modules

The major modules that will be built or modified, named in the project's vocabulary. For each module, a one-line statement of responsibility. No file paths, no code, no interface signatures — just the conceptual shape.

If any module is intended to be a deep module, note that briefly.

- **`<ModuleName>`** — responsibility in one line. _(deep module)_
- **`<ModuleName>`** — responsibility in one line.

## User Stories & Acceptance Criteria

A LONG, numbered list of user stories. Each story in the format:

1. As an `<actor>`, I want `<feature>`, so that `<benefit>`.
   - [ ] AC: testable condition
   - [ ] AC: testable condition
   - [ ] AC: testable condition

<example>
1. As a mobile bank customer, I want to see the balance on my accounts, so that I can make better informed decisions about my spending.
   - [ ] AC: All accounts the customer owns are listed, in the order they appear in the account-management screen
   - [ ] AC: Each account shows its current balance in the account's native currency
   - [ ] AC: A loading state is shown while balances are being fetched
   - [ ] AC: If a balance fails to load, that account shows an error state with a retry affordance; other accounts are unaffected
   - [ ] AC: If the customer has no accounts, an empty state is shown with a CTA to open one
</example>

Where stories are tightly coupled and share criteria, group them under a single AC list and note the grouping explicitly.

## Implementation Decisions

A list of implementation decisions that fall _outside_ what's already captured in ADRs. This can include:

- Module interfaces (described in prose, not code)
- Technical clarifications from the developer
- Schema changes
- API contracts
- Specific interactions

Reference relevant ADRs by identifier rather than restating them.

Do NOT include specific file paths or code snippets — they go stale fast.

**Exception:** if a prototype produced a snippet that encodes a decision more precisely than prose can (state machine, reducer, schema, type shape), inline it within the relevant decision and note briefly that it came from a prototype. Trim to the decision-rich parts — not a working demo, just the load-bearing bits.

## Testing Decisions

- What makes a good test here (default: external behaviour only, not implementation details — adjust if the project's conventions differ)
- Which modules will be tested
- Prior art for the tests (similar test types elsewhere in the codebase)

## Out of Scope

An explicit list of things that are NOT in this PRD. This section is load-bearing — it stops downstream slicing from drifting into adjacent features. Be specific: name the things someone might reasonably assume are included.

## Further Notes

Any further notes about the feature. Optional.

</prd-template>
