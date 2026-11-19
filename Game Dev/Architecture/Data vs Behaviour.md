# Data vs Behaviour

Keep **data** (what something is) separate from **behaviour** (what it does in the scene).

| Kind | Use for | Examples |
|---|---|---|
| Plain C# `[Serializable]` classes | Data that is saved or sent over the network | Player profile, quest progress |
| ScriptableObject | Designer-authored content shared by many objects | Screen definitions, dialogues, quests, currencies, level curves |
| MonoBehaviour | Scene presence and Unity lifecycle | Interactables, views, service hosts |
| Plain C# services | Game logic with no scene dependency | Player data service, quest service |

Why: data is what gets serialized and synced. Logic in plain classes can be tested without a scene.

## Polymorphic lists in ScriptableObjects
Use `[SerializeReference]` to store a list of different subclasses, for example dialogue actions or quest objectives:

```csharp
[SerializeReference] public List<DialogueAction> actions;
```

Unity's default inspector can't add subclass instances to such a list. A small custom property drawer (a "subclass picker" attribute) shows a dropdown of concrete types.

## ScriptableObjects can't reference scene objects
Store a string key and resolve it at runtime through a registry. See [[Registry Pattern]].

## Related
- [[Dialogue System]]
- [[Quest System]]
