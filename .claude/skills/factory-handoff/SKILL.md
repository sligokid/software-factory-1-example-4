---
name: "factory-handoff"
description: "Hand a finished spec and task list to the factory: analyze, run handoff checks, commit, push, open the draft PR and signal the factory (Linear state or PR label)."
user-invocable: true
disable-model-invocation: true
---

## Context

Run this on a `feature/<ID>` branch after `/speckit-tasks`. After handoff the factory implements
every phase of `tasks.md` on this branch, one commit per phase, and the developer's next step is
reviewing the single PR.

## Steps

1. **Locate the feature.**
   - Current branch must match `feature/<ID>`; otherwise stop.
   - The spec directory is the one `specs/*/` folder added on this branch
     (`git diff --name-only origin/main...HEAD -- specs/` plus untracked files under `specs/`).
     Exactly one is allowed; otherwise stop and explain.
   - Read `specs/<dir>/source.yaml`. `type` is `linear` or `document`. If the file is missing,
     stop and suggest re-running `/factory-start`. **In document mode never call Linear.**

2. **Analyze.** Invoke the `speckit-analyze` skill. If it reports any CRITICAL or HIGH issue,
   list them, stop, and suggest fixing them with the relevant Spec Kit command.

3. **Handoff checks.** Check every item and report all failures together:
   - `spec.md`, `plan.md` and `tasks.md` exist in the spec directory.
   - No `[NEEDS CLARIFICATION` text remains in any file in the spec directory.
   - `tasks.md` is organised as `## Phase N: ...` sections with `- [ ] T### ...` tasks, and no
     task is already checked.
   - Each phase is independently testable and, by your estimate, fits in one commit within the
     commit size limit (`limits.commit_lines` in `.factory/factory.yaml`, default 300). For each
     phase print: phase name, task count, estimated changed lines. Any phase over the limit is a
     failure: suggest a split.
   - No files outside `specs/` are changed on this branch.
   - `factory validate` passes, and the product's `commands.verify` from
     `.factory/factory.yaml` passes.
   If anything fails, stop without committing.

4. **Commit and push.**
   ```bash
   git add specs/<dir>
   git commit -m "<ID>: spec, plan and tasks"
   git push -u origin feature/<ID>
   ```

5. **Open the draft PR** with `gh pr create --draft --base main --head feature/<ID>`:
   - Title: `<ID>: <title>`
   - Body:
     ```
     Source: <Linear URL, or "document: specs/<dir>/<document file>">
     Spec: specs/<dir>

     ## Phases
     | Phase | Tasks | Est. lines | Commit | CI |
     |-------|-------|------------|--------|----|
     | Phase 1: ... | T001-T004 | ~120 | pending | pending |

     ## Status
     Handed to the factory. This PR will be marked ready for review when every phase is
     implemented and speckit-converge reports converged.

     <!-- factory:spec=specs/<dir> id=<ID> source=<linear|document> -->
     ```
   - If a PR for this branch already exists, update its body instead.

6. **Signal the factory.**
   - Always add the label `factory:ready` to the PR (`gh pr edit --add-label factory:ready`;
     create the label with `gh label create factory:ready` if it does not exist).
   - Linear mode only: move the issue to `Ready for agent` and comment
     `Handed to the factory: <N> phases. PR: <pr url>`.

7. **Report** the PR link and the phase table. Tell the developer the factory will post progress
   on the PR (and on the Linear issue in Linear mode), and that their next step is reviewing the
   PR when it is marked ready.
