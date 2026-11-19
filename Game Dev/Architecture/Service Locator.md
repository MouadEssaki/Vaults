# Service Locator

A small static class that maps an **interface type** to the one instance currently providing it.

```csharp
public static class Services
{
    private static readonly Dictionary<Type, object> map = new();

    public static void Register<T>(T service) where T : class
    {
        if (map.ContainsKey(typeof(T))) { Debug.LogError($"{typeof(T).Name} already registered"); return; }
        map[typeof(T)] = service;
    }

    public static void Unregister<T>(T service) where T : class
    {
        if (map.TryGetValue(typeof(T), out var current) && current == service) map.Remove(typeof(T));
    }

    public static T Get<T>() where T : class => (T)map[typeof(T)];
    public static bool TryGet<T>(out T service) where T : class
    {
        var found = map.TryGetValue(typeof(T), out var o);
        service = o as T;
        return found;
    }
}
```

Usage from any script:

```csharp
Services.Get<IPlayerDataService>().AddCurrency(playerId, "tickets", 50);
```

## Singleton vs service locator

| | Singleton (`CameraDirector.Instance`) | Service locator (`Services.Get<ICameraDirector>()`) |
|---|---|---|
| Ease of use | Simplest | Almost the same |
| Callers depend on | The concrete class | An interface |
| Swapping the implementation | Hard | Easy: register another class |
| Testing with fakes | Hard | Easy |

The locator matters most for systems that will change when netcode arrives, for example swapping a local player data service for one that sends requests to a server.

## Registration rules (Unity)
- Service hosts register in `Awake` with an early execution order (for example `[DefaultExecutionOrder(-100)]`).
- Consumers resolve services in `Start`, never in `Awake`, because two early hosts run `Awake` in an undefined order.
- A duplicate registration logs an error and keeps the first instance.
- Components should tolerate a missing service (log one warning) so test scenes without managers still run.

## Alternatives
- ScriptableObject event channels plus inspector references.
- A DI framework (VContainer, Zenject). More power, more setup, adds a package.

## Related
- [[Registry Pattern]]
- [[Development Before Netcode]]
