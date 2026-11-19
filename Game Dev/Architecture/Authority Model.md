# Authority Model

The **authority** is the single place that decides what is true in a game session: whether an action is allowed, and what the resulting state is.

## Three steps for every action

```text
Client intent  →  Authority validates and executes  →  Clients present the result
```

1. **Intent (request).** The client says what it wants: "player X wants to interact with object Y". It never changes shared state itself.
2. **Validation and execution.** The authority checks the request (does the player exist, is the target in range, is it on cooldown, is it allowed) and then applies the change.
3. **Presentation.** Clients show the result: open a UI, play an effect, update a counter.

Keep these as **separate methods**, even in a single-player build. A network transport can then be inserted between step 1 and step 2 later without rewriting the systems.

## Example

```csharp
// Client side: intent only
authority.Submit(new InteractionRequest(localPlayer.Id, target.Id));

// Authority side
public void Submit(InteractionRequest request)
{
    if (!players.TryGet(request.Player, out var player)) return;
    if (!registry.TryGet(request.Target, out var target)) return;

    var validation = target.Validate(player);
    if (!validation.Allowed) { Rejected?.Invoke(request, validation); return; }

    target.Execute(player);           // changes state
    events.RaiseInteracted(player.Id, target.Id.Value);
    Present(target, player);          // routes presentation to one or all players
}
```

## Where the authority runs

| Mode | Authority runs on |
|---|---|
| Solo / offline | The local game, in-process |
| Listen server (player-hosted) | The host player's machine |
| Dedicated server | A server process with no player |

The gameplay code should not care which one it is. See [[Multiplayer Topologies]].

## Rules
- Never use a static "the player". Every system receives the acting player (an ID or context object) as a parameter.
- Player-owned data (currency, XP, unlocks, stats) is only changed by authority-side services.
- Some things can be timed on the client for responsiveness (for example a hold-to-interact timer), but the authority still validates range, cooldown, and rules.

## Related
- [[Local vs Shared State]]
- [[Registry Pattern]]
- [[Private vs Public Matches]]
