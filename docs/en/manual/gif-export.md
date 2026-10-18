# GIF Export

- Entry: pixel animation editor menu "Export GIF…" → pick a path → settings dialog (scale 1/2/3 + transparency toggle) → export.
- Compositing matches the preview exactly: tracks stacked in list order, hidden frames leave holes, **zero-delay columns skipped**.
- **Transparency** (off by default): controls only the **frame-level transparent color** — on = after compositing, punch the frame transparent color once more (transparent index = frame transparent color); off = the frame transparent color stays as a solid color. Layer transparent keys always apply (compositing semantics, unaffected by the toggle). When tracks have different canvas sizes the letterbox borrows a free slot and stays transparent as before.
- Technical notes: direct 8bpp palette passthrough, disposal=2, infinite loop (NETSCAPE); scaling is nearest-neighbor on the indexed image.
- Headless export: `PixelActorMaker.exe --gifexport <in.gal> <out.gif> [scale] [reference-track gal...]` (CLI is always transparency-mode, unaffected by the GUI default).

> Note: the exported frames must be 8bpp palette frames (24bpp truecolor does not support GIF palette export and is rejected with a clear error).
