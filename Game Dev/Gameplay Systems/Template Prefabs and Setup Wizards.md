# Template Prefabs and Setup Wizards

A pattern for making content creation cheap: build one **template prefab** with all the components wired, plus an **editor wizard** that creates a configured variant in a few clicks.

## How it works
1. `Template X.prefab` contains the full component setup with placeholder visuals.
2. An editor window (`Tools > Create X`) asks for a name, a model, and data assets (for example a dialogue for an NPC, or a type ID for a collectible).
3. The wizard creates a **prefab variant** of the template through `PrefabUtility`, assigns the fields, swaps the model, and saves it in the right folder.
4. Fixes made to the template propagate to every variant.

## Examples
- Character template plus a player setup wizard.
- NPC template plus an NPC setup wizard (model, dialogue, NPC ID).
- Collectible template plus a collectible setup wizard (type ID, visuals, optional linked quest).

## Tips
- Put the shared logic in a generic `TemplateInstanceWizard` base, and keep each specific wizard thin.
- Create assets only through `AssetDatabase`, so GUIDs and `.meta` files stay correct.
- Generate unique IDs for scene instances, never for the prefab asset itself.

## Related
- [[Interactables]]
- [[Collectibles]]
- [[Unity Project Conventions]]
