# Multiplayer Topologies

## Main options

| Topology | Who runs the authority | Pros | Cons |
|---|---|---|---|
| **Listen server / player-hosted** | One player's machine (the host) | Free to run, simple for friends and private lobbies | Host advantage, host can cheat, session ends when the host leaves, limited by the host's upload bandwidth |
| **Dedicated server** | A server process in a data center, with no player | Fair, harder to cheat, stable, scales to large lobbies | Costs money per running server, needs a headless build and hosting setup |
| **Relay** | Still a host, but traffic passes through a relay service | Solves NAT traversal without port forwarding | Adds latency and relay cost. Does not fix trust. |
| **Peer-to-peer (lockstep / distributed)** | Shared between peers | No server cost | Hard to secure, desync risk, poor fit for 30+ players |

## What games usually do
- Competitive and public matchmaking: **dedicated servers**.
- Private matches or co-op: often **player-hosted** (sometimes with a relay), or dedicated servers spun up on demand.
- Many games support both. They use **one gameplay codebase** and run it either inside the host's client or in a headless server build.

### Example: PvZ Garden Warfare 2
Public matchmaking uses EA dedicated servers drawn from a pool. A private match is also given a temporary dedicated server allocated for that session, instead of running on a player's machine.

### Example: Roblox
The platform provides and pays for servers. Developers never manage hosting. Each experience instance is a server Roblox spins up.

## Server pools
A **pool** (or fleet) is a set of servers or capacity kept ready by a hosting provider. When matchmaking forms a match, an **allocator** picks a free server (or starts one), gives the clients its IP and port, and shuts it down after the match. "Scale to zero" means no running servers, and no server cost, when nobody plays.

## Supporting both host and dedicated
Write gameplay against an **authority interface**, not against "am I the host". The same authority code runs:
- in-process for solo;
- inside the host client for private matches;
- in a headless server build for public matches.

Player data never lives in the match authority long-term. Both modes load and save through the same backend. See [[Backend and Persistence]].

## Related
- [[Private vs Public Matches]]
- [[Dedicated Server Hosting]]
- [[Authority Model]]
- [[Development Before Netcode]]
