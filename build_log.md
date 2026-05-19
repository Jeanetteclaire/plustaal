# Build log

Significant milestones for `plustaal`. Not every commit — just the moments where a real piece of work landed and is worth remembering.

Newest at the top.

---

## 2026-05-19 — Step 2: Snap view navigation

Tapping any tile opens a fullscreen four-page snap view: Level 1 Recognition, Level 2 Cloze, Level 3 Simple translation, Level 4 Complex translation. Horizontal scroll-snap (one page per swipe). Back button top-left returns to the wall. Always resets to Level 1 on each open. Aesthetic continuity carried through: same warm-neutral palette, same cream Manrope, plus signs reappearing as low-opacity outlined SVG decorations scattered across each page background.

Decisions:

- **Four pages, horizontal scroll-snap with `scroll-snap-stop: always`.** Each swipe locks to one level; can't stop between pages. Mandatory snap behaviour.
- **Backgrounds get progressively lighter L1→L4** (`#1a1814` → `#252119` → `#302a1f` → `#3b3326`). Subtle but readable signal of difficulty progression.
- **Outline plus signs as ambient decoration.** Same plus geometry as the wall (30%-thick arms) but rendered as SVG paths with stroke and no fill. 18 per page at random positions and sizes (40-180px), opacity 7%. Each page generates its own scatter — no two pages identical.
- **Back button is a fixed-position ← arrow** top-left, 44×44 tap target, opacity 0.6 at rest brightening on press. Follows safe-area-inset for notched phones.
- **Reset to Level 1 on every open.** Each journey into a tile starts at Recognition rather than wherever the user last left off. Predictable.
- **Page title weight matches wall title weight (700).** Same Manrope Bold across both. Visual voice consistent.

State: navigation skeleton complete. Tapping any tile opens the same placeholder snap view — content varies by level (label and title only) but not yet by tile. Real exercise content lands in Step 3+.

---

## 2026-05-19 — Step 1: Visual foundation

Halftone wall of 60 plus-sign tiles, 6×10 grid, with "PlusTaal" title in Manrope Bold (700) above. Five discrete mastery sizes determine each plus's scale within its cell. Warm-neutral palette throughout: near-black background `#1a1814`, deeper black wall `#14110d`, cream plus signs `#f0e9dd`. Each tile is a button with a scale-down press animation.

Decisions:

- **60 tiles, 6 cols × 10 rows.** Each grammar topic gets ~5 tiles. Phone-first sizing (~360px wall width). Larger tile counts (200+) considered for true halftone density but rejected due to content authoring cost.
- **Five mastery sizes:** 78%, 62%, 43%, 33%, 17% (untouched → mastered). Plus ±3% per-tile random variation so the wall doesn't read as mechanically stepped.
- **Halftone aesthetic.** Each plus is the same colour and shape; only the size varies. Larger plus = more "ink" = darker tone. Stand back from the screen and the wall has a real tonal gradient; up close, just plus signs of different sizes. The art works *with* CSS rather than against it.
- **Manrope title.** Bold (weight 700) at 38px, cream, centred at top, tight margin to the wall (0.2rem). Title and plus signs share the same cream so they read as one composition.
- **Plus geometry.** Two crossed rectangles, each 30% thick. Centred via `(100 - thickness) / 2` left/top values. Pure CSS, no SVG.
- **Mastery model.** Each tile has 4 levels (recognition → cloze → simple translation → complex translation). Completing a level shrinks the plus to the next discrete size. Tiles also decay over time: not practiced for a while → plus grows back, art stays fluid.

State: visual layer working in Chrome. Safari has a known caching issue with Google Fonts that masks heavier weights during development — confirmed not a code problem.

---

## 2026-05-18 — Pre-build: Design exploration

Before this build began, a design exploration ran through several aesthetic directions. The first instinct — a wall of wooden mosaic tiles with grain texture, elevation variation, and directional lighting — produced four iterative previews (`wall_preview.html` through `wall_preview_v3.html`) that progressively added 3D height variation, SVG-based wood grain, directional shadows, and pseudo-element side faces.

The wooden direction was eventually abandoned. The visual ceiling of CSS for simulating organic material (wood grain, real depth, light catching on physical surfaces) is genuinely limited; the closer the previews tried to get to the reference image, the more they read as "approximation of wood" rather than wood. The right tools for that aesthetic would have been photography, pre-rendered 3D, or runtime WebGL — none a good fit for a quick-to-build personal PWA.

The pivot to halftone plus-signs was the productive redirect. CSS handles geometric pattern excellently — uniform shapes, varied scale, sharp edges, no faux texture required. The aesthetic still serves the same underlying idea (the wall as art produced by practice) while being achievable in the medium.

Worth keeping the wood-tile previews in the repo as design archaeology. They document the exploration and the pivot.

Build approach decided: same methodology as Cue Card. Small steps, each landing one piece. Build log entries for significant milestones, README kept current, terminal-block commands for delivery. No fragility scans, no heavy documentation.
