# Third-Person Character Movement

How my Rigidbody-based third-person controller works: locomotion, crouch and roll, climbing, foot IK, procedural crouch, and the animator. The scripts are split by responsibility, and each one is one component.

```text
PlayerInputHandler  → reads Input System actions, exposes state (Move, Look, Jump, Sprint, Crouch, Aim…)
PlayerLocomotion    → physics movement (FixedUpdate)
PlayerClimb         → ledge detection and scripted mantle
PlayerAnimator      → pushes state to Animator parameters
PlayerFootIK        → feet on uneven ground (OnAnimatorIK)
ProceduralCrouch    → additive bone bend (LateUpdate)
PlayerAimCamera     → blends the Cinemachine orbit for aiming
```

## Input
- Input System with a `PlayerInput` component, and callbacks named `OnXxx(InputAction.CallbackContext)`.
- Held buttons (sprint, crouch, aim) are tracked from `started` to `canceled`, so the state is reliable even when frames drop.
- Gameplay scripts read the handler's properties and never touch the devices directly.

## Locomotion (Rigidbody, `FixedUpdate`)
- **Camera-relative direction:** take the camera's forward and right vectors flattened on the ground plane, then combine them with the move input.
- **Acceleration and deceleration:** Lerp the current horizontal velocity toward the target velocity, with separate rates for speeding up and slowing down.
- **Air control:** a reduced acceleration in the air that keeps momentum instead of snapping.
- **Slopes:** project the desired velocity on the ground plane (`Vector3.ProjectOnPlane(velocity, groundNormal)`), so the character follows slopes instead of bouncing off them.
- **Ground check:** a `SphereCast` down from inside the capsule. It counts as grounded only if the hit normal is steeper than a minimum floor normal, so walls don't count as ground.
- **Jump:** zero the vertical velocity, then apply an `Impulse` force. A short **post-jump ungrounded lock** stops the ground check from re-grounding on the first frames.
- **Physics material with zero friction** on the capsule, so the character doesn't stick to walls. Movement code handles stopping.
- **Rotation:** turn toward the movement direction. If the input is more than about 90° away, play a **turn-in-place** animation first.

## Crouch and roll
- **Crouch:** shrink the capsule height and center. Before standing up, check `CanStandUp` with a `SphereCast` upward, so the player can't stand into a ceiling.
- **Roll:** triggered by crouch while sprinting. Speed starts high and decays over the roll. The capsule takes a low pose during it.

## Climb / mantle
**Detection** uses three probes:
1. A forward raycast to find a wall.
2. A downward raycast from above the wall to find the ledge top.
3. A sphere cast for headroom on the ledge.

There are separate probes for grounded and airborne states, so the player can grab ledges mid-jump.

**Execution:** a scripted L-shaped path (up first, then forward onto the ledge) with `Rigidbody.MovePosition`. Gravity is off during the climb.
- The animation's root motion is swallowed, and the hips XZ are locked, so the code controls position while the clip handles the pose.
- The clip's vertical hop can be scaled to match different ledge heights.

## Foot IK (`OnAnimatorIK`)
- Raycast down from each foot. Set the IK position to the hit point plus the foot offset, and the IK rotation aligned to the surface normal.
- In `LateUpdate`, **lower the hips** by half the height difference between the feet, so the lower foot can reach the ground.
- Blend the IK weight out while airborne or rolling.

## Procedural crouch (`LateUpdate`)
- An additive rotation on bones (spine, chest, head, thighs, shins, arms) around axes relative to the character, plus a hip drop.
- Blended by a weight, so it layers on top of any animation without needing crouch versions of every clip.

## Animator
- Parameters are cached as hashes (`Animator.StringToHash`): InputX/Y, IsMoving, IsSprinting, IsCrouching, IsRolling, IsAiming, IsClimbing, IsTurning, TurnDirection, IsGrounded, VerticalVelocity.
- A masked **arms layer** for upper-body poses while sprinting.

## Aim camera
Blend the Cinemachine `OrbitalFollow` radius, the offset, and the field of view between normal and aim values for an over-the-shoulder zoom.

## Multiplayer notes
- The owning client simulates its own movement for responsiveness (client-side prediction). The authority validates or reconciles it, depending on the netcode choice.
- Remote players get interpolated transforms and animator parameters, and don't run physics movement.
- Foot IK and procedural crouch are purely cosmetic, so they run locally on every client.

## Related
- [[Camera and Character Directors]]
- [[Multiplayer Topologies]]
