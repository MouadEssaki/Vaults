# Dedicated Server Hosting

Research for an indie game that needs on-demand dedicated servers and has to start cheap but be able to scale. Prices are as researched in late 2026. **Check current pricing before committing.**

## Status of common providers
- **Unity Multiplay:** deprecated (shut down April 1, 2026). Do not use.
- **Edgegap:** container-based, on-demand, pay per vCPU-minute. Very good fit for small teams.
- **AWS GameLift Servers:** fleets of instances, autoscaling, FlexMatch matchmaking. More control, more setup.
- **PlayFab Multiplayer Servers:** tied to PlayFab. Evaluation mode is limited to 1,000 lifetime players, and the Standard plan is about $99/month.
- **Self-managed VPS (Hetzner, OVH, …):** cheapest raw compute, but you build allocation and scaling yourself, and you pay even when idle.

## Edgegap
- **Model:** you push a Linux server container. The backend calls Edgegap's API to deploy one server per match near the players.
- **Price:** about $0.00115 per vCPU-minute (≈ $0.069 per vCPU-hour), plus about $0.10 per GB of outbound traffic.
- **Idle cost:** $0. On-demand deployments only bill while running.
- **Free trial:** 1 app, 1 concurrent deployment, 1.5 vCPU and 3 GB of memory, 60-minute cap per deployment. Enough for development tests.
- **The API key must stay in the backend**, never in the game client.

## AWS GameLift
- **Model:** a fleet of EC2 instances runs your server processes. GameLift places sessions, and FlexMatch handles matchmaking.
- **Price:** per instance-hour, billed per second with a 1-minute minimum. Spot instances are 50–85% cheaper but can be interrupted. Newer instance generations include free bandwidth.
- **Idle cost:** set the fleet minimum to 0 to pay nothing for instances when no one plays (at the cost of a cold start).
- **Free tier:** GameLift Anywhere (use your own machine as a fleet) is free for 12 months, with about 3,000 game sessions and 500k connection-minutes per month. FlexMatch includes 50k matchmaking packages per month free.
- After the free period, even development testing costs something unless you test against local servers.

## Comparison

| | Edgegap | AWS GameLift |
|---|---|---|
| Setup effort | Low | Medium to high |
| Cost when idle | $0 | $0 with minimum fleet 0 |
| Cost per match | Per vCPU-minute, small and predictable | Per instance-hour. Cheaper at scale, especially with Spot. |
| Matchmaking | Bring your own, or their add-on | FlexMatch included |
| Best when | Small team, uncertain player count | Large scale, already on AWS |

## Decision framework
1. Development: test locally (a local headless build) plus the Edgegap free trial or GameLift Anywhere.
2. Launch: Edgegap on-demand, with no idle cost and a simple setup.
3. If the game grows a lot: compare per-match cost against GameLift with Spot capacity, and migrate only the allocator.

Keep the provider behind an **allocator interface** (for example `IGameServerAllocator`), implemented only in the backend. Switching hosts then doesn't touch gameplay code.

## Related
- [[Multiplayer Topologies]]
- [[Backend and Persistence]]
