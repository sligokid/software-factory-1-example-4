---
name: "factory-replan"
description: "Revise the remaining phases of a factory feature that needs a replan, then hand it back to the factory."
argument-hint: "Feature id (ENG-123 or the document feature id); defaults to the current branch"
user-invocable: true
disable-model-invocation: true
---

## Input

```text
$ARGUMENTS
```

The input is the feature id. If it is empty, take it from the current `feature/<ID>` branch.

## Steps

1. **Load context.**
   - `git fetch origin && git switch feature/<ID> && git pull --ff-only`.
   - Read `specs/<dir>/source.yaml` to get the source mode. **In document mode never call
     Linear.**
   - Find the PR for `feature/<ID>` with `gh pr view`. Read its review comments, factory
     comments and the reason it was sent to replan. If it still has the `factory:working` label,
     stop: the factory is still running.
   - Linear mode only: fetch the issue and its recent comments with the Linear MCP tools. The
     issue should be in `Needs replan` or `Agent failed`; if it is in `Agent working`, stop.

2. **Summarise the state** for the developer:
   - Phases done (all tasks checked, with their commits) and phases remaining.
   - What went wrong, from the review comments, factory comments and failure logs.

3. **Revise with the developer.** Discuss what should change, then edit only the remaining
   (unchecked) part of `tasks.md`, and `plan.md` or `spec.md` if the approach itself changes.
   Never edit or uncheck completed phases; if completed work must be undone, add a new phase
   that does it. Use `/speckit-plan` or `/speckit-tasks` if a larger rewrite is needed.

4. **Check and hand back.** Run the same checks as `/factory-handoff` steps 2 and 3, for the
   remaining phases only. Then:
   ```bash
   git add specs/<dir>
   git commit -m "<ID>: replan remaining phases"
   git push
   ```
   Update the PR body phase table, reopen the PR as a draft if it was closed, remove any
   `factory:needs-replan` label and add `factory:ready`.
   Linear mode only: move the issue to `Ready for agent` and comment with a short summary of
   what changed.

5. **Report** what changed and that the factory will resume at the first unfinished phase.
