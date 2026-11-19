# Gameplay Events

Gameplay events are notifications about things that **already happened**. Interested systems react, and the system that raised the event doesn't know who is listening.

```csharp
public interface IGameplayEvents
{
    event Action<PlayerId, string> Interacted;            // interactable id
    event Action<PlayerId, string> CollectibleCollected;  // collectible type id
    event Action<PlayerId, string> NpcTalkedTo;           // npc id
    event Action<PlayerId, PlayerStat, int> StatChanged;
}
```

## Example flow

```text
Player collects an item
    → authority validates
    → player data service records it
    → raises CollectibleCollected(player, "token")
    → quest service advances "collect 3 tokens"
    → UI refreshes
```

The quest system never needs a reference to the collectible, the NPC, or the minigame. It only listens for events.

## Registry vs events
- **Registry:** "What exists, and how do I find it?"
- **Event:** "Something happened; whoever cares may react."

## Rules
- Raise events on the **authority**, after the change is validated and saved.
- Raise them in a fixed order: change the data, save, notify data changed, then raise gameplay events. Listeners then always see consistent data.
- Clients should receive the resulting state, not trust their own local copy of an event.
- Unsubscribe in `OnDisable` or `OnDestroy` to avoid handlers on destroyed objects.

## Related
- [[Quest System]]
- [[Authority Model]]
