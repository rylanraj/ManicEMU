# Fire Emblem Fates (3DS) performance profile

This build includes a small per-title optimization for Fire Emblem Fates that smooths the heavy 2D→3D battle transitions on iOS (MoltenVK/Metal).

What it does automatically when a Fates title is detected:
- Forces single-eye rendering (disables right-eye, sets 3D factor to 0, render_3d Off) to prevent dual-eye work during transitions.
- Enables async shader compilation and async presentation to avoid frame stalls while new shaders are created.
- Enables disk shader cache and hardware shaders to reduce repeat hitches.
- Disables the “new” vsync path to reduce long latency spikes on some devices.
- Caps the internal resolution factor to 2× if set higher, and enables texture preloading to help with 2D UI assets during transitions.

Detection
- Title IDs (USA):
  - Birthright: 0004000000179800
  - Conquest:   0004000000179900
  - Special:    0004000000179A00
- If Title ID isn’t matched, a fallback heuristic checks the SMDH title for “Fates” or the Japanese “if”.

Notes
- These overrides apply only to Fates. Awakening and other games are unaffected.
- If you need to opt out, we can add a user-default toggle (e.g., `ManicEMU.enableFatesProfile = false`). For now the profile is always on for detected Fates titles.
- You can still manually change resolution in Settings; if it exceeds 2×, the profile will clamp it to 2× for Fates only.

Why this helps
Fates’ transitions use more offscreen framebuffers, blits, and small stateful draws than Awakening. On mobile GPUs via MoltenVK → Metal these patterns are costly. Reducing stereo work and stalling (shader compilation, vsync) addresses the main sources of hitching.

File reference
- Impl: `ManicEmu/ThreeDS/Custom/ThreeDSObjC.mm` inside `insertCartridgeAndBoot`, search for “Fire Emblem Fates performance profile”.
