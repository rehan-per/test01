# Sip The Season — kiosk build notes

Source spec: `STS_Kiosk_PRD.md` (Ray, ETS BIA departure store). This file tracks what's
implemented, what's placeholder, and decisions made in chat that go beyond the PRD text —
read it before picking this back up in a new session.

## Deliverable

`sip-the-season-kiosk.html` — single self-contained file, no build step, no network calls.
Open it directly (`file://`) or serve it statically; either works since everything (data,
styles, icons-as-inline-SVG, snow effect) is inline. Preview locally with e.g.
`python3 -m http.server 8731` from this folder (a `.claude/launch.json` config named
`sts-kiosk-static` is already set up for the in-app browser preview).

## Status vs. the PRD

**Built and working:** full IA (Attract → Category → Range → Blend), all 3 categories / 12
ranges / 22 blends per PRD §5–6 (cross-checked counts and Range→Blend mappings by hand),
Santa's Stockings colour-swatch merge, search (blend name / range name / style number, jumps
straight to a blend or highlights a range card), text-size cycle + high-contrast mode, language
switcher, inactivity auto-reset to Attract, View-Transition-less CSS fade (no `startViewTransition`
wired yet — see Open items), allergen flag ("Contains licorice" only, exactly where PRD §6 flags
it), brew times.

**Brand assets — supplied by Ray 2026-09-08, now live.** `STS Logo.pdf` and
`ETS_illustration_Sheet.pdf` were converted to inline SVG (custom PDF→SVG pass — no renderer on
the machine; scripts in the session scratchpad, not the repo) and recoloured toward the
wine/gold/cream palette. Now in the build via a new `ART` object:
- **Attract wordmark** — the real gold "SIP THE SEASON" mark replaces the CSS-styled text. The
  tagline stays separate localized text (the artwork's baked-in English tagline was dropped so
  ja/de/es/fr still translate).
- **Category illustrations** — `ART.decos` (lantern), `ART.houses` (gingerbread house),
  `ART.crackers` (nutcracker) replace the hand-drawn line icons in the tile/range badges.
Full provenance, the colour-remap, and what was NOT used are in
`design-system/sip-the-season/pages/brand-assets.md`.

**Still placeholder:** `ICONS.cup` / `ICONS.photo` (line art — only used in the still-pending
cup-colour / product-photo slots). Typography still uses system-font stacks standing in for
Fraunces/Inter — PRD §4.3 says don't guess a third typeface; real font files not supplied yet.

**Ingredients — supplied by Ray 2026-09-08, now live.** Full ingredient lists for all 22
blends in `en` / `es` / `it` / `ja`, from three `ETS Sip the Season - Ingredients` spreadsheets.
Stored in `INGREDIENTS` (blend-keyed, sibling of `BLENDS`); the Blend-detail Ingredients block
shows real text when present, `.pending` otherwise. Full provenance + the judgment calls
(one canonical recipe per blend where packs differ slightly; first-pass es/it translations;
JP labelling style) are in `design-system/sip-the-season/pages/blend.md`. `de` / `fr` have no
ingredient text yet → English fallback. Italian is staged in `INGREDIENTS.it` but `LANGS` `it`
is `ready:false` (shows "coming soon") until the rest of the `it` strings land.

**Still left as empty/pending, never fabricated:** tasting notes, price, product photography,
cup-colour photography, organic/vegan certification badge. All render a "coming soon" state.
Do not fill these with invented copy even if asked to "just add something" — PRD §6 and §11 are
explicit that pricing and certification claims need Ray's sign-off, and the tasting-note copy
doesn't exist yet anywhere.

## Language data — added this session, beyond the PRD

The PRD only ships English everywhere plus Japanese *blend names*; category/range names and all
UI chrome were flagged as untranslated gaps (§7, §11). Ray asked in chat to fill in German,
Spanish, French, and the missing Japanese category/range names "from your knowledge." Done for:
category names + blurbs, all 12 range names + format labels, all 22 blend names, the 3 stockings
colour names, the allergen phrase, and the full UI-chrome string table — five languages, all
switchable now (`LANGS[*].ready = true`).

**This is a first-pass machine translation, not a native-speaker or brand review.** Before this
ships to a real kiosk, someone fluent (ideally Ray or ETS's own regional team) should proof at
least the product names — they're the customer-facing brand identity, and translation choices
like keeping "Nutcracker" untranslated vs. localizing "Santa" → "Père Noël" (fr) / keeping "Santa"
(de/es) were judgment calls, not confirmed brand guidance.

Data shape: every name is a `{en, ja, de, es, fr}` object; `localized(obj)` picks
`obj[state.lang] || obj.en`. To add a 6th language: add an entry to `LANGS`, a table to `UI`, and
a key to every name object — the render code doesn't need to change.

Language/text-size/contrast are **session-scoped, not persisted** across a page reload — this is
a shared walk-up kiosk, so the inactivity timeout (and explicit Home) resets them for the next
passenger rather than leaving the kiosk "stuck" on whatever the last person picked. Stepping Back
one screen mid-browse does *not* reset them (see `NAV_DEFAULTS` vs `PREF_DEFAULTS` in the script).

## Design-system pass — applied this session

Reconciled the build against `design-system/sip-the-season/MASTER.md` (generated by the
`ui-ux-pro-max` skill, then hand-corrected — see that file's provenance note). The palette,
radii, shadows, type direction, nav pattern and accessibility model were already aligned (the
MASTER was written from this build). Three real gaps were closed:

1. **`prefers-reduced-motion`** — was unhandled. Added a media block that drops the screen-fade,
   the snowfall and the Attract CTA shimmer and renders the final state; the search-result
   smooth-scroll now falls back to an instant jump. The short (<=180ms) `:active` press-feedback
   transitions are deliberately kept.
2. **Emoji as chrome icons** — search / language / close / contrast / back were `🔍 🌐 ✕ ◐ ‹`
   and the snowflakes were `❄`. All replaced with inline line-SVG (`UI_ICONS`, `SNOWFLAKE_SVG`)
   drawn in the same style as the badge art, stroked with `currentColor` so they track
   cream-on-wine, wine-on-gold (pressed) and white (high-contrast) automatically. MASTER §9/§10
   forbids emoji icons; they also render as colour emoji wherever an emoji font is installed.
   `Aa` (text, not an icon) is unchanged.
3. **`--space-*` scale** — added the MASTER §4 spacing tokens to `:root` for future work. Existing
   hand-tuned spacing values were left as-is, not retrofitted.

Verified in the browser: no console errors; icons legible in both contrast modes; nav flow
(Attract → Category → Range → Blend) intact.

`../.claude/launch.json` (config `sts-kiosk-static`) is now actually present — it was referenced
here before but had not been committed.

### Polish pass — 2026-09-08 (with the brand assets)

- **Staggered card entrance** — `card-in` / `tile-in` keyframes: each tile / range card / blend
  chip fades+rises with a small per-item delay (~40–70ms), so a list assembles rather than
  snapping. `--ease-out` (`cubic-bezier(.16,1,.3,1)`, decelerate) drives it and the screen fade.
  Tiles fade only (a transform keyframe would fight their `translate(-50%,-50%)` centering).
  All of it is disabled under `prefers-reduced-motion` (tile keeps its centering transform).
- **High-contrast fix** — `.tile-blurb`, `.blend-list-header .meta` and `.section-heading p`
  were set to `#000` on their dark backgrounds (invisible). Split the HC muted-text rules:
  `#000` on white cards, `#fff` on the dark ground / black tile. The SVG wordmark gets
  `fill:#ffd257` in HC (replacing the old `-webkit-text-fill-color` hack for the text version).
- Category tile badge bumped 9.4→10.5rem; `.badge svg` now contains-fit (illustrations have
  varied aspect ratios) instead of a forced square.

File is ~216 KB (was ~85 KB) — the brand illustration SVGs account for ~130 KB. Acceptable for a
self-contained offline kiosk that loads once; the gingerbread house is the heaviest (~86 KB /
~480 path elements). If size becomes an issue, that one is the candidate to simplify or swap.

## Layout — also revised this session

Ray flagged the Category screen's icons as needing a circular arrangement and to be bigger, and
separately flagged that every screen had too much unused space below its content. Two different
things were going on:

1. **Category screen** now arranges the 3 category tiles around a circle/triangle instead of a
   grid row, with a much bigger badge specifically for that hero context (`.tile .badge`).
2. **The real bug**: `.screen-scroll{flex:1 1 auto}` never had an actual flex parent for the
   Category/Blend screens — only the Attract screen was ever wrapped in the `.screen` flex
   container, so the scroll area was just sizing to its own content and leaving the rest of the
   (tall, portrait-tablet) viewport as dead space below it, on every screen, regardless of icon
   size. Fixed by wrapping Category/Blend output in `.screen` too (see `render()`), which gives
   `.screen-scroll` real height to work with, combined with `justify-content:safe center` so a
   short list (e.g. a 2-card category) centers in the space instead of pinning to the top, while
   a long one (e.g. a 12-blend list, or any list at the largest text-size setting) safely falls
   back to top-aligned so scrolling still reaches the first item.

## Known data-integrity note carried from the PRD

Santa's Winter Cottage intentionally shares its barcode/style-number pattern-break with Winter
Wonderland Cottage (PRD §8) — Ray said not to fix the source data. It's recorded in the `note`
field on that Range but deliberately **not** shown on the live customer-facing card (an internal
data-hygiene footnote isn't something a passenger needs to see); it's here in case a future
feature (e.g. barcode-based search) needs to know about it.

## Open items (from PRD §11, still unresolved)

- Real product photography (packaging + cup colour) — blocked on Ray.
- Tasting-note copy for all 22 blends — blocked on Ray.
- Pricing per Range/Blend — blocked on Ray.
- A confirmed idle-timeout duration for the Attract auto-reset — currently a provisional
  `INACTIVITY_TIMEOUT_MS = 90000` (90s) constant near the top of the script; easy to change once
  Ray gives a number.
- Fresh typographic direction for the Latin typeface (see PRD §4.3) — don't re-guess a third
  serif blind; ask for reference brands or descriptive words first.
- Organic/vegan certification badge — needs the actual certifying body's logo + Ray's
  confirmation before it's displayed at all (PRD §6).
- `startViewTransition` isn't wired up yet (screens fade via plain CSS `animation` instead) —
  low priority, PRD treats it as a nice-to-have with a CSS fallback already acceptable.
