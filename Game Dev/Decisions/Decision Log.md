# Decision Log

My default choices for game projects, and why. Override per project when needed.

## Architecture

| Decision | Choice | Why |
|---|---|---|
| Authority | Request → validate → execute → present, always, even offline | Netcode can be added later without a rewrite. See [[Authority Model]]. |
| Player references | Never a static "the player". Pass a player ID or context. | Multiplayer-safe by default |
| Effect classification | Every effect is labelled local-only, personal, or shared | Decides what needs syncing. See [[Local vs Shared State]]. |
| Wiring | Static service locator with interfaces | Almost as simple as singletons, but swappable and testable. See [[Service Locator]]. |
| Decoupling | Gameplay events for "something happened" | Quests and achievements don't depend on the systems they track |
| Runtime lookup | Registries (dictionaries keyed by ID) | Constant-time lookups, no `Find*` calls |
| Content data | ScriptableObjects, with `[SerializeReference]` plus a subclass picker for polymorphic lists | Designer-friendly, no code for new content |
| Folder layout | One root folder per system | Easy ownership, ready for asmdefs |

## Multiplayer and hosting

| Decision | Choice | Why |
|---|---|---|
| Development before netcode | In-process local authority, local JSON and in-memory stores | Build and test now, choose providers later. See [[Development Before Netcode]]. |
| Private vs public | Private matches player-hosted and untrusted; public matches on dedicated servers | Free casual play without competitive cheating. See [[Private vs Public Matches]]. |
| Dedicated hosting (leaning) | On-demand with scale to zero (Edgegap first, GameLift if very large) | Lowest cost for an indie game with an uncertain player count. See [[Dedicated Server Hosting]]. |
| Backend | Separate from match servers; owns accounts and profiles | Match servers are temporary. See [[Backend and Persistence]]. |
| Priority | Cost over complexity | I'm a software engineer and complexity is fine |
| Still open | Netcode library, AWS vs PlayFab backend, final host | Decide before the multiplayer integration plan |

## Gameplay systems

| Decision | Choice |
|---|---|
| Interaction trigger | Tap or Hold, chosen per object |
| Interaction targeting | Proximity plus facing, with a physics overlap query |
| Cooldowns | Global for shared objects, per player for local ones |
| UI tech | uGUI plus TextMeshPro |
| UI structure | One shell with ScriptableObject-defined screens and tab prefabs |
| Currencies | Data-driven, multiple currencies; real money only buys existing currencies |
| Levels | Curve asset, `baseXp * level^exponent`, store total XP |
| Quests | Several active at once, repeatable flag, currency and XP rewards, saved in the profile |
| Collectibles | Personal, quest-tied (visible only while the quest is active), or shared |
| Content creation | Template prefab plus setup wizard per content type |
| Character director | Teleport first, walk-to later |
| Assembly definitions | Not at the start; add when the codebase grows |

## How to add a decision
Add a row with the decision, the choice, and a short reason. If it reverses an earlier one, keep the old row and note what replaced it.
