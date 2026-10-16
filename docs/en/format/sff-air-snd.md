# .sff / .air / .snd and PAMSDK_Unity

All three files are **plain JSON text** (UTF-8, indented); the Unity/engine side can deserialize them with any JSON library. Companion plain-data SDK: repo `Tools/FrameEventEditor.SDK/PAMSDK_Unity.cs` (single file, zero methods, zero dependencies; located at `SDK/PAMSDK_Unity.cs` inside the release package).

## .sff (sprites/atlas)

- `baseMap` / `normalMap`: atlas PNG **bytes**. Under Newtonsoft, `byte[]` serializes as a **base64 string**.
- Sprite metadata includes the rect inside the atlas; **`SpriteMeta.rect` has a bottom-up y axis** (Unity convention) — mind the flip on the engine side.
- The palette is a standalone entry (RGBA).

## Part layers (weapon variants, modelDispMask)

One character showing different weapons does **not** need multiple action sets: draw each weapon on a gal layer named `layer_N` (N=1..9; `layer_0` and any other name = base layer). Import splits them automatically:

- The base group keeps the gal file name (e.g. `Body`) and contains no `layer_N` pixels; each `layer_N` becomes a part group **`Body+1`, `Body+2`, …**
- Part groups are **strictly 1:1 with base group frame numbers** (no weapon pixels that frame = a fully transparent placeholder sprite) — a part's frame number always follows the body event's frame number, so sync is free
- Each part group is cropped independently (tight crop, no transparent padding); alignment comes entirely from the pivot derived from the Center layer — composited by overlaying at the same pivot, identical rects not required
- **Part groups are hidden in the group list** and managed automatically by their base group: importing/re-importing/deleting/renaming the base group carries the `+N` family along; the frame-event group dropdown lists base groups only
- The preview's part checkboxes (1..9) toggle parts live, all on by default — preview-only, never written to data
- **The convention is the manifest**: the `group+N` naming itself is the part definition; there is no manifest file

Engine-side contract (mirrored in PAMSDK_Unity):

- After a `SpriteEvent` applies, look up `group+N` sprites **by the same frame number** and overlay them; the character mask `modelDispMask` uses **bit N = part N visibility** (`SetMaskBit(n, on)` takes effect immediately — switching weapons never changes the animation)
- Missing group / bit off / missing part frame = that part is not drawn; legacy characters without `layer_N` layers behave exactly as before (zero changes to the air/sff/snd formats)

## .air (actions and frame events)

- An action = a frame sequence; each frame can carry several event types.
- **Event polymorphism discriminator = the `"type"` class-name field**:

```json
{ "type": "AttackEvent", …attack-box fields… }
```

  Five types: `SpriteEvent` (display) / `CollisionEvent` (hit box) / `BodyEvent` (body box) / `AttackEvent` (attack box) / `SoundEvent` (sound). Instantiate the matching event class by name and fill the remaining fields.

## .snd (sounds)

- `byte[]` (WAV bytes) uses **Unity JsonUtility's numeric-array format** (`[82,73,70,70,…]`), **not base64** — a different convention from .sff's byte[]; parse by which file the field lives in.

## Notes

- **Not interchangeable with MUGEN's `.air`/`.sff`/`.snd`**: the shared extensions are pure coincidence — this tool's trio is its own JSON format; MUGEN can't read it, and this tool can't read MUGEN's asset files either.
- Classes clashing with your project can be renamed freely; the JSON `"type"` discriminator values stay the same.
- The three files are saved by the editor as a "same-folder same-name trio" (Ctrl+S).
- **Unity import**: Unity doesn't recognize the `.sff/.air/.snd` extensions (they won't come in as TextAsset). In the editor's Save As dialog pick **JSON role (Unity)** as the type to get `Role.sff.json/.air.json/.snd.json` — read the TextAsset's `text` field and hand it to Newtonsoft + the PAMSDK_Unity data classes.
- The format is checked against the ledger by `Tools/FrameEventEditor.SDK/check_fields.py`; game-side Mugen.cs field changes are synced into the SDK.
