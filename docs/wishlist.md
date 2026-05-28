# PlusTaal — wishlist

Feature ideas noticed during real use. Not a commitment to build — a place to capture the thought so it isn't lost, and so the 3-month review has something concrete to work from.

Newest at the top.

---

## Resume at the next incomplete level, not Level 1

**Noticed:** 2026-05-20

When you tap a tile that already has some levels completed, the snap view always opens on Level 1. If you've already done Levels 1 and 2, you have to swipe past them every time to reach Level 3, which is the page you actually want.

**Wish:** tapping a tile with N levels complete should open directly on the next level to be done (level N+1). E.g. a tile with Levels 1–2 complete opens on Level 3. A fully mastered tile (4/4) could open on Level 4 (to review) or Level 1 (to start a fresh pass) — decide which on review.

**Notes / things to think about:**
- The snap view currently always resets `scrollLeft = 0` on open. This change would set the initial scroll position to the relevant page instead.
- Mastery state already knows `levelsComplete` per tile, so the "which level is next" information is already available — no new state needed.
- Edge case: a fully mastered tile. Open on Level 4? Open on Level 1 for a fresh run? Or remember that it's mastered and offer a choice?
- Should the levels you've "skipped past" still be swipeable backwards? Almost certainly yes — just change where it *opens*, not what's reachable.

---
