# .sff / .air / .snd and PAMSDK_Unity

All three files are **plain JSON text** (UTF-8, indented); the Unity/engine side can deserialize them with any JSON library. Companion plain-data SDK: repo `Tools/FrameEventEditor.SDK/PAMSDK_Unity.cs` (single file, zero methods, zero dependencies; located at `SDK/PAMSDK_Unity.cs` inside the release package).

## .sff (sprites/atlas)

- `baseMap` / `normalMap`: atlas PNG **bytes**. Under Newtonsoft, `byte[]` serializes as a **base64 string**.
- Sprite metadata includes the rect inside the atlas; **`SpriteMeta.rect` has a bottom-up y axis** (Unity convention) — mind the flip on the engine side.
- The palette is a standalone entry (RGBA).

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
