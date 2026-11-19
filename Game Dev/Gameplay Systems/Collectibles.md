# Collectibles

## Three kinds

| Kind | Visible when | Stored in | Seen by others |
|---|---|---|---|
| **Personal** (untied) | Always, until this player collects it | Player profile: collected IDs (one-time per player) | No |
| **Quest-tied** | Only while the linked quest is **active** for this player | The quest's progress entry, for this run only (cleared when a repeatable quest restarts) | No |
| **Shared** | Until anyone collects it | Match state on the authority | Yes, replicated |

Lobby and quest collectibles are usually personal: each player sees their own world state. Match pickups (weapons, power-ups) are shared.

## Flow (quest-tied)

```text
Validate:  quest is Active for this player AND not already collected in this run
Execute:   data service adds the ID to the quest's collected list,
           increments the collectible type count, saves,
           raises ProfileChanged → CollectibleCollected(typeId) → StatChanged
Quest:     "collect N of type X" objective advances
Present:   hide the object for this player
```

## Ideas
- Each collectible has an **instance ID** (this exact object) and a **type ID** (what kind it is, for example `token`). Quests count by type, and saves track by instance.
- Visibility refreshes on events (profile changed, quest started or completed), never by polling every frame.
- A template prefab and a setup wizard make new collectible variants quick to create. See [[Template Prefabs and Setup Wizards]].

## Related
- [[Interactables]]
- [[Quest System]]
- [[Local vs Shared State]]
