# PlusTaal

B2 Dutch through halftone.

A spaced-repetition learning tool for advanced Dutch grammar, presented as a halftone-pattern wall of plus-sign tiles. Each tile is a grammar topic. Mastery shrinks the plus sign over time; neglect lets it grow back. The wall is the art produced by practice.

## How it works

Sixty tiles arranged 6×10 fill the screen. Each tile represents a vocabulary or grammar theme within one of thirteen grammar topics — imperfectum, plusquamperfectum, passief, *er* usage, inversie, dubbele infinitief, *om...te*, and so on. Tapping a tile opens a snap-page sequence of four exercises: recognition, cloze, simple translation, and complex translation. Completing levels shrinks the tile's plus sign through five discrete sizes. Mastered tiles read as small marks; untouched tiles read as full plus signs.

## Visual identity

- Warm-near-black background `#1a1814`
- Wall background `#14110d` (slightly deeper, gives subtle layering)
- Plus signs in cream `#f0e9dd`
- Manrope ExtraBold for the title, weight 800
- Plus signs as discrete buttons, geometric, no faux 3D or texture
- Halftone gradient at rest: tile size varies with mastery, creating a tonal gradient across the wall

The aesthetic deliberately works *with* CSS rather than against it. An earlier design direction attempted wooden mosaic tiles with grain texture and elevation — that direction proved a poor match for the medium and was abandoned. The halftone redirection is documented in the build log.

## Stack

- Single `index.html`, vanilla HTML/CSS/JS
- Manrope from Google Fonts (eventually self-hosted for production)
- Deployed via GitHub Pages
- PWA-installable (manifest added in a later step)
- No build tools, no dependencies

## Local development

Open `index.html` in any modern browser. For development, use Chrome — Safari aggressively caches Google Fonts which can mask weight changes during design iteration.

## Deploy

GitHub Pages: enable in repo settings, point to `main` branch. Pushed changes deploy automatically.
