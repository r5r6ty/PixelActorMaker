# Frame Event Editor (sff/air/snd)

> TODO: screenshots.

## The trio

| File | Content | Format |
|---|---|---|
| `.sff` | atlas (base + normal) + sprite metadata + palette | JSON text |
| `.air` | actions (animations) and per-frame **five event types** | JSON text |
| `.snd` | sound effects (WAV bytes) | JSON text |

All three live in the same folder with the same base name. Data structures: [Data Format](../format/sff-air-snd.md).

**Unity delivery (`.json` double-extension form)**: in the save dialog's "Save as type" pick **"JSON role (Unity)"** and the trio is saved as `Role.sff.json / Role.air.json / Role.snd.json` — Unity imports `.json` as a TextAsset, directly readable. Both the open dialog and the command line accept the form; the two forms are the same logical file and can be converted freely (pick the other type in Save As).

## Five frame event types

Each animation frame can carry multiple events; the `"type"` field discriminates the class:

| type | Meaning |
|---|---|
| `SpriteEvent` | display: references an image group/sprite |
| `CollisionEvent` | hit box (collision) |
| `BodyEvent` | body box |
| `AttackEvent` | attack box |
| `SoundEvent` | sound trigger |

## Basics

- `Ctrl+S` save (Save As first when no path); duplicate action/group/sound names are rejected before saving (names are dictionary keys on the engine side).
- Group right-click / double-click = enter the gal editor for that image group (multi-track append, keeps your place).
- Import entries live in each row's right-click menu: gal = image-group row; palette = palette row (a single `.pal` / a `.gal` takes the first frame's palette, both re-importable); sound = sound row.
- Undo `Ctrl+Z` / redo `Ctrl+Y`, the history panel can jump.
- Preview bloom: toolbar "Post-processing" menu (glow data comes from each palette color's "glow" channel).
