# CLAUDE.md

Operating guide for Claude Code in this repository. The constitution
(`.specify/memory/constitution.md`) overrides this file; read it before any change.

## Product
This is a simple REST API.

## Commands
The product's commands are defined in `.factory/factory.yaml`:
- `commands.setup`: install dependencies.
- `commands.verify`: lint, type check and test. Must pass before every commit.
- `commands.checks`: product-specific rule checks (if defined).

## Developer workflow (interactive)
1. `/factory-start <ISSUE>` or `/factory-start <document>`: start from a Linear issue or from a
   document (no Linear), create `feature/<ID>`, start the spec.
2. `/speckit-clarify`, `/speckit-plan`, optionally `/speckit-checklist`, then `/speckit-tasks`.
3. `/factory-handoff`: analyze, check, push, open the draft PR, hand over to the factory.
4. `/factory-replan <ID>`: revise remaining phases after the factory stops for a replan.

## Rules of thumb
- One phase of `tasks.md` = one commit, within the commit size limit, with its tests.
- Never commit to `main`, never commit secrets, never edit protected paths
  (`.github/`, `.factory/`, `.specify/`, `.claude/`, `.mcp.json`, `CLAUDE.md`).
