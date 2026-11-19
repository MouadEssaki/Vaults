# Planning With AI Agents

How I break a feature set into plans that several AI agents can implement in parallel without breaking each other.

## Structure
- **Plan:** one system (for example "interactions" or "UI shell"), with an overview and work packages.
- **Work package (WP):** a unit one agent can implement alone, with clear file ownership.
- **Wave:** a group of WPs that can run at the same time. Wave N starts only when the WPs it depends on are finished.

```text
Wave 1: shared contracts (interfaces, data types)
Wave 2: system runtimes in parallel (code only)
Wave 3: prefabs and UI assets
Wave 4: sample content, templates, wizards
Wave 5: scene integration and end-to-end validation
```

## Rules
- **Contracts first.** All interfaces are written in one wave, so parallel agents code against a fixed API.
- **Single owner per file per wave**, especially scenes, prefabs, input action assets, project settings, and the package manifest.
- **One compile error blocks everyone** when everything is in one assembly. Each agent leaves the project compiling with a clean console.
- **Only one agent runs mutating editor commands at a time.**
- Without git worktrees, file ownership is the only isolation.
- WPs that create MonoBehaviours write a **prefab wiring spec** (fields, child hierarchy, required components), and later asset-building waves build only from it.
- Plan status lifecycle: `Draft → Approved → In progress → Implemented → Validated`. Only I approve and validate.

## Shared brief
One document with all decisions (numbered), the contract sheet, the WP table, and the dependency matrix. Every agent reads it first.

## Cost control
- Use one agent per plan, not nested sub-agents.
- Use a cheaper or faster model for reconciliation and mechanical edits.

## Related
- [[Development Before Netcode]]
- [[Unity Project Conventions]]
