# Backend and Persistence

## What "the backend" actually is
The backend is **not** the registries, and it is **not** the match server. It is a separate, always-available service that owns long-term account data:

- authentication and account IDs;
- player profiles: currencies, XP and levels, unlocks and cosmetics, quest progress, personal collectibles;
- server allocation calls to the hosting provider;
- accepting trusted match results.

```text
Game client ──► Backend API ──► Database
     │               │
     │               └──► Hosting provider API (allocate a match server)
     ▼
Match server (host or dedicated) ──► Backend API (load/save profiles, submit results)
```

Match servers are temporary. They load the profiles of connected players, cache them during the match, and send changes back. The backend is the source of truth.

## Layers in the game code

| Layer | Responsibility | Example |
|---|---|---|
| Player data service (`IPlayerDataService`) | Gameplay-facing API, authority-only. Validates and applies changes, raises events. | `AddCurrency(player, "gold", 50)` |
| Data store (`IPlayerDataStore`) | Loads and saves profiles. Swappable. | Local JSON now, remote API later |
| Backend | Real storage and rules | Database behind an HTTP API |

Gameplay code only talks to the service. Swapping local JSON for a remote backend changes one adapter.

## Hosted backend stacks

### AWS serverless
- Cognito (authentication), API Gateway plus Lambda (API), DynamoDB (profiles), S3 (files and builds), CloudWatch (logs).
- Costs scale with requests and are near zero at small scale. A generous free tier exists for several services.
- Most control, more code to write.

### PlayFab
- Authentication, player data, inventory, economy, and leaderboards out of the box.
- Less backend code, but plan limits (Evaluation: 1,000 lifetime players) and a monthly fee on paid plans.

### Edgegap plus a backend
Edgegap only hosts match servers. You still need somewhere to run the backend API and database (AWS, PlayFab, or another host). The backend is what calls Edgegap to start servers.

## Local development
- A JSON file per profile in `Application.persistentDataPath`.
- An in-memory store for automated tests.
- Include a `Version` field in the profile for future migrations.
- Write to a temp file, then replace, so a crash mid-save doesn't corrupt the profile.

## Related
- [[Player Data and Progression]]
- [[Dedicated Server Hosting]]
- [[Development Before Netcode]]
