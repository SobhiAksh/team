# Rennstern Firmware Edition — Design System

## Product context

A custom firmware-style touchscreen UI for the **Sony α7S II**'s own rear LCD (not a phone app, not a companion app — it lives on the camera's built-in screen, inside the camera body itself, with the hardware controls around it: viewfinder hump, MOVIE mode dial, C1/C2/C3 custom buttons, Fn, joystick, and the physical REC button).

Owner: Sobhi, who runs the film studio/brand **Rennstern**. The brief: "a customized UI/UX for my Alpha7S2 — modern, stylish, simple, for making genuine-looking documentary videos, with premade setups, in a simple accessible format, Rennstern family firmware edition alike" — then clarified twice: (1) it must look like it's built into the real camera body, not a phone shell, and (2) each premade setup should be a deep, "all-in-one filmmaker" configuration (not just exposure basics) — image profile, focus behavior, stabilization, audio, monitoring LUT, and exposure aids all prepared together, as if a smarter in-house firmware team had done the busywork for you.

Key pages / screens (4, tab-switched on one screen):
- **Home** — the active look at a glance (profile, ISO, shutter, WB) plus a quick-switch rail to jump straight to any of the 6 looks, and a one-tap button into Rec.
- **Presets** — a grid of 6 premade "Looks", each opens a detail sheet with a full spec table (profile, ISO, shutter, WB, framerate, focus mode/area, stabilization, audio input/gain, monitoring LUT, zebra/peaking levels) and an "Anwenden" (Apply) button.
- **Rec** — what matters while actually rolling: timecode, L/R audio meters, rule-of-thirds grid toggle, zebra/peaking toggles, remaining card time, the active look's name.
- **Tasten (Controls)** — a reference list mapping the camera's real physical buttons (C1, C2, C3, AEL, Fn, joystick) to what they do in this firmware.

The six premade Looks (keep these names, values and intent — they are real production presets, not placeholders):
1. **Interview** — PP8 S-Log2, ISO 800, 1/50, 24p, WB 3200K manual, AF-C Flexible Spot + Eye-AF, SteadyShot Standard, XLR audio −6dB low-cut 80Hz, monitoring LUT S-Log2→Rec709(800%), Zebra 70 / Peaking red-medium.
2. **Straßendoku (Street doc)** — PP10 HLG, ISO Auto to 3200, 1/50, 24p, WB Auto, AF-C Wide + Eye-AF, SteadyShot Active, internal mic auto-gain, no LUT needed (HLG reads natively), Zebra off / Peaking yellow-low.
3. **Nachtkonzert (Night concert)** — PP8 S-Log2, ISO 12800 (second native base ISO), 1/50, 24p, WB 3200K, AF-C Zone + low-light boost, SteadyShot Active, XLR −3dB limiter on, LUT S-Log2→Rec709(800%), Zebra off / Peaking red-high.
4. **Kino-B-Roll (Cinema B-roll)** — PP9 S-Log3/S-Gamut3.Cine, ISO 3200, 1/50, 24p, WB 5600K, manual focus + peaking-guided, SteadyShot Standard + gimbal, XLR −9dB low-cut 80Hz, LUT S-Log3→S-Gamut3.Cine, Zebra 52 / Peaking white-high.
5. **Zeitlupe (Slow motion)** — PP1 Standard, ISO 1600, 1/250, 120p@1080 (Full HD only), WB Auto, AF-C Flexible Spot, SteadyShot Standard, reference audio only, no LUT, Zebra off / Peaking yellow-medium.
6. **Run & Gun** — PP10 HLG, ISO Auto to 6400, 1/50, 24p, WB Auto, AF-C Wide + Eye-AF, SteadyShot Active, internal mic auto-gain, no LUT needed, Zebra off / Peaking red-low.

Real hardware control map (Tasten screen content):
C1 → Peaking toggle · C2 → Zebra toggle · C3 → Silent Shutter · AEL → hold exposure · Fn → jump to Presets · Joystick → move AF point.

## Brand voice / copy rules

- Write German first, short and neutral, address the reader as **du**. English only for this brief/README, never inside the firmware UI itself.
- HUD/status labels are uppercase monospace, e.g. "RENNSTERN OS", "LOOK / 01", "AKTUELLER LOOK".
- No emoji anywhere. No exclamation marks in UI copy.
- "Echt statt digital" (real instead of digital) is the guiding idea: profiles like HLG/S-Log over flat sharpened "camcorder" defaults — material that looks like film before the edit even starts.

## Visual foundations

**One theme — dark, cinematic, deliberately single-theme (this is a camera screen, not a responsive web app that needs to flip to light mode).**

Color tokens (OKLCH):
- `background`: oklch(0.135 0.020 265) — the blue-black film ground, used for the page and the camera's screen panel.
- `surface` / card fill: oklch(0.175 0.020 265)
- `surface-raised` (hover/active): oklch(0.205 0.019 265)
- `chassis` (camera body metal): oklch(0.085 0.012 265), darker variant oklch(0.065 0.010 265) for shadows/recesses
- `foreground` (text/icons): oklch(0.965 0.006 265)
- `muted` (secondary text, HUD labels): oklch(0.660 0.015 265)
- `line`/border hairlines: oklch(0.270 0.018 265)
- `accent` (Rennstern orange — the ONE accent, spend it sparingly: one highlighted action per view): oklch(0.700 0.190 45)
- `accent-ink` (text/icons placed on the orange fill): oklch(0.135 0.020 265)

Never introduce a second accent hue, a gradient, or any color outside this token set. Chart/meter fills may use a desaturated green (oklch(0.6 0.1 150)) only for audio-level "safe" headroom, blending into orange at the top — that is the one sanctioned exception, for level meters only.

**Type**
- Display/UI face: **Space Grotesk** (Google Fonts), semibold/600-700 for headings, 400-500 for body/UI.
- HUD/mono face: **JetBrains Mono** (Google Fonts), used only for: status labels, look codes ("LOOK / 01"), spec table keys, timecode, button legends — always uppercase, letter-spacing ~0.08–0.16em.
- Numbers that line up in columns (timecode, battery %, spec values) use tabular numerals.

**Spacing & radius**
- 8px base radius. Cards/panels/dialogs: 10–14px radius. Buttons/chips: 6–10px, or fully round for pills and dots.
- Tight, compact information density — this is a camera LCD, not a spacious web page. Favor small type (9–15px) and small gaps (4–10px) over generous whitespace.

**Camera-body chrome**
- The firmware screen sits inset inside a camera body chassis illustration: dark metal gradient body, a small raised EVF hump, a MOVIE mode dial (small disc, orange index dot), a grip shadow on one edge, and real hardware buttons positioned around the screen: a bigger orange physical REC button, C1/C2/C3 chip buttons, an Fn chip below the screen, and a small joystick/d-pad near the grip.
- The screen itself is landscape (close to 3:2, like the real LCD), with a thin status bar (OS label, battery, clock) on top, the active tab's content in the middle, and a 4-item bottom tab bar (Home / Presets / Rec / Tasten) with simple line-icon glyphs, the active tab tinted orange.

**Motion**
- Minimal and functional only: a REC dot that blinks while recording, a preset detail sheet that slides up from the bottom of the screen, audio meters that animate live. No decorative animation. Respect reduced-motion.

## UX principles for this product

- No submenus for the common path — Home shows the active look's key numbers without drilling in.
- Presets are the product: six complete, production-grade setups a documentary filmmaker would actually use, each covering image + focus + stabilization + audio + monitoring + exposure aids together, not just exposure.
- The hardware-button legend (Tasten) exists so the real camera's custom buttons are never a mystery mid-shoot.
- Everything that matters during a take (Rec screen) fits without scrolling: timecode, meters, grid/zebra/peaking toggles, remaining card time.
