# Private vs Public Matches

## The problem
A player-hosted server is cheap, but the host's machine runs the authority, so the host can cheat. If those matches grant ranked progress or valuable rewards, cheating becomes an unfair advantage.

## My approach: make private matches useless for competitive advantage

| | Private match | Public match |
|---|---|---|
| Server | Player-hosted (listen server, optionally with a relay) | Dedicated server, allocated on demand |
| Trust | **Untrusted** | **Trusted** |
| Ranked placement / leaderboards | No | Yes |
| Valuable currency or rewards | No, or a limited non-competitive set | Yes |
| Hosting cost | ~$0 | Paid per running server |

Cheating in a private match then only affects the friends who chose to play with that host.

## Rules
- The backend accepts competitive results only from servers it allocated and can verify, for example with a server token issued at allocation.
- The match type is decided at session creation and stored server-side, never sent as a flag the client can change.
- Private-match rewards, if any, should be capped and non-tradeable.

## Related
- [[Multiplayer Topologies]]
- [[Dedicated Server Hosting]]
- [[Backend and Persistence]]
