# FAQ

**Q: Free version vs activated version?**
The free version allows full editing and preview; saving/exporting is blocked (a "free version limit" dialog). Activation keys are **bound to a computer**: the "Help → About" dialog shows your machine code — send it to the developer for an activation key, then paste it in the About dialog to unlock.

**Q: macOS / Linux support?**
Windows 10/11 x64 only.

**Q: Can the produced assets be used commercially?**
Yes. Assets (gal/sff/air/GIF/PNG) are entirely yours with no restrictions; the license only restricts the editor software itself.

**Q: The software won't start / shows garbled text on someone else's computer?**
The release package must be **unzipped as a whole** (the DLLs next to the exe and the `gale_ops` folder are required at runtime); don't copy just the exe.

**Q: How do I recover an older version after saving?**
Saving automatically writes a `.bak` backup (the previous file content).

**Q: UI language?**
Chinese by default; 中文 / English / 한국어 supported. Switch via menu **Help → Preferences → System → Language** and press OK — takes effect after restarting the program (or set `"Language"` in the `config.json` next to the exe).

**Q: Action/group names show as ??? ?**
Names in a file were authored in the creator's own UI language. The editor loads fonts for the current UI language, which may not contain the other language's characters, so names in other languages show `?` — **the data is fine** (the file stores the original text); switch the UI back to the file author's language to display them correctly.

**Q: Can I import MUGEN's .air / .sff files?**
No. The extensions are the same but the formats are not interchangeable — this tool's trio is its own JSON format; MUGEN can't read it, and this tool can't read MUGEN's asset files either.

**Q: How do I read these files in Unity?**
See "Data Format": `.sff/.air/.snd` are plain JSON (with the PAMSDK_Unity plain-data classes); pixel animation uses the `.gal` format.
