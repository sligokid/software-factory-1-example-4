---
name: "factory-start"
description: "Start a factory feature from a Linear issue or from a document: create the feature branch, record the source and draft the spec with speckit-specify."
argument-hint: "Linear issue key (ENG-123) or a document path (docs/feature.md [--id short-name])"
user-invocable: true
disable-model-invocation: true
---

## Input

```text
$ARGUMENTS
```

## Choose the source

- **Linear mode:** the input is a Linear issue key (pattern `^[A-Z][A-Z0-9]*-[0-9]+$`, for
  example `ENG-123`). The feature id is the issue key.
- **Document mode:** the input is a path to a document the developer provides (Markdown, text,
  PDF or any file you can read), optionally followed by `--id <short-name>`. The feature id is
  `--id` if given, otherwise a 2-4 word kebab-case slug of the document's title. **Document mode
  never uses Linear**: do not call any Linear tool, do not ask about Linear.
- Anything else (empty input, a missing file): ask the developer for an issue key or a document
  path and stop.

The feature id must match `^[A-Za-z0-9][A-Za-z0-9._-]{1,40}$`.

## Steps

1. **Check the repository.** `.factory/factory.yaml` must exist (run `factory validate`); if not,
   tell the developer to run `factory init` first and stop. Run `git status --porcelain`; if
   there are uncommitted changes, stop and ask the developer to commit or stash them.

2. **Read the source.**
   - Linear mode: fetch the issue with the Linear MCP tools: title, description, state, URL.
     If the Linear tools are unavailable, tell the developer to connect the Linear MCP server
     from `.mcp.json`, or to start from a document instead, and stop. If the issue is already in
     `Ready for agent`, `Agent working` or `Awaiting approval`, stop: the factory owns it.
   - Document mode: read the whole document. Its title is the first heading, or the file name.

3. **Create the feature branch** from the latest `main`:
   ```bash
   git fetch origin main
   git switch -c feature/<ID> origin/main
   ```
   If `feature/<ID>` already exists locally or on origin, switch to it and say you are resuming.
   If there is no `origin` remote yet, branch from the local `main` and say so.

4. **Draft the spec.** Invoke the `speckit-specify` skill with this description and a short
   name of the form `<id-lowercase>-<2-3 words>` (or just `<id>` in document mode):
   ```
   <ID>: <title>
   Source: <Linear URL, or the document path>

   <full issue description, or the full document text>
   ```

5. **Record the source** in the new spec directory as `specs/<dir>/source.yaml`:
   ```yaml
   type: linear          # or: document
   id: <ID>
   title: <title>
   url: <Linear URL>     # linear only
   path: <document path> # document only
   ```
   In document mode also copy the document into `specs/<dir>/` (keep its file name) so the spec
   stays traceable even if the original moves. `/factory-handoff`, `/factory-replan` and the
   factory read `source.yaml` to decide whether to talk to Linear.

6. **Linear mode only:** move the issue to `In spec`, assign it to the developer if unassigned,
   and comment `Spec started on branch feature/<ID>.`

7. **Report** the source mode, the branch, the spec directory and any `[NEEDS CLARIFICATION]`
   markers, then suggest the next steps:
   `/speckit-clarify` -> `/speckit-plan` -> `/speckit-checklist` (optional) -> `/speckit-tasks`
   -> `/factory-handoff`.

Do not commit or push in this command.
