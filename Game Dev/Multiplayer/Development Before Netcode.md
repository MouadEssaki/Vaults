# Development Before Netcode

How to build and test gameplay systems **before** choosing a netcode library, a server host, or a backend, without having to rewrite them later.

## Policy
- All gameplay runs through an **in-process local authority** that uses the same request → validation → execution → result flow a real server would use.
- Systems depend on **interfaces**: authority, player data service, quest service, gameplay events.
- Persistence uses **local JSON** and **in-memory** adapters behind a store interface.
- No gameplay code references a hosting provider, a backend provider, or a netcode package.
- Hosting, transport, authentication, and remote saving are later **integration** steps.

## Why it works
The local authority stops the code from quietly becoming client-authoritative. When netcode arrives, a network transport is inserted between "client sends request" and "authority executes". The gameplay classes stay the same.

## What changes later
1. Choose the netcode library and server host.
2. Choose the backend and identity provider.
3. Replace the local data store with a remote one.
4. Add network replication for shared state, and targeted messages for personal state.
5. Add a headless server build, the allocator, and server startup and shutdown.
6. UI, dialogue, quests, and camera stay on the same interfaces.

## Testing
- Unit tests against in-memory fakes.
- Play Mode tests with the local authority and a temporary JSON store.
- Simulate several players by registering several player IDs in the player registry, even with one machine.

## Related
- [[Authority Model]]
- [[Backend and Persistence]]
- [[Multiplayer Topologies]]
