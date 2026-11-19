# Interactables

A generic system for anything a player can interact with: shops, lockers, NPCs, collectibles, buttons, props. Adding a new type should be cheap: subclass, override one method, done.

## Structure

```text
IInteractable              (capability: what any interactable exposes)
  └─ InteractableBase      (abstract MonoBehaviour: shared fields, registry, cooldown)
       ├─ ScreenInteractable       opens a UI screen
       ├─ NpcInteractable          starts a dialogue
       ├─ CollectibleInteractable  grants a collectible
       └─ EffectInteractable       plays an effect (firework, sound, animation)
```

```csharp
public interface IInteractable
{
    InteractableId Id { get; }
    string PromptText { get; }
    Transform PromptAnchor { get; }
    PresentationScope Scope { get; }   // LocalOnly or Everyone
    float Cooldown { get; }
    InteractionMode Mode { get; }      // Tap or Hold
    float HoldDuration { get; }

    bool IsAvailableFor(PlayerContext player);           // should the prompt show at all?
    InteractionValidation Validate(PlayerContext player); // authority: is this allowed?
    void Execute(PlayerContext player);                   // authority: change state
    void Present(PlayerContext player);                   // client: show the result
}
```

## Flow

```text
PlayerInteractor (client)
  finds nearby candidates with a physics overlap (OverlapSphereNonAlloc, interactables layer)
  picks the best one by distance and facing direction
  shows the prompt (with a fill bar for Hold)
  on press (Tap) or after the hold completes (Hold) → submits InteractionRequest(playerId, targetId)

InteractionAuthority
  resolves player and target through registries
  checks the cooldown, then target.Validate(player)
  target.Execute(player)
  raises Interacted gameplay event
  routes Present() to the acting player (LocalOnly) or to everyone (Everyone)
```

## Design choices
- **Tap vs Hold per object.** Hold is for things that should not trigger by accident. The hold is timed on the client because it's input feel. Releasing, losing the target, or opening a UI cancels it.
- **Cooldowns:** an `Everyone` object has one global cooldown, and a `LocalOnly` object has a cooldown per player.
- **Stable IDs.** Each scene instance gets a unique ID (an auto GUID or a readable name) so requests and save data can reference it.
- **Registration.** Register in `OnEnable` and unregister in `OnDisable`. If the registry isn't ready yet, retry once in `Start`.
- **Physics overlap instead of trigger colliders on every object.** One query per local player avoids per-object `Update` and scales to many players.
- Subclasses that override `Reset`, `OnValidate`, or `OnEnable` call `base` first.

## Effect classification examples

| Interactable | Execute (authority) | Present (client) | Category |
|---|---|---|---|
| Shop kiosk | nothing, or a log entry | open the shop screen | Local-only |
| NPC | raise "talked to" event | start the dialogue box | Local-only presentation, authority event |
| Personal collectible | record in the player's profile | hide it for that player | Personal |
| Shared firework | start global cooldown | everyone sees the explosion | Shared |

## Related
- [[Authority Model]]
- [[Local vs Shared State]]
- [[Collectibles]]
- [[Registry Pattern]]
- [[Template Prefabs and Setup Wizards]]
