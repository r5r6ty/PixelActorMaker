# GIF Export

- Entry: pixel animation editor menu "Export GIF…" → pick a path → settings dialog (scale 1/2/3) → export.
- Compositing matches the preview exactly: tracks stacked in list order, hidden frames leave holes, **zero-delay columns skipped**.
- Technical notes: direct 8bpp palette passthrough, transparent index = frame transparent color, disposal=2, infinite loop (NETSCAPE); scaling is nearest-neighbor on the indexed image.
- Headless export: `PixelActorMaker.exe --gifexport <in.gal> <out.gif> [scale] [reference-track gal...]`.

> Note: the exported frames must be 8bpp palette frames (24bpp truecolor does not support GIF palette export and is rejected with a clear error).
