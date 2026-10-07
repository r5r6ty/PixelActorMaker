# Pixel Animation Editor (gal)

> TODO: screenshots. This page covers structure and behavior first; layout will be rearranged around the screenshots later.

## Multi-track timeline

- Each **track** = an independently editable `.gal` document (no master/sub relationship — all tracks are equal).
- Tracks can be dragged horizontally to different positions on the timeline (the Offset column; may be negative); frame f sits in column f+Offset.
- Playback preview / GIF export = compositing the tracks' frames **per column** (list order = stacking order, bottom layer drawn first).
- `A`/`D` = step columns (current track first, top to bottom, first track with a frame, wrapping); `W`/`S` = switch layer; `Space` = play.
- A hidden frame leaves a hole in its column (nothing drawn, no interaction, column position unchanged, per track); zero-delay frames are skipped without consuming time.

## Frames and layers

- Frames: Shift for range multi-select; double-click = select all adjacent **same-name** frames on this track; the frame properties dialog (name/delay/transparent color) edits a buffer only, Apply = one undo step.
- Delay shown in three scales: 1/60 s (default) / 1/100 s / 1/1000 s; storage is always milliseconds.
- Layers: multi-select; eye/lock icons at the top-left of each cell; locked layers are ignored by painting tools.
- Special layers (`del`/`Collider`/`Body`/`Attack`/`Center`) and special frames (name contains `del`): **red name badge**; special layers are not locked by default — attack/hit boxes can be drawn as special layers and exported; baking filters special layers out.

## Tools

| Tool | Behavior |
|---|---|
| Pencil | dot/cross brush; **Shift+drag = draw a straight line** (line starts on press, lands on release = one undo step; toggling Shift mid-drag switches axis locking live) |
| Fill | flood fill of a connected region |
| Color pick | connected same-color region on the current layer = selection; Shift/Alt+click = flood add/subtract |

- No eraser: erasing = pencil with the transparent index (or the transparent-drawing toggle).
- Selection: drag a box → 8 resize handles → floating move layer (doesn't land until released); hold Shift while dragging = horizontal/vertical constraint; composite selections: Shift adds / Alt subtracts; rotation handle (draggable pivot, Shift = 15° snapping).
- Clipboard interop: copy / paste goes through the system clipboard — selection pixels can be pasted into Paint, chat windows, etc., and images from outside (screenshots) can be pasted in; on 8bpp frames pasted colors are normalized to the nearest palette color.

## Palette

- Double-click a swatch to edit; **dragging** a swatch: no modifier = swap two colors, Ctrl = overwrite, Shift = swap and remap pixel indices along (whole document).
- With `SyncPal=1` (synced palette) the first frame's palette is used for both display and editing.
- Right-click color pick (canvas) with Shift = pick the **composited display color** (on 8bpp, exact index match against the current palette).
- Color depth: a new `.gal` defaults to **8bpp (indexed)**; the menu "All frames (current track name) → Color depth" switches 8bpp ↔ 24bpp (truecolor) document-wide — every frame × every layer converted in one undo step.
- On 24bpp (non-indexed) frames the palette panel shows the bundled `default.pal` reference palette (read-only color picking; the status line shows the current #RRGGBB).

## Undo and saving

- In-editor undo/redo (the history panel can jump); history does not survive "return ↔ enter".
- Save = block-splice write-back (unchanged layers preserved byte-for-byte) + a `.bak` backup; "Save as" = save all tracks (track 0 uses the chosen path, the rest get a numeric suffix).
