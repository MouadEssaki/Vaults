# Local vs Shared State

Before building any effect, classify it. This decides whether it ever needs to go through the network.

| Category | Meaning | Examples |
|---|---|---|
| **Local-only** | Only affects this client's presentation. Never synced. | Opening a menu, moving your own camera, UI hover, cursor lock |
| **Personal (authority-validated)** | Belongs to one player, validated and saved by the authority, but not shown to others. | Personal collectibles, quest progress, currency balance |
| **Shared** | Part of the world everyone sees. Owned by the authority and replicated to all players. | A shared firework exploding, a weapon being picked up, a door opening |

## Why "personal" is its own category

A personal collectible only disappears for the player who took it, so other players never need to hear about it. But it still grants progress or rewards, so a modified client must not be able to claim it alone. The authority validates and saves it, and sends the result **only** to that player.

Purely cosmetic, offline-only things with no reward can be handled entirely on the client.

## Presentation scope

A simple per-object setting covers most cases:

```csharp
public enum PresentationScope { LocalOnly, Everyone }
```

- `LocalOnly`: the acting player sees the effect.
- `Everyone`: all players see it.

Cooldowns follow the same idea: a shared object has one global cooldown, a local object has a cooldown per player.

## Related
- [[Authority Model]]
- [[Collectibles]]
- [[Interactables]]
