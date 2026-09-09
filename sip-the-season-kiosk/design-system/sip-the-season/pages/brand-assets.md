# Brand assets — logo & illustrations

> How the real STS logo and ETS illustration art got into the kiosk, and the
> decisions baked in. Overrides `../MASTER.md` §3 (typography) and §6 (style)
> only where noted.

> **⚠️ SUPERSEDED 2026-09-09.** The PDF-content-stream trace described below
> rendered as broken, lumpy letterforms (wordmark) and heavy sub-path sludge
> (illustrations) at kiosk size — Ray: "the graphics like the logo look
> terrible". It was **removed**. The wordmark is now real type set as a gilded
> SIP / THE / SEASON lockup (`renderWordmark()` + `WM_SPRIG`); the three category
> illustrations are **bespoke SVGs** hand-drawn to one system (see BUILD_NOTES
> "Premium visual overhaul" and MASTER §9). The rest of this file is kept as the
> record of what the PDFs contained and what was tried — the original AI/PDF
> files are still the source if ETS ever provides clean SVG/PNG exports to drop
> in. The colour-remap notes still apply if those raw exports are used.

## Source

Two Adobe Illustrator PDF exports from Ray, 2026-09-08:

| File | What it holds |
|------|---------------|
| `STS Logo.pdf` | "SIP THE SEASON" wordmark — a gold version and a grey version, each with the "FESTIVE INFUSIONS · GLORIOUSLY ORGANIC" tagline under it |
| `ETS_illustration_Sheet.pdf` | 6-page festive illustration library on placeholder-grid layouts: stockings, fireplace, wreath, baubles, candles, gingerbread house + man, nutcrackers (green & red uniform + full colour), teapot, lantern, rocking horse, stars, snowman, Santa/Mrs Claus silhouettes, decorative borders, the STS logo lockup again |

Both are **pure vector** (no embedded rasters).

## Extraction

The machine had **no PDF renderer** (`poppler` / `pdftocairo` / `mupdf` all
failed to install — network), so conversion was done with a **custom
PDF-content-stream → SVG pass** (`pdf2svg.py` / `crop.py`, in the session
scratchpad, not committed — regenerate from the PDFs if needed). It walks the
page operators (`m l c v y re`, `q Q cm`, `f/f*/S`, `rg/k/g` colour) and emits
`<path>` elements; text, clipping, shadings and images are ignored (fine for
flat art).

Caveats of that pass:
- **CMYK → RGB is naïve** (`(1−C)(1−K)` etc.) — Illustrator's real profile
  conversion isn't available, so saturated CMYK greens/pinks came out too
  bright. Fixed by a **colour remap** (below), not by better conversion.
- **No clipping** — a path that Illustrator clipped can overflow. Checked
  visually for the 4 assets used; none do.

## What's used (`ART` object in the build)

| Key | Source | viewBox | ~size | Notes |
|-----|--------|---------|-------|-------|
| `ART.wordmark` | `STS Logo.pdf`, gold copy, tagline cropped off | 202×108 | 8.5 KB / 1 path | On Attract, replacing the CSS text `.wordmark`. Gold is the artwork's own `#cc9900`. |
| `ART.decos` | illustration sheet p2, lantern cell | 113×257 | 0.7 KB / 4 paths | Recoloured green→gold, dark-green→wine. |
| `ART.houses` | illustration sheet p4, gingerbread-house cell | 182×298 | 86 KB / ~480 paths | Acid green→gold/green fixed; browns/cream kept. Heaviest asset. |
| `ART.crackers` | illustration sheet p6, full-colour nutcracker cell | 113×283 | 37 KB / ~130 paths | Acid greens→brand green, hot pink→`--pink-300`, pure red→`#a3213c`. |

**Colour remap applied** (source hex → brand):
`#e3ff3d #33f705 #4df73b #1da800 …` (acid greens) → `#2e9d40` / `#27813a` / `#e0b04a`;
`#ff80ef #ff80f0 …` (hot pink) → `#e6afcb`;
`#fc0000 #ff0000` (pure red) → `#a3213c`;
`#fcde19 #f2cd46` (raw yellow) → `#e0b04a` / `#cb951a`.

## What's NOT used (available in the PDFs if wanted later)

Tagline lockup, grey wordmark, teapot, wreath, individual stockings/candles,
baubles, rocking horse, hanging stars, snowman, Santa/Mrs-Claus silhouettes,
the green- and red-uniform nutcrackers from p3, decorative borders, botanical
motifs. The "English Tea Shop" oval on p2 is the **parent brand** — not STS.

## Decisions / open questions for Ray

1. **Category ↔ illustration mapping** is my call:
   Decos → lantern, Houses → gingerbread house, Crackers → nutcracker.
   "Decos" is "Stockings & lanterns" — a lantern covers half of it; there was
   no clean isolated *stocking* on the sheet (the stocking art is a
   staircase scene). Swap to something else if you'd rather.
2. **Colours are remapped, not exact** — the extraction can't read the real
   CMYK profile. If precise brand colour matters, hand off the SVGs (or the
   original AI files) to whoever owns the palette for a proper match, or
   re-export the art as SVG/PNG directly from Illustrator and drop it in.
3. **Wordmark tagline** — the kiosk shows the localized `.tagline` text under
   the mark, not the artwork's baked-in English one. If the locked logo must
   appear exactly as drawn (tagline included, English only), say so.
4. Still no **Fraunces / Inter** font files — headings/body remain system-stack
   stand-ins (MASTER §3).
