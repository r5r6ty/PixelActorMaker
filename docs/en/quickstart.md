# Quick Start

1. Unzip the release package and double-click `PixelActorMaker.exe` (Windows 10/11 x64, no installation, **keep all files together**: the DLLs next to the exe and the `gale_ops` folder are required at runtime).
2. UI language: Chinese by default; 中文 / English / 한국어 supported — menu **Help → Preferences → System → Language**, pick one and press OK, **takes effect after restarting the program** (or set `"Language"` in the `config.json` next to the exe).
3. Personalization is remembered automatically: window position/size, the gal editor's tool / onion skin / preview zoom (`config.json` next to the exe), floating-window layout (`imgui.ini`) — all saved on exit, no manual management needed.
4. Two production routes:
   - **Pixel animation**: menu "New" or "Open" a `.gal` → draw frame by frame on the multi-track timeline → "Export GIF…" to check the result.
   - **Character trio**: open a `.sff` (with `.air`/`.snd` in the same folder) → edit actions and frame events (attack boxes / hit boxes / sounds) → Ctrl+S to save.

## Free version and activation

- The free version has **full editing** and preview; a "free version limit" prompt appears on save / export.
- Activation: menu **Help → About** — send the **machine code** shown on that page to the developer, paste the received activation key into the input box on the same page and press "Activate" to unlock (the key is bound to your computer; you can also save the key as a one-line `license.key` next to the exe and restart).

## Using the data in a game engine

See "Data Format": `.sff`/`.air`/`.snd` are JSON text (with the PAMSDK_Unity plain-data classes, drop them straight into a Unity project); pixel animation uses the `.gal` format.
