# LSL-RLV-LookAt

An LSL script for Second Life that makes a worn attachment turn its wearer's avatar to face whoever touches (clicks) it, using the RestrainedLove (RLV) API.

## Overview

Standard LSL rotation functions (`llSetRot`, `llTargetOmega`, `llRotLookAt`) only affect prims/objects — they cannot rotate an avatar's body. To turn the *wearer* toward the toucher, this script sends an RLV `@setrot` force command to the wearer's own viewer instead.

## How It Works

1. The script sits inside an object worn by the avatar (e.g. a HUD, collar, or accessory).
2. On `touch_start`, it captures the toucher's key and world position with `llDetectedKey(0)` / `llDetectedPos(0)`.
3. It computes the yaw angle from the wearer to the toucher:
   ```lsl
   vector pointTo = llDetectedPos(0) - llGetPos();
   float angle = llAtan2(pointTo.x, pointTo.y);
   llOwnerSay("@setrot:" + (string)angle + "=force");
   ```
4. RLV, running in the wearer's viewer, rotates the avatar to face that heading.

## Requirements

- An RLV-enabled viewer for the **wearer** (e.g. Firestorm with RLVa enabled, Catznip). `@setrot` is a one-shot forced command processed client-side, not a persistent restriction — no relay or third-party consent script is needed for this specific command.
- The script must run inside an object attached to (worn by) the avatar that should turn.

## Installation

1. Copy the `.lsl` file into a prim.
2. Attach/wear that object.
3. Ensure `llOwnerSay` messages sent by the object aren't filtered by an RLV relay/blacklist.

## Usage

Touch the attachment (or have another avatar touch it, depending on your permissions setup) and the wearer will rotate to face the point that was touched/the toucher's position.

## Known Limitations

- `@setrot` is imprecise: RLV ignores rotation requests under roughly 6–10°, so very small heading changes may not register ([RLV specification](https://grimore.org/fuss/lsl/restrained_love_viewer/specification)).
- Has no effect on a seated avatar unless a sit target is involved — RLV rotation only works for standing avatars.
- Because `@setrot` is a one-shot force command rather than a restriction, it won't appear in the viewer's active-restrictions list; use RLVa's debug chat logging to verify it's firing.
- Only rotates yaw (heading) — it does not tilt or otherwise reposition the avatar.

## Roadmap

- [ ] Add a cooldown/debounce so rapid repeat touches don't spam `@setrot`
- [ ] Optional double-rotation workaround for angle changes under the 10° threshold
- [ ] Permission/owner-only touch mode

## License

[MIT](LICENSE)
