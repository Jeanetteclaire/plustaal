# Build log

Significant milestones for `plustaal`. Not every commit — just the moments where a real piece of work landed and is worth remembering.

Newest at the top.

---

## 2026-05-20 — Recognition question now content-driven

The Level 1 question label ("Is this use of er correct?") was the last piece of er-specific text hardcoded in the UI. Now each tile declares its own via an optional `recognitionQuestion` field in its JSON, with a generic "Is this correct?" fallback if absent. The passief tile can ask "Is this passive construction correct?", the inversie tile "Is the word order correct?", and so on.

Decisions:

- **Optional field with fallback, not required.** A tile that omits `recognitionQuestion` still works — falls back to generic text. No tile can break for lacking it.
- **Caught before bulk content authoring.** Adding the field to the schema now (rather than after 59 tiles exist) means every generated tile includes it from the start. Retrofitting 59 files would have been painful. Schema decisions are cheapest before the content exists.
- Content brief updated to document the field and show it in the er-1 reference example.

State: the UI is now fully topic-agnostic. Every piece of topic-specific text — prompts, explanations, notes, and now the recognition question — flows from content. The app no longer assumes it is about *er*.

---

## 2026-05-20 — Step 8: Tile-mapping architecture

The big structural change: from "one hardcoded active tile" to "every grid position maps to its own content file, loaded on demand." Tapping any tile now attempts to load `content/<tileId>.json`; if the file exists, the tile opens; if not, a "coming soon" toast appears. Adding content to the app is now a single action — drop a correctly-named JSON file into `content/`. No code editing required to light up a tile.

Decisions:

- **`TILE_MAP` array maps the 60 positions to tile IDs**, grouped by topic so the wall reads as regions (all 5 *er* tiles adjacent, all imperfectum adjacent, etc.). Pre-filled with all 60 planned tile IDs from the content brief. To rearrange the wall, reorder the array; to leave a gap, use null.
- **Content loads on tap, not at startup.** Previously `loadContent()` ran once at boot and fetched `er.json`. Now `loadContent(tileId)` is called when a tile is tapped, fetches that tile's file, and returns true/false. A failed fetch (file not yet authored) returns false → "coming soon" toast. No wasteful pre-fetching of 60 files.
- **One file per tile**, named `<tileId>.json` (e.g. `er-1-plaatsbijwoord.json`). Existing `er.json` renamed to match. Matches what the content brief instructs the authoring instance to produce — one tile per file, addable incrementally.
- **Wall is now a true mastery map (the honest version of the original vision).** Previously the 59 inactive tiles showed a decorative position-gradient (small top-left → large bottom-right) that was never tied to anything. Now every tile sizes by its actual mastery: untouched = 78% (largest), mastered = 17% (smallest). The wall starts bold and near-uniform, and develops texture purely from practice. Trade-off accepted: less pretty on day one, but every tile's shrink-on-mastery is now visible and meaningful, and the texture the user eventually sees is entirely theirs. Considered keeping the decorative gradient (Option Y) but rejected — it made mastery unreadable from size and muted the reward of mastering top-left tiles.
- **Mastery state survives the change** because it was always keyed by tileId, not by position. The *er* tile moved from position 0 to position 19 (into the er region) without losing progress.

State: PlusTaal is now content-scalable. The architecture supports all 60 tiles; only the content files remain to be authored. Adding a tile = adding a file. This is the structural foundation for the 3-month content build.

---

## 2026-05-20 — Step 7: Work queue

A place to flag explanations for later research without breaking practice flow. A "+ save to queue" button sits below the feedback on every level. Tapping it saves that question's full context (prompt, model answer, explanation/note, tile, level, timestamp) to a queue. A "queue" link top-right of the wall (with a live count) opens a dedicated queue view: a chronological list, newest first, where each item can be expanded to add the user's own research notes, ticked as researched (greys out but stays as an audit trail), or deleted.

Decisions:

- **Save the whole block, not a text selection.** Selecting specific text is fiddly on a phone and breaks flow. Saving the entire explanation block is one tap; the user adds their own notes later when researching.
- **Separate localStorage key (`plustaal:queue`)** from mastery state (`plustaal:state`). Different lifecycles — mastery decays, queue items persist until manually cleared. The reset-progress button wipes mastery but leaves the queue intact.
- **Researched items grey out rather than delete.** The trail of what's been investigated is itself useful. Explicit delete (with confirmation) for genuine removal.
- **Research notes auto-save on input.** Type in an item's textarea, navigate away, notes persist. No save button needed.
- **No deduplication.** Saving the same question twice across sessions creates two entries — intentional, since the user may want to re-flag something as understanding shifts. Duplicates can be deleted manually.
- **Per-level save button resets per question.** Each new question gets a fresh "+ save to queue" button; tapping shows "saved ✓" for the remainder of that question.

State: the research-capture loop is complete. The app now serves the Thinking Gym principle directly — flag the thing, keep practising, investigate properly later. The tool supports the user's own thinking rather than substituting for it.

---

## 2026-05-20 — Step 6: PWA wrapper + reset progress button

PlusTaal now installs as a proper home-screen app. Tapping the cream-plus icon launches the app full-screen with no Safari chrome, matching the Cue Card pattern. A small "reset progress" link at the bottom of the wall view lets the user wipe all mastery state with a double-tap confirmation pattern.

Decisions:

- **Icon design: pure plus sign, cream on warm-black.** 60% of the canvas, 30% thick arms — same proportions as the tile pluses in the app itself. The home-screen icon IS a tile of the wall. No wordmark, no decoration. iOS rounds the corners automatically.
- **Two icon sizes (192px and 512px) generated with Pillow** rather than commissioned. Single colour fill + two rectangles is trivial code, doesn't need a designer, looks identical to a hand-drawn version at this aesthetic. The 512px also serves as the maskable purpose for Android.
- **Reset button is small, low-opacity (20%), at the bottom of the wall.** Discoverable on inspection but invisible during normal use. Double-tap pattern instead of `confirm()` dialog — feels native to the app rather than a browser interruption.
- **Confirmation text brightens to terracotta** (`#e0a59c`) for the 3-second confirmation window. Re-uses the same red the app uses for incorrect answers, so the warning colour is already familiar.
- **`.gitignore` added** to keep `.DS_Store` and editor metadata out of the repo. Also `files.zip` and `files/` patterns so accidental drag-drop unzips don't pollute future commits.
- **Content brief committed to repo** as `plustaal_content_brief.md`. Self-contained authoring prompt — pasteable into any fresh Claude conversation to generate one tile of content at a time. Includes schema, full er-1 example, all 13 topics with sub-tile IDs, writing guidelines, user profile with recurring errors.

State: app is fully installable and resettable. Pre-3-months phase complete except for the work queue feature (next session). After that, content authoring across 59 remaining tiles is the bulk of the work, parallel to actually using the app.

---

## 2026-05-20 — Step 5b: Level 2 rewrite as transformation + bug fixes

The original Level 2 cloze format had a structural flaw: when every question is about *er*, the answer is always "er", and the user can win without thinking. Rewrote Level 2 as **transformation** — given a Dutch sentence, rewrite it applying the rule. The trivial-answer trap is broken because the transformations require word-order changes, choosing between `er` / `daar` / `er…naartoe`, and recognising verb subcategories (directional vs locative).

The same pattern works for every grammar topic in the app: present-tense → imperfectum, active → passive, simple → bijzin, etc. Locked in as the Level 2 design across the whole app.

Decisions:

- **Transformation over multi-choice or "fix the wrong word".** Both alternatives considered. Transformation forces real production and word-order thinking, which is the actual B2 muscle. Multi-choice is too easy. "Fix the wrong word" tests proofreading rather than construction.
- **Multiple accepted answers per question** (2-4 typically). Pronoun variants (`we`/`wij`, `ze`/`zij`), word-order alternatives, `er` vs `daar` where both work.
- **Lenient auto-check** (trim, lowercase, strip punctuation) is sufficient because transformations are bounded — the set of valid outputs is enumerable.

Bug fixes batched in same step:

- Stray `"` character before the Level 2 textarea — the "''" mystery in earlier screenshots was a literal typo rendering as floating text.
- Level 2 focus listener was nested inside the keydown listener, meaning the scroll-into-view never fired on first focus and accumulated extra listeners on each keypress. Unnested.
- iOS keyboard was pushing prompt content off the top of the screen because the snap pages used `justify-content: center` — content centred in the full viewport, but only half was visible above the keyboard. Changed to `justify-content: flex-start` so content anchors to the top of each page. iOS keyboard appearance no longer displaces the prompt.

Workflow decision: **targeted diffs over full-file rewrites** for small changes (1-30 lines). Each turn names exactly what changes and where. Substantial changes (new features, big restructures) still go full-file but the user pastes their current state first so I'm not working from a stale model.

State: Level 2 now genuinely tests grammar rather than allowing trivial bypass. Keyboard behaviour stable across Levels 2-4 on iOS. All four levels working end-to-end on phone.

---

## 2026-05-19 — Step 5: All four levels populated for er-1

Levels 2, 3, and 4 of `er-1-plaatsbijwoord` now have content and working UI. Tapping the active tile opens a four-page swipe sequence with real exercises at every level. Completing all four marks the tile as mastered (plus shrinks to 17%, smallest of the five mastery sizes).

Level designs:

- **Level 2 — Cloze (later rewritten as Transformation in Step 5b).** 7 fill-in-the-blanks with `acceptedAnswers` arrays for lenient checking.
- **Level 3 — Simple translation.** 5 English sentences requiring one targeted *er* construction. Self-assessment flow: user types translation, sees model answer + alternatives + teaching note, taps Goed (correct) or Niet helemaal (not quite). Trust-based — the user's developing judgement is the real test, not exact string matching.
- **Level 4 — Complex translation.** 5 English sentences requiring multiple constructions: plusquamperfectum + *er* + word order; dubbele infinitief in plusquamperfectum; nested bijzinnen with `is veranderd`; etc. Real B2 muscle.

Decisions:

- **Self-assessment for translation levels, not auto-check.** Translation has too much valid variation for string matching to work — multiple right answers, near-rights that are wrong, alternate word orders. The user's own judgement is more accurate than the app's. Honesty risk acknowledged and accepted — the practice itself is the value, not the score.
- **Alternative answers shown inline.** Below the model answer in italics: "Also acceptable: …". The user sees that translation has multiple right answers in real time, which itself teaches a meta-skill (Dutch flexibility).
- **Note field per question.** Brief teaching paragraph alongside the model answer explaining the structural rules at play. Especially valuable on Level 4 where multiple constructions interact.
- **Question counts: 7/7/5/5 across the four levels.** Recognition and Cloze are similar effort. Translations get fewer because each takes longer mentally.

State: full tile complete. The app is now a real, working Dutch grammar practice loop for one topic.

---

## 2026-05-19 — Step 4: State persistence — the wall remembers

The wall is no longer static. Mastery state for each tile saves to localStorage under `plustaal:state`. Completing a level on the active tile shrinks the tile's plus to the next size (78% → 62% → 43% → 33% → 17%). On every app load, weekly decay is applied: tiles not practiced for 7+ days drop one mastery level per overdue week.

For Step 4, only one tile on the wall is "active" — the top-left position (`er-1-plaatsbijwoord`). The other 59 show a "Coming soon — content in progress" toast when tapped. This avoids fake-mapping 60 tiles to the same content.

Decisions:

- **One active tile, the rest are honest about being incomplete.** Two options considered. Option A: all 60 tiles open the same content (all mastery effects apply to that one). Option B: only the active tile opens content, others show "coming soon" toast. Option B chosen — more honest, mastery is tracked per-tile correctly, and the user can visually see their one real tile shrinking as proof the model works.
- **Weekly decay over daily or logarithmic.** Daily was too aggressive — losing progress for missing a single day would feel punishing. Logarithmic was the "real" memory model but complex to reason about and build. Weekly is simple, has consequence, and survives normal life.
- **Tile sizes animate smoothly** on the wall when state changes. CSS `transition: width 0.5s ease, height 0.5s ease` on `.tile-plus`. After completing a level and tapping back to the wall, the tile visibly shrinks — subtle reward, reinforces the "art produced by practice" idea.
- **State shape:** `{ tileId: { levelsComplete: 0-4, lastPracticed: ISODate } }`. Absent tiles = untouched. Tracked tiles have a number and a date.
- **Background-progression-by-level stripped.** Original design had each level page slightly different shade. Reading as decoration that didn't earn its place — the content already signals difficulty. All four level pages now use the same `#1a1814` as the wall.

State: PlusTaal is no longer "Cue Card for Dutch." The wall is a living artefact of practice — it remembers, it decays, it rewards return visits with visible change. This is the move that made PlusTaal into what it was supposed to be.

---

## 2026-05-19 — Step 3: Level 1 exercise — Recognition for Er

First real working exercise. Content for one tile (`er-1-plaatsbijwoord`, Er als plaatsbijwoord) lives in `content/er.json`. Level 1 Recognition flow: 7 prompts of *er* used correctly or incorrectly in context; user taps Correct or Niet correct; tile colours green or red; explanation appears below; tap Next to advance; completion state at the end with score.

For Step 3, every tile on the wall opens the same exercise content. Tile-to-content mapping comes in a later step.

Decisions:

- **JSON for content, not markdown or a database.** Content is structured exercise data (every tile/level/exercise has the same shape), single-user, ships static. JSON parses with one line of code and the structure self-documents. Markdown was right for STRATA (prose); JSON is right for PlusTaal (structured data).
- **One file per zone** (`content/er.json`, future `content/imperfectum.json`, etc.). Lets each zone be authored and shipped incrementally.
- **`fetch()` to load content** at app startup. Requires HTTP serving — won't work over `file://`. Local development needs `python3 -m http.server 8000` or testing on the deployed Pages URL.
- **Feedback uses warm green/red** matching the rest of the palette. Soft sage `#b8dab8` for correct, soft terracotta `#e0a59c` for incorrect — not jarring primaries.
- **Reset on snap view open.** Every time a tile is tapped, Level 1 starts at Question 1 with score zero. No mid-session persistence yet.
- **Recognition format chosen for *Er*** specifically because the recurring error patterns in your Dutch are about using *er* (or *daar*, or *naartoe*) in the wrong context. Spotting wrongness in real sentences builds the corrective instinct better than producing the right form would at this stage.

State: full Level 1 loop working end-to-end on phone, deployed. Levels 2-4 still placeholder pages. Mastery state not yet persisted — tile sizes on the wall don't change when a level is completed (Step 5+).

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
