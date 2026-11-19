# Registry Pattern

A **registry** is an in-memory index of runtime objects, usually a dictionary keyed by an ID. It answers "what exists right now, and how do I find it?" without scanning the scene.

```csharp
private readonly Dictionary<string, IInteractable> interactables = new();

public void Register(IInteractable item)   => interactables[item.Id.Value] = item;
public void Unregister(IInteractable item) => interactables.Remove(item.Id.Value);
public bool TryGet(InteractableId id, out IInteractable item)
    => interactables.TryGetValue(id.Value, out item);
```

Objects register themselves when enabled and unregister when disabled:

```text
Object.OnEnable()  → registry.Register(this)
Object.OnDisable() → registry.Unregister(this)
```

## Common registries

| Registry | Key → value | Used for |
|---|---|---|
| Player registry | `PlayerId` → player context (avatar, local or remote) | Finding a player's avatar, knowing who is local |
| Interactable registry | `InteractableId` → interactable | The authority resolving a request's target |
| Scene anchor registry | string key → `Transform` | Named positions that data assets can reference |

Scene anchors solve a Unity limitation: a ScriptableObject cannot reference a scene object. A dialogue asset stores the string `"platform"` and resolves it at runtime.

## What a registry is not
- **Not a database.** It lives in memory and disappears when the session or scene ends. Scene objects register again on the next load.
- **Not persistent player data.** Currency, XP, and progress belong in a player data service and a data store. See [[Backend and Persistence]].

## In multiplayer
The authority (host or dedicated server) holds the authoritative registries. Clients may keep local references for presentation, but the authority decides whether an ID is valid.

## Performance
Dictionary lookups are effectively constant time. A few dictionaries with 30 to 40 players and a few hundred objects cost almost nothing. What to avoid instead:
- per-frame `Find*` calls;
- every player checking every object every frame;
- scanning all player data on each interaction.

## Related
- [[Service Locator]]
- [[Authority Model]]
