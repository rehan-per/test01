# Blend detail screen — overrides & data provenance

> Page-specific notes for the **Blend detail** screen (`renderBlendDetail`).
> Rules here override `../MASTER.md` for this screen only.

## Layout

- Single `.detail-card` (cream, `--radius-lg`, `--shadow-lift`), centred, `max-inline-size: 42rem`.
- Order: media placeholder → title (`Fraunces` stand-in serif) → key/value `<dl>` (brew, allergen, price) → detail blocks (tasting notes, **ingredients**, cup colour) → staff note.
- Detail blocks: real content renders as `.ingredients-body` (plain body text, unitless `line-height: 1.55`, no box); not-yet-supplied content renders as `.pending` (dashed italic box). This visual split — "box = still coming, plain text = real" — must stay consistent as more fields are filled.
- `.ingredients-body` must never get a fixed height: ingredient copy is long and grows in `de`/`fr` (English fallback) and at the `xlarge` text size (MASTER §8 text-reflow).

## Ingredient data — `INGREDIENTS` in the build

**Source:** three `ETS Sip the Season – Ingredients` spreadsheets from Ray, 2026-09-08:
`…- Ingredients.xlsx` (EN + Italian), `…- Ingredients (1) Spanish.xlsx` (EN + Spanish),
`コピーETS… - Ingredients.xlsx` (EN + Japanese). All 22 blends covered in `en`, `es`, `it`, `ja`.

### Decisions baked in (need Ray's confirmation)

1. **One recipe per blend.** The spreadsheets list ingredients *per pack*, and some blends are
   very slightly reformulated between pack formats (a percentage point, or `organic` vs
   `natural` on a 1% flavour). The kiosk shows one list per blend — the recipe that appears
   in the **most packs**. Blends where the source has >1 recipe:

   | Blend | Shown (most common) | Other source variant(s) |
   |-------|--------------------|-------------------------|
   | `winter-apple` | rosehip 21%, 8 ingredients | rosehip 19.5%, 9 ingredients (adds cinnamon + vanilla flavour) |
   | `mrs-claus-berry-hug` | cocoa + raspberry flavour, cinnamon 11% | coconut flavour, cinnamon 12.5% |
   | `frostys-fruity-carnival` | apple 5%, peach 4% (9× — tie broken toward the shorter list) | apple 5.5%, peach 3.5% (9×) |
   | `gingerbread-man` | black tea 53% / cinnamon 31% | black tea 48% / cinnamon 35% (3×) |
   | `santas-cinnamon-sleigh` | black tea 67.5% | black tea 69% (9×) |
   | `reindeers-frosted-mint` | green tea 74.5% | green tea 73.5% (4×) |
   | `nutcrackers-velvety-spice`, `reindeers-candy-cane`, `winter-anise-brew`, `santas-mint-reserve`, `crimson-fruity-punch` | most common | minor flavour-wording / ordering only |

   If any blend needs its pack-specific list, the model would have to move from
   `INGREDIENTS[blendId]` to per-range lists — flag it and we'll do that.

2. **Translations are first-pass.** `es` and `it` are Ray's machine/first-pass translations,
   not native-speaker reviewed — same status as the product-name translations (PRD §7). The
   source had inconsistencies (e.g. lemongrass rendered as *citronela* / *hierba buena* /
   *limoncillo*); one spelling was picked per blend. Faithful transcription, light whitespace
   normalisation only — no translation "corrections". Proof before print.

3. **`ja` format.** Japanese follows JP food-labelling convention: ingredient **names only**,
   no percentages, trailing `／ 香料` (/ flavouring) where flavours are used. Three pure
   spice/tea blends (`ceylon-cinnamon`, `chai-black-tea`, `turmeric-ginger-lemongrass`) have
   no `／ 香料` because they contain no added flavour — matches their EN lists.

4. **Brew times cross-checked.** The Japanese file carries a brew-time column; it agrees with
   every `brew` value already in `BLENDS`. No changes needed.

5. **Allergen consistency.** The 5 blends the kiosk flags `licorice:true`
   (`crimson-fruity-punch`, `santas-cocoa-hug`, `santas-mint-reserve`,
   `turmeric-ginger-lemongrass`, `vanilla-apple-crumble`) are exactly the 5 whose ingredient
   lists contain `organic licorice`. The "Contains licorice" note was stripped from the stored
   ingredient string (the kiosk shows it in the dedicated Allergen row instead).

### Still pending on this screen (unchanged)

Product photography, cup-colour photography, tasting notes, price, organic/vegan certification
badge — all still render the `.pending` "coming soon" state. `de` / `fr` ingredient text not
supplied (English fallback). Italian UI not live (`LANGS` `it` is `ready:false`).
