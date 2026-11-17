# /factory-setup

Fill in the placeholders that `factory validate` reports after `factory init`.

## Steps

1. Run `factory validate` and collect the warnings.
2. If there are no warnings, report that the product is already set up and stop.
3. For each warning present, ask the developer **one question at a time**:

   | Warning | Question to ask |
   |---------|-----------------|
   | `factory.yaml: placeholder still set` | What runner (`python` / `node` / `base`), optional setup command, and verify command? |
   | `factory.yml: set the factory repository` | GitHub `owner/repo` of the factory (e.g. `sligokid/software-factory`) |
   | `constitution.md: product section not filled in yet` | What technology stack and key conventions should go in the constitution? |
   | `CLAUDE.md: product section not filled in yet` | One or two sentence description of this product |

4. After collecting all answers, apply them:

   **`.factory/factory.yaml`**
   - Set `runner:` to the chosen runner
   - If a setup command was given, uncomment/add `setup:` under `commands`
   - Replace the `verify:` placeholder line with the real command

   **`.github/workflows/factory.yml`**
   - Replace `OWNER/software-factory` (and strip the trailing `# FACTORY:` comment) with
     `{factory_repo}/.github/workflows/product.yml@v1`

   **`.specify/memory/constitution.md`**
   - Replace the `<!-- FACTORY: ... -->` comment block with a `### Technology Stack` section
     containing the developer's answer, followed by:
     `Run /speckit-constitution to expand these principles.`

   **`CLAUDE.md`**
   - Replace the `<!-- FACTORY: ... -->` comment with the product description

5. Run `factory validate` again to confirm zero warnings.
6. Tell the developer the next step: run `/speckit-constitution` to fill in the full product principles, then start a feature with `/factory-start`.
