# Chrome Dino — 3D

Open `index.html` in a browser. Works from `file://`; no build or server.

**Space / ↑** jump &nbsp;·&nbsp; **↓** duck &nbsp;·&nbsp; **P** pause &nbsp;·&nbsp;
**M** mute &nbsp;·&nbsp; **Enter** restart

## What it is

The simulation is a faithful port of Chromium's own game
(`components/neterror/resources/dino_game/`). Collision boxes, obstacle
spacing, jump physics and the speed curve are unchanged. Only the camera moved
into 3D: screen x maps to world z receding into the distance, screen y to world
y. Sprites come from Chromium's original sheets, byte-for-byte.

## Features

1. **3D perspective track** — obstacles approach through real depth, sized by distance.
2. **Parallax starfield** — two-layer drift behind the horizon.
3. **Day/night cycle** — driven by distance; lights dim and stars fade in.
4. **Procedural dust** — kicked up on every landing.
5. **Crash debris** — 90 particles plus camera shake on impact.
6. **Speed streaks** — appear above speed ~8.5, denser as it climbs.
7. **Pause** — `P`, freezes simulation and rendering state.
8. **Persistent high score** — `localStorage`; shows as `HI` beside your run.
9. **Synthesized audio** — jump / crash / milestone tones via WebAudio, `M` to mute.
10. **Pixel-accurate sprites** — nearest-neighbour, correct HiDPI table, no smoothing.

## Things that look like bugs but aren't

- **Sprites are inverted.** Chrome's sheet is ink on white. Inverting it — what
  Chrome's own dark mode does with a canvas filter — turns the ink light and the
  white page halo black. `knockOutDarkHalo` then clears that halo to transparent,
  so sprites sit on 3D geometry without a card around them. The T-Rex's eye is a
  genuine white hole in the art and survives, because the flood fill can't reach
  it without crossing solid ink.
- **The jump peaks at ~91px, not 30.** `minJumpHeight` is a proximity gate that
  arms the descent clamp, not a ceiling. `minJumpHeight` is absolute
  (`groundYPos − 30`) while `maxJumpHeight` stays relative (30). Rebasing both
  gives 62px; rebasing neither gives 99px.
- **The track is much shorter than a 2D game would suggest.** A perspective
  camera shrinks distance hyperbolically. Stretching x=600 out to z=−50 made
  spawns about 2% of screen height — invisible. `zSpan: 15` keeps an obstacle
  clearly readable from the instant it appears.
- **The camera is deliberately off-axis** (`x=3.4`). Head-on, the runner and the
  obstacles behind it stack in the same screen column and the depth reads as
  overlap.

## Verification

`check3d.mjs` drives the page through headless Chrome: 16/16 assertions pass,
covering WebGL init, scene construction, sprite texture upload, jumping,
obstacle mesh lifecycle, pause freeze, night transition, crash and restart.

The jump arc is checked against a literal transcription of `trex.ts updateJump`
— 91px over 35 frames, identical to Chromium.

Verified against Chromium source at the time of writing. Chrome ships the same
game gzip-compressed inside `resources.pak`, so a Chrome update could change
numeric values without anything visible changing here.
