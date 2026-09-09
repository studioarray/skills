# Studio Array skills

Reusable agent skills for implementing GitHub issues, reviewing pull requests, and working in TypeScript.

| Skill | Purpose |
| --- | --- |
| [TypeScript Craft](typescript-craft/SKILL.md) | Guide backend and React TypeScript implementation, module structure, types, and tests. |
| [Issue to PR](issue-to-pr/SKILL.md) | Coordinate implementation of a GitHub issue and its sub-issues, then review the resulting PR. |
| [PR review orchestration](pr-review-orchestration/SKILL.md) | Coordinate QA and fixes for an existing PR until merge readiness. |

## Install in Codex

Ask Codex to install the skills on each computer:

```text
Use $skill-installer to install typescript-craft, issue-to-pr, and
pr-review-orchestration from https://github.com/studioarray/skills.
```

The installer creates personal skill copies. To update an existing installation, ask Codex to update it from this repository.

## Use

```text
$issue-to-pr https://github.com/owner/repo/issues/123
```

```text
$pr-review-orchestration https://github.com/owner/repo/pull/456
```

```text
$typescript-craft Implement this TypeScript feature.
```

The skills can also be selected automatically when a request matches their purpose. `issue-to-pr` uses `pr-review-orchestration` for its review stage.

The orchestration skills use GPT-6 Astra medium subagents for implementation and QA. They create or update PRs, commit and push changes, and post review and fix comments. They stop at merge readiness. Testing follows each repository's guidance and CI workflows.

## Dependencies

The orchestration skills expect GitHub access, a local repository checkout, and Codex subagent tools with the requested model available. They also use these separately installed skills:

- [Code Review](https://github.com/mattpocock/skills/blob/main/skills/engineering/code-review/SKILL.md) from [mattpocock/skills](https://github.com/mattpocock/skills).
- [Codebase Design](https://github.com/mattpocock/skills/blob/main/skills/engineering/codebase-design/SKILL.md) from [mattpocock/skills](https://github.com/mattpocock/skills).
- [Ponytail](https://github.com/DietrichGebert/ponytail), which provides `ponytail:ponytail` and `ponytail:ponytail-review`.

These dependencies are not bundled here. Install them on each computer alongside this collection. TypeScript Craft includes its supporting references in this repository.
