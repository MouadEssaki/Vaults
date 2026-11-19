# Player Data and Progression

Unity has no built-in player database. `PlayerPrefs` is fine for settings, not for progression. Build a small service instead.

## Layers

```text
Gameplay code ──► IPlayerDataService (authority-only mutations, events)
                        │
                        ▼
                  IPlayerDataStore (Load / Save)
                        │
          ┌─────────────┴─────────────┐
     JSON file (dev)           Remote backend (release)
```

## Profile (serializable data)

```csharp
[Serializable] public class PlayerProfile
{
    public int Version = 1;              // for future migrations
    public string PlayerId;
    public List<CurrencyBalance> Currencies;
    public long Xp;                      // total XP earned
    public int GamesPlayed;
    public long TotalScore;
    public List<string> CollectedCollectibleIds;
    public List<string> OwnedCosmeticIds;
    public List<QuestProgressData> Quests;
}
```

Use lists, not dictionaries, because Unity's `JsonUtility` can't serialize dictionaries.

## Service API (shape)

```csharp
long GetBalance(PlayerId p, string currencyId);
void AddCurrency(PlayerId p, string currencyId, long amount);
bool TrySpendCurrency(PlayerId p, string currencyId, long amount);
void AddXp(PlayerId p, long amount);
int  GetLevel(PlayerId p);
void RecordGameResult(PlayerId p, int placement, long score);
event Action<PlayerId> ProfileChanged;
```

- IDs are strings and amounts are `long`, so the API is easy to serialize over the network.
- Every mutation saves, then raises `ProfileChanged`, then raises gameplay events.

## Currencies
- Data-driven: one `CurrencyDefinition` asset per currency (ID, name, icon). Supports any number of currencies, for example a main one and a premium one.
- **Real money only grants an existing currency.** It never creates a separate kind of value, so the economy stays in one place.

## Levels
A `LevelCurve` asset: the XP needed to go from level `L` to `L + 1` is `round(baseXp * L^exponent)`. Store **total XP**, and compute the level from it.

Example with `baseXp = 100`, `exponent = 1`: level 2 is reached at 100 total XP, level 3 at 300.

## Rewards from matches
Placement-based rewards are computed on the authority at the end of a match, and can differ per game mode.

## Related
- [[Backend and Persistence]]
- [[Gameplay Events]]
- [[Private vs Public Matches]]
