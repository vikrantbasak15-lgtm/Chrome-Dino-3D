# 🦖 T-Rex Runner 3D

**Chrome's offline dino game, rebuilt in 3D.**

The classic "No internet" runner with a real perspective camera. Obstacles rush toward you through actual depth, stars drift behind the horizon, and day turns to night as you run. It is a faithful port of Chromium's own game logic, with a Three.js renderer on top. No build step and no server: just open the file.

---

## 🎮 Play

1. Download or clone the repo.
2. Open `index.html` in any modern browser. It works straight from `file://`.

| Key | Action |
| --- | --- |
| `Space` / `↑` | Jump |
| `↓` | Duck |
| `P` | Pause |
| `M` | Mute / unmute |
| `Enter` | Restart |

---

## ✨ Features

- **3D perspective track**: obstacles approach through real depth and are sized by distance.
- **Parallax starfield**: a two-layer drift behind the horizon.
- **Day/night cycle**: driven by distance run. Lights dim and the stars fade in.
- **Procedural dust**: kicked up on every landing.
- **Crash debris**: 90 particles plus camera shake on impact.
- **Speed streaks**: appear above speed ~8.5 and get denser as you go faster.
- **Pause**: `P` freezes both the simulation and the rendering state.
- **Persistent high score**: saved in `localStorage` and shown as `HI` next to your run.
- **Synthesized audio**: jump, crash and milestone tones generated with the Web Audio API.
- **Pixel-accurate sprites**: nearest-neighbour filtering, correct HiDPI handling, no smoothing.

---

## 🧠 How it works

The simulation is a port of Chromium's dino game (`components/neterror/resources/dino_game/`). Collision boxes, obstacle spacing, jump physics and the speed curve are unchanged. Only the camera moved into 3D: screen x maps to world z receding into the distance, and screen y maps to world y. Sprites come from Chromium's original sprite sheets, byte-for-byte.

### Things that look like bugs but aren't

- **Sprites are inverted.** Chrome's sheet is ink on white. Inverting it (what Chrome's own dark mode does) makes the ink light and the white page halo black. `knockOutDarkHalo` then clears that halo to transparent, so sprites sit on the 3D geometry without a card around them. The T-Rex's eye is a genuine white hole in the art and survives, because the flood fill can't reach it without crossing solid ink.
- **The jump peaks at ~91px, not 30.** `minJumpHeight` is a proximity gate that arms the descent clamp, not a ceiling. It is absolute (`groundYPos − 30`), while `maxJumpHeight` stays relative (30). Rebasing both gives 62px, and rebasing neither gives 99px.
- **The track is much shorter than a 2D game would suggest.** A perspective camera shrinks distance hyperbolically. Stretching x=600 out to z=−50 made spawns about 2% of screen height, which is invisible. `zSpan: 15` keeps an obstacle clearly readable from the instant it appears.
- **The camera is deliberately off-axis** (`x=3.4`). Head-on, the runner and the obstacles behind it stack in the same screen column, and the depth reads as overlap.

---

## ✅ Verification

`check3d.mjs` drives the page through headless Chrome. All 16 assertions pass, covering WebGL init, scene construction, sprite texture upload, jumping, the obstacle mesh lifecycle, pause freeze, the night transition, crash and restart.

The jump arc is checked against a literal transcription of `trex.ts updateJump`: 91px over 35 frames, identical to Chromium.

> Verified against the Chromium source at the time of writing. Chrome ships the same game gzip-compressed inside `resources.pak`, so a Chrome update could change numeric values without anything visibly changing here.

---

## 📁 Project structure

```
.
├── index.html      # game, renderer and UI (single file)
├── three.min.js    # Three.js, bundled locally so it works offline
├── check3d.mjs     # headless Chrome test script
└── README.md
```

---

## 🧰 Tech stack

- **Three.js** (bundled `three.min.js`) for 3D rendering
- **Vanilla JavaScript** for the game loop, physics and input
- **Web Audio API** for sound effects
- **localStorage** for the high score

---

## 🌐 Browser support

Any modern browser with WebGL (Chrome, Edge, Firefox, Safari).

---

## 🙏 Credits

- Game design, sprites and logic: the [Chromium project](https://www.chromium.org/)
- 3D rendering: [Three.js](https://threejs.org/)
- 3D port by **Vikrant Basak**

> Not affiliated with Google or the Chromium project.

---

## 📄 License

Add your preferred license here. Note that the sprites and game logic originate from Chromium (BSD-style license), so keep that attribution in mind when choosing one.
