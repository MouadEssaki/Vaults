# Assembly Definitions (`.asmdef`)

By default, Unity compiles every script into one assembly, `Assembly-CSharp`. An **assembly definition** file placed in a folder makes the scripts in that folder (and its subfolders) compile into their own separate assembly (DLL).

## Pros
- **Faster iteration.** Changing a script only recompiles its assembly and the assemblies that depend on it, not the whole project.
- **Enforced boundaries.** An assembly can only use what it explicitly references, so dependency rules can't be broken by accident (UI can't reach into a netcode implementation).
- **Test assemblies.** Required for proper Edit Mode and Play Mode test assemblies.
- **Isolation of errors.** Editor-only code stays out of builds.

## Cons
- More setup: every reference must be declared, including package assemblies (Cinemachine, Input System, TextMeshPro).
- Circular dependencies are not allowed, so contracts have to be split from implementations.
- Scripts in `Assembly-CSharp` can use asmdef code, but asmdef code can't reference `Assembly-CSharp`.

## When to add them
- Small project or prototype: skip them.
- Growing codebase with clear systems, tests, or long compile times: add them, usually one per system plus a `Core` assembly for shared contracts.

## Related
- [[Unity Project Conventions]]
