# Skills

This repository contains a small chain of agent skills for moving from a rough idea to tested implementation without relying on session memory or a specific issue tracker.

## Process

These skills are meant to work as a planning-and-implementation process:

- **Plan** - informal chat or notes that sketch the idea. No skill required.
- **Grill** - stress-test the plan, sharpen terminology, and capture durable shared-language docs and ADRs.
- **PRD** - synthesize modules, user stories, acceptance criteria, and scope boundaries into a markdown PRD.
- **Slice into issues** - turn the PRD into vertical tracer-bullet slices with structural links back to modules and acceptance criteria.
- **TDD** - implement one approved slice at a time with a red-green-refactor loop.
- **TypeScript craft** - companion guidance for backend TypeScript and React TypeScript implementation so first-pass code stays maintainable, composed, and well-placed.
- **Orchestrate issues** - run ready issues sequentially with implementation/checker agents, one clean commit per issue.

## Skills

- `grill-me` - plain grilling session for an existing rough plan or design.
- `grill-with-docs` - grilling session that updates `CONTEXT.md`, `CONTEXT-MAP.md`, and ADRs as decisions crystallize.
- `to-prd` - writes a durable, sliceable PRD from upstream planning and grilling artefacts.
- `to-issues` - slices a PRD into dependency-ordered markdown implementation slices.
- `tdd` - implements one slice or bugfix at a time using behavior-first TDD.
- `typescript-craft` - default companion for backend TypeScript and React TypeScript work, emphasizing maintainable code shape, DDD/hexagonal placement, React composition, TypeScript hygiene, and focused tests.
- `orchestrate-issues` - orchestrates multiple ready issue files through implementation, independent checking, per-issue commits, and agent cleanup.

## Default Artefacts

- PRDs: `docs/prds/<prd_id>.md`
- Slices: `docs/issues/<slice_id>.md`
- Single-context shared language: `CONTEXT.md`
- Multi-context shared language: `CONTEXT-MAP.md` plus per-context `CONTEXT.md`
- ADRs: `docs/adr/<NNNN-slug>.md`

These defaults are intentionally markdown-first and can be overridden by project convention.

## Credits

These skills are adapted from ideas and original skill drafts by Matt Pocock in [`mattpocock/skills`](https://github.com/mattpocock/skills), especially the engineering skills around grilling, PRDs, issue slicing, and TDD.
