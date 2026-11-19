# UI Shell and Tabs

One reusable window (the **shell**) that shows any screen made of **tabs**. An interactable only says which screen to open. The shell handles the window, the tab bar, switching, input locking, and closing.

Similar to micro-frontends on the web: each tab is an independent component, and the shell composes them.

## Data

```csharp
[CreateAssetMenu] public class ScreenDefinition : ScriptableObject
{
    public string Title;
    public List<TabDefinition> Tabs;   // label + tab prefab
}

public abstract class UITabView : MonoBehaviour
{
    public virtual void OnTabShown(ScreenContext context) { }  // context = player + screen
    public virtual void OnTabHidden() { }
    public virtual void OnScreenClosed() { }
}

public interface IUIShell
{
    bool IsOpen { get; }
    void Open(ScreenDefinition screen, PlayerContext player);
    void Close();
    void SelectTab(int index);
    event Action<ScreenDefinition> Opened, Closed;
}
```

## Is it overengineering?
Not if it stays this small: one ScriptableObject per screen, one prefab per tab. A new screen needs no new code, only a new asset. A new tab is one MonoBehaviour.

## Input and cursor
When a UI opens, a **control lock** switches the input action map from Player to UI, unlocks the cursor, and blocks interaction. On close it restores the previous state. Make the lock **counted** (push and pop), so a dialogue and a camera shot overlapping don't unlock too early.

## Multiplayer
UI is **local-only**. Tabs read data through services. Any action that changes data (buying an item) is a **request** to the authority, never a direct write.

## Tech
uGUI with TextMeshPro is simple and mature for runtime game UI. UI Toolkit is good for editor tools and data-heavy screens. Pick one per project for consistency.

## Related
- [[Interactables]]
- [[Player Data and Progression]]
