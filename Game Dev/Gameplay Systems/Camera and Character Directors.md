# Camera and Character Directors

Two small services that other systems call to take temporary control of the camera or the player character. Dialogues, cutscenes, and triggers don't need to know about Cinemachine or physics.

## Camera director

```csharp
public struct CameraShotRequest
{
    public Transform Anchor;   // where the camera goes
    public Transform LookAt;
    public float BlendIn, Hold, BlendOut;
    public bool ReturnToGameplay;
}

public interface ICameraDirector
{
    void PlayShot(CameraShotRequest request, Action onComplete);
    void ReturnToGameplay(float blend);
    bool IsPlaying { get; }
}
```

- Implemented with a Cinemachine camera whose priority is raised above the gameplay camera, so the Cinemachine brain blends between them.
- **Guarantee exactly one `onComplete` per `PlayShot`** in every case: normal end, interrupted by a new shot, null anchor, or component disabled. Callers chain on the callback and never poll `IsPlaying`.
- Callers own input locking.

## Character director

```csharp
public interface ICharacterDirector
{
    void Teleport(PlayerId player, Vector3 position, Quaternion rotation);
}
```

- Teleport the Rigidbody (reset velocity), and optionally realign the follow camera behind the player.
- Walk-to (a navmesh path) can be added later behind the same interface.

## Multiplayer
- Camera shots are **local-only**.
- Teleporting a player changes their position, which is **shared** state. In a networked game the authority performs it, or the owning client does it with authority approval, and it is replicated.

## Related
- [[Dialogue System]]
- [[Third-Person Character Movement]]
