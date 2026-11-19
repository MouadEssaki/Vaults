# Dialogue System

A node graph stored in a ScriptableObject. Nodes can have choices, and both nodes and choices can run **actions** (start a quest, play a camera shot, teleport the player, give an item).

## Data

```csharp
[CreateAssetMenu] public class DialogueDefinition : ScriptableObject
{
    public string StartNodeId;
    public List<DialogueNode> Nodes;
}

[Serializable] public class DialogueNode
{
    public string Id, Speaker, Text, NextNodeId;
    public List<DialogueChoice> Choices;
    [SerializeReference, SubclassPicker] public List<DialogueAction> OnEnterActions;
}

[Serializable] public class DialogueChoice
{
    public string Text, NextNodeId;
    [SerializeReference, SubclassPicker] public List<DialogueAction> Actions;
}

[Serializable] public abstract class DialogueAction
{
    public abstract void Execute(DialogueActionContext context); // player, speaker, anchor lookup
}
```

Concrete actions: `StartQuestAction`, `CameraShotAction` (with follow-up actions after the shot), `TeleportPlayerAction`, `EndDialogueAction`.

## Service

```csharp
void StartDialogue(DialogueDefinition d, PlayerContext p, Transform speaker, Action onFinished);
void Advance(PlayerId p);             // continue a node without choices
void Choose(PlayerId p, int index);   // pick a choice
void Stop(PlayerId p);
event Action<PlayerId, DialogueNode> NodeEntered;
void SuspendPresentation(PlayerId p); // counted, e.g. during a camera shot
void ResumePresentation(PlayerId p);
```

Sessions are **per player**. Many players can talk to the same NPC at once.

## Design choices
- Nodes are a flat list with string IDs. It's simple to edit in the inspector and easy to move to a graph editor later.
- Scene positions are referenced by **anchor keys**, resolved first among the speaker's children, then in a global anchor registry.
- The dialogue box hides during camera shots and waits for the shot's completion callback. Don't poll.
- Actions that change shared or persistent state (starting a quest, giving currency) call authority services.

## Related
- [[Quest System]]
- [[Camera and Character Directors]]
- [[Data vs Behaviour]]
