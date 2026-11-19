# Quest System

Data-driven quests whose objectives advance from [[Gameplay Events]], so the quest system doesn't depend on the systems it tracks.

## Data

```csharp
[CreateAssetMenu] public class QuestDefinition : ScriptableObject
{
    public string Id, Title, Description;
    public bool Repeatable;                 // e.g. daily quests
    [SerializeReference, SubclassPicker] public List<QuestObjective> Objectives;
    public List<CurrencyReward> CurrencyRewards;
    public long XpReward;
}

[Serializable] public abstract class QuestObjective
{
    public string Description;
    public int TargetCount;
    public abstract int Apply(QuestSignal signal, int current); // returns new progress
}
```

## Objective types
| Objective | Listens to | Key |
|---|---|---|
| Collect | `CollectibleCollected` | collectible type ID |
| Talk to NPC | `NpcTalkedTo` | NPC ID |
| Interact | `Interacted` | interactable ID |
| Reach stat | `StatChanged` | stat name; seeded from the current value at start |

## Rules
- A player can have several active quests.
- Signals only count for quests that are **active** when the signal arrives. Nothing counts retroactively, except "reach stat", which checks the current value.
- On completion: grant currency and XP through the player data service, and mark the quest completed. A repeatable quest can be started again, with fresh progress.
- Progress is saved in the player profile.
- A quest tracker HUD can be added later. It's only a view on the quest events.

## Multiplayer
The quest service runs on the authority. Progress is **personal** state, sent only to the owning player.

## Related
- [[Gameplay Events]]
- [[Collectibles]]
- [[Dialogue System]]
- [[Player Data and Progression]]
