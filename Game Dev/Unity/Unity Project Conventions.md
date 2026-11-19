# Unity Project Conventions

## Folders
One folder per **system** at the root of `Assets/`, each with the same inner layout:

```text
Assets/
  Core/          Scripts/ (Contracts, Runtime, Editor)
  Interactions/  Scripts/ Prefabs/ Data/
  UI/            Scripts/ Prefabs/ Data/
  PlayerData/    ...
  Dialogue/      ...
  Quests/        ...
  Directors/     ...
  Player/        ...
  ThirdParty Assets/   (never modified; wrap or extend instead)
```

`Contracts/` holds the interfaces and data types that other systems depend on. `Runtime/` holds the implementations.

## Code style
- One namespace per system (`GameName.Interactions`, `GameName.UI`, …).
- Public properties are PascalCase with `{ get; private set; }`.
- Serialized fields are private with `[SerializeField]`, plus `[Header]` and `[Tooltip]`.
- Interfaces for capabilities, abstract base classes for shared behaviour, concrete classes for content.
- Comments only for constraints the code can't express.

## Scene hierarchy
Root divider objects group content: `--MANAGERS--`, `--CAMERAS--`, `--LIGHTS--`, `--UI--`, `--ENVIRONMENT--`, `--PLAYER--`. An editor tool can generate them.

## Execution order
- Service hosts use `[DefaultExecutionOrder(-100)]` and register in `Awake`.
- Consumers resolve services in `Start`.

## Assets and `.meta` files
- Create, move, and rename assets through Unity (`AssetDatabase`) so GUIDs and references survive.
- If a file is moved on disk, move its `.meta` file with it. Never delete or regenerate a `.meta` file on its own.
- Don't hand-edit `.unity` or `.prefab` YAML.

## Performance at 30+ players
- No `Find*` calls per frame.
- Prefer events over per-player `Update` polling.
- Avoid O(n²) loops over players. Use non-allocating physics queries (`OverlapSphereNonAlloc`).

## Related
- [[Assembly Definitions]]
- [[Service Locator]]
