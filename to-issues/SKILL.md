---
name: to-issues
description: Slice a PRD into independently-grabbable, tracer-bullet vertical-slice markdown files. Each slice cites the modules it touches and the acceptance criteria it satisfies. Use when the user wants to break a PRD into implementation slices.
---

# To Issues

## Your role in the process

This skill is part of a planning-and-implementation process:

- **Plan** — rough description (informal, no skill required)
- **Grill** — stress-test and sharpen
- **PRD** — modules, user stories with ACs, scope boundaries
- **Slice into issues (this skill)** — vertical tracer-bullet decomposition
- **TDD** — implement one slice at a time

Everything outside slicing is out of scope. Do not redesign modules (that was PRD synthesis). Do not write code or tests (that's TDD). Your single job is to take a PRD and produce a buildable, dependency-ordered sequence of vertical slices, each one structurally tied back to the PRD.

## Process

### 1. Gather context

Work from whatever is already in the conversation context. If the user passes a reference to a source document (file path, URL, or identifier) as an argument, fetch it and read its full body.

The source document is expected to be a PRD with at minimum: a Modules section, User Stories with Acceptance Criteria, and an Out of Scope section. Read the PRD's frontmatter to capture its `prd_id` — slices will reference this as `parent`.

If those sections are missing, say so and stop — slicing without ACs forces you to invent the verifiable conditions, which is exactly the synthesis-without-foundation problem to avoid.

### 2. Explore the codebase (optional)

If you have not already explored the codebase, do so to understand the current state. Use the project's domain vocabulary in slice titles and descriptions, and respect any decisions in shared-language docs or ADRs.

### 3. Draft vertical slices

Break the plan into **tracer-bullet** slices. Each slice is a thin vertical slice that cuts end-to-end through ALL integration layers (schema → API → UI → tests), NOT a horizontal slice of one layer.

<vertical-slice-rules>
- Each slice delivers a narrow but COMPLETE path through every relevant layer
- A completed slice is demoable or verifiable on its own
- Prefer many thin slices over few thick ones
- The first slice should be the thinnest plausible end-to-end thing — a "walking skeleton" that proves the modules can talk to each other
- A slice may touch multiple files or components when that keeps responsibilities composed and prevents one monolithic file
</vertical-slice-rules>

**Each slice must explicitly cite:**

- **Modules touched** — naming modules from the PRD's Modules section. If a slice would touch every module, that's a smell: either the slice is too thick, or the modules aren't actually decoupled.
- **Acceptance criteria satisfied** — referencing specific ACs from the PRD's User Stories section, by story number and AC index (e.g. story 2, AC 1).
- **Architecture notes** — any composition boundary, public interface, or split of responsibilities the implementer must preserve. This is especially important for React or UI-heavy slices where orchestration, state, data loading, and presentation can easily collapse into one file.

After drafting, run two coverage checks:

- **AC coverage**: every AC in the PRD should be satisfied by at least one slice. List any ACs not covered.
- **AC uniqueness**: an AC should ideally be owned by one slice. Flag ACs claimed by multiple slices as overlap risks.

**Slice classification:**

Slices may be **HITL** or **AFK**:

- **HITL** (human-in-the-loop) — requires human interaction during implementation: an architectural decision, a design review, a judgement call not pinned down by ACs.
- **AFK** (away-from-keyboard) — can be implemented and merged without human interaction.

Prefer AFK over HITL where possible. A slice that's HITL purely because its ACs are vague is a signal that the PRD needs sharper ACs, not that the slice needs a human — flag those back to the user rather than waving them through.

### 4. Quiz the user

Present the proposed breakdown as a numbered list. For each slice, show:

- **Title** — short descriptive name
- **Type** — HITL / AFK
- **Modules touched** — from PRD Modules section
- **ACs satisfied** — by story number and AC index
- **Blocked by** — which other slices (if any) must complete first
- **Architecture notes** — any composition boundary needed to keep the slice maintainable

Also show:

- **Uncovered ACs** (if any) — ACs from the PRD not claimed by any slice
- **Overlap risks** (if any) — ACs claimed by multiple slices

Ask the user:

- Does the granularity feel right? (too coarse / too fine)
- Are the dependency relationships correct?
- Should any slices be merged or split further?
- Are the correct slices marked HITL vs AFK?
- How should we resolve any uncovered ACs or overlaps?

Iterate until the user approves the breakdown.

### 5. Publish

For each approved slice, publish a new markdown file, unless the project specifies a different convention.

**Default convention:** write each slice to `docs/issues/<slice_id>.md` where `slice_id` is `<NNN>-<short-kebab-slug>` — `NNN` is a zero-padded sequence number (find the highest existing number across `docs/issues/` and increment, or start at `001`). If `docs/issues/` doesn't exist, create it. If the repo already has a different convention for tracking work (e.g. an issue tracker, a `tickets/` folder), use that instead.

**Override:** if the repo's shared-language docs, an AGENTS.md, a CLAUDE.md, or similar configuration specifies a different publishing target, follow that.

If unsure which convention applies, ask the user once.

Publish slices in dependency order (blockers first) so you can reference real `slice_id`s in the `blocked_by` field.

Do NOT modify the parent PRD.

## Slice template

<slice-template>

```yaml
---
type: slice
slice_id: <NNN-kebab-slug matching the filename, without .md>
title: <human-readable title>
status: ready
slice_type: AFK
parent: <prd_id of the parent PRD>
modules_touched:
  - <ModuleName>
acceptance_criteria:
  - story: <N>
    ac: <M>
  - story: <N>
    ac: <M>
blocked_by: []
created: <YYYY-MM-DD>
---
```

# <Human-readable title>

## Parent

Reference to the parent PRD: `<prd_id>` (`docs/prds/<prd_id>.md`).

## What to build

A concise description of this vertical slice. Describe the end-to-end behaviour, not layer-by-layer implementation.

Avoid specific file paths or code snippets — they go stale fast. **Exception:** if a prototype produced a snippet that encodes a decision more precisely than prose can (state machine, reducer, schema, type shape), inline it here and note briefly that it came from a prototype. Trim to the decision-rich parts.

## Modules touched

- **`<ModuleName>`** — what this slice does to it (extend interface, add new method, etc.)
- **`<ModuleName>`** — what this slice does to it

## Architecture notes

Describe any composition boundaries the implementer should preserve: public interfaces, ownership of state or orchestration, reusable components/hooks, adapters around external systems, or reasons this slice should be split across files instead of concentrated in one large file. Write "None beyond existing patterns" when no special composition pressure exists.

## Acceptance criteria

Drawn directly from the parent PRD. Reference by story number and AC index, and restate inline for the implementer:

- [ ] (Story 2, AC 1) Restated condition
- [ ] (Story 2, AC 3) Restated condition
- [ ] (Story 4, AC 2) Restated condition

## Blocked by

- `<slice_id>` — short reason for the dependency

Or "None — can start immediately" if no blockers.

</slice-template>
