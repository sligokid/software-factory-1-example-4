<!--
SYNC IMPACT REPORT (remove before committing)
=============================================
Version change: 0.1.0 → 0.2.0
Bump rationale: MINOR — Product Principles section populated with five concrete principles
                (was an empty stub); no factory sections altered.

Modified sections:
  - Product Principles: stub → five named principles (I–V)

Added sections: none
Removed sections: none

Deferred TODOs: none — all placeholders resolved
-->

# software-factory-1-example-4 Constitution

This constitution governs every spec, plan, task and line of code in this repository, whether
written by a person or by an agent. Spec Kit commands, the factory and the reviewer agent all
check work against it.

The **Product Principles** section belongs to this product. The **Factory** sections below are
shared by every product built with the factory; change them only by upgrading the factory kit.

## Product Principles

### I. Technology Stack

The runtime is **Python 3.12**, the web framework is **FastAPI**, and the database is
**PostgreSQL**. No alternative HTTP frameworks or relational databases may be introduced.
All new code MUST target Python 3.12+ and rely only on dependencies declared in
`requirements.txt`.

### II. Type Safety

All Python code MUST carry complete type hints. Request and response schemas MUST be defined as
**Pydantic v2** models — not plain dicts or dataclasses. The type `Any` is permitted only at
explicit external boundaries (e.g. raw JSON ingestion) and MUST be annotated with a comment
explaining why a narrower type is not feasible.

### III. RESTful API Design

Endpoints MUST follow RESTful conventions:

- Resource paths use plural nouns (e.g. `/v1/users`, not `/v1/user`).
- Standard HTTP verbs apply: GET (read), POST (create), PUT/PATCH (update), DELETE (remove).
- Responses MUST use correct HTTP status codes (201 for creation, 404 for not found, etc.).
- The API version MUST appear in the URL path prefix (e.g. `/v1/`).

### IV. Test-First Development

Every endpoint and every service-layer function MUST have a corresponding `pytest` test.
Tests MUST run against a real PostgreSQL instance — mocking the database is not permitted.
`pytest` MUST pass with zero failures before any commit is made.

### V. Simplicity (YAGNI)

Features are built strictly to spec. No speculative abstractions, no helper functions
introduced before they are needed in at least two call sites, and no dependency added
"for future use." Three similar lines are preferable to a premature abstraction.

## Factory: Spec and Task Rules

- A spec handed to the factory has no `[NEEDS CLARIFICATION]` markers left.
- `tasks.md` is organised into phases (`## Phase N: ...`). **The factory implements one phase
  per run, as one commit**, so every phase MUST be independently testable and fit the commit
  size limit in `.factory/factory.yaml` (default 300 changed lines, excluding `specs/` and the
  configured exclusions). Split any phase that would not.
- Task descriptions include exact file paths.
- Every behaviour change ships with its tests in the same commit.

## Factory: Agent Boundaries

Agents (the factory's implement, fix, rework and converge runs) MUST:
- Change only what the current phase requires, plus the feature's own `specs/` folder.
- Never change `.github/`, `.factory/`, `.specify/`, `.claude/`, `.mcp.json`, `CLAUDE.md`,
  this constitution, or any path listed under `protected` in `.factory/factory.yaml`.
- Never push to `main`. Commit only to the feature branch they were given.
- Run only git, Spec Kit scripts and the commands in `.factory/factory.yaml`.
- Never read, print or commit secrets.

## Factory: Workflow and Quality Gates

1. A developer runs `/factory-start <ISSUE>`, the Spec Kit commands, then `/factory-handoff`,
   which opens one draft PR `feature/<ISSUE> -> main`.
2. The factory implements each phase as one commit. `commands.verify` and `commands.checks`
   MUST pass before every commit; CI and the reviewer agent must pass before the next phase.
3. `speckit-converge` must report converged before the PR is marked ready.
4. A human reviews and merges the PR. Nothing reaches `main` any other way.

## Governance

- This constitution overrides any other guidance in the repository, including `CLAUDE.md`.
- Only humans amend it, through a normal PR that updates the version and dates below.
  Versioning: MAJOR for removed or redefined principles, MINOR for new principles or sections,
  PATCH for wording.
- The reviewer agent cites the section for every violation it reports.

**Version**: 0.2.0 | **Ratified**: 2026-11-17 | **Last Amended**: 2026-11-17
