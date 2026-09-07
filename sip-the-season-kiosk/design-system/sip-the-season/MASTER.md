# Design System Master File — Sip The Season Kiosk

> **LOGIC:** When building a specific screen, first check `design-system/sip-the-season/pages/[screen].md`.
> If that file exists, its rules **override** this Master file. If not, follow the rules below.

---

**Project:** Sip The Season — BIA departure-store touch kiosk (ETS)
**Generated:** 2026-09-07 by `ui-ux-pro-max --design-system` (Luxury/Premium Brand profile, Motion 3/10, Density 3/10)
**Reconciled:** 2026-09-07 against `STS_Kiosk_PRD.md` (§4–§11) and the shipped `sip-the-season-kiosk.html`

> **Provenance / how to read this file.** The skeleton and the generic rules
> (spacing scale, shadow ramp, component CSS, anti-patterns, motion principles,
> checklist) came from the `ui-ux-pro-max` skill. The **style**, **navigation
> pattern**, **palette**, and **typography** it auto-selected were wrong for a
> physical retail kiosk (it picked "Liquid Glass / Apple system chrome" and a
> marketing scroll-narrative) and have been **replaced** with the PRD-locked and
> already-shipped direction. Where the tool still adds value it's kept and
> labelled *(ui-ux-pro-max)*.

---

## 1. What this is (context that drives every rule)

- **Physical walk-up kiosk**, portrait tablet, single fixed orientation. Not responsive web. No mouse, no hover, no keyboard for customers (keyboard/focus rules still apply for staff + accessibility hardware).
- **Self-contained `.html`** — no build step, no network calls. Everything (data, CSS, fonts-as-stacks, icons-as-inline-SVG) is inline. **No CDN `@import`, no web-font fetch, no GSAP/ScrollTrigger.**
- **Information architecture:** `Attract → Category → Range → Blend`. A browse tree, not a landing page. There is **no "CTA"**, no conversion funnel, no scroll narrative.
- **Shared device, many short sessions.** Language / text-size / contrast are session-scoped and reset on the inactivity timeout (`INACTIVITY_TIMEOUT_MS`, provisional 90 s) and on explicit Home — never persisted.
- **Five languages** switchable live (en / ja / de / es / fr); every visible string is a `{en,ja,de,es,fr}` object. Layout must survive the longest translation at the largest text size.
- **Brand:** ETS "Sip The Season" — seasonal / winter, premium, warm, gift-shop context. Real ETS illustration + logo assets are **not yet in the repo**; current icons/wordmark are neutral placeholders (PRD §4.3, §6).

---

## 2. Color palette (authoritative — from the shipped build)

These are the tokens already in `sip-the-season-kiosk.html` `:root`. **Do not swap the palette** — it's the deliberate STS winter direction.

| Role | Token | Hex |
|------|-------|-----|
| Surface / app background | `--wine-900` | `#3a0e26` |
| Raised surface | `--wine-800` | `#4a1030` |
| Raised surface (alt) | `--wine-700` | `#541232` |
| Primary accent (CTA-equivalent, active state) | `--gold-600` | `#cb951a` |
| Accent hover / highlight | `--gold-400` | `#e0b04a` |
| Card / content background | `--cream-100` | `#fbf6ea` |
| Card background (alt) | `--cream-050` | `#f8f2e2` |
| Text on cream | `--ink` | `#2a1018` |
| Success / "available" | `--green-600` / `--green-500` | `#27813a` / `#2e9d40` |
| Decorative (Santa's Stockings, soft tags) | `--pink-300` | `#e6afcb` |
| Decorative (cool accent) | `--navy-500` | `#2f5d8a` |

**Contrast checks required** (WCAG 4.5:1 normal text, 3:1 large/non-text) in **both** default and high-contrast mode:
- `--ink` on `--cream-100` — primary reading pair, must pass 4.5:1.
- cream text on `--wine-900` — Attract / chrome, must pass 4.5:1.
- `--gold-600` on `--wine-900` and gold as a focus ring on cream — verify 3:1 non-text.
- High-contrast mode must raise every pair that's borderline, not just darken text.

### Cross-check *(ui-ux-pro-max, "Luxury/Premium Brand")*

The tool's independent palette for this product category was **premium near-black + gold**: primary `#1C1917`, accent `#A16207`, background `#FAFAF9`. Same instinct as wine+gold+cream (dark ground, single warm-metal accent, off-white content). Useful only as a sanity check that the shipped direction is on-genre — not something to adopt.

---

## 3. Typography (PRD §4.3 — locked)

- **Display / headings:** **Fraunces** (intended). Currently stubbed with `"Iowan Old Style","Palatino Linotype","Book Antiqua",Georgia,"Noto Serif JP",serif`. A high-contrast humanist serif — keep the stub in the same family of feel until real Fraunces files are embedded.
- **Body / UI:** **Inter** (intended). Currently stubbed with the system sans stack (`-apple-system,"Segoe UI",Roboto,…`).
- **Do not introduce a third typeface** without Ray's input (PRD §4.3, explicit). The `ui-ux-pro-max` run suggested "Cormorant + Montserrat" with a Google-Fonts `@import` — **ignore it**: wrong fonts, and a network `@import` breaks the no-network rule.
- *(ui-ux-pro-max corroboration:* a direct `--domain typography` search for "warm elegant editorial premium serif" returns **"Classic Elegant — Playfair Display + Inter"**, *"Best For: luxury brands, spa, editorial."* Inter body confirmed; Playfair Display is the same high-contrast-display-serif slot Fraunces fills.)
- When real fonts are added: **self-host / embed as base64 `@font-face`**, `font-display: swap`, Latin + `latin-ext` + Japanese subsets (`ja` is a shipped language).

### Text-size cycle (shipped)

`html[data-textsize]` → `normal 16px` / `large 19px` / `xlarge 23px`. Every layout must hold at `xlarge` **in the longest language**. Follow the *Text Reflow* rule below.

---

## 4. Spacing, radii, shadows, motion tokens

### Spacing — Spacious *(ui-ux-pro-max, Density 3/10)*

| Token | Value | Usage |
|-------|-------|-------|
| `--space-xs` | `0.25rem` (4px) | tight inline gaps |
| `--space-sm` | `0.5rem` (8px) | **minimum gap between two touch targets** |
| `--space-md` | `1.5rem` (24px) | standard card padding |
| `--space-lg` | `2rem` (32px) | screen gutters, section padding |
| `--space-xl` | `3rem` (48px) | between major blocks |
| `--space-2xl` | `4rem` (64px) | Attract vertical rhythm |

### Radii (shipped)

`--radius-lg: 1.4rem` (cards, tiles) · `--radius-md: 1rem` (buttons, inputs) · `--radius-sm: 0.6rem` (chips, tags).

### Shadows (shipped — warm-tinted, keep)

```css
--shadow-soft: 0 0.6rem 1.6rem rgba(20,4,12,0.28), 0 0.15rem 0.4rem rgba(20,4,12,0.18); /* resting cards */
--shadow-lift: 0 1rem 2.4rem rgba(20,4,12,0.34), 0 0.25rem 0.6rem rgba(20,4,12,0.2);   /* active / pressed tile */
```

Shadows are tinted toward the wine ground (`rgba(20,4,12,…)`), not neutral black — keep that. *(The tool's generic `rgba(0,0,0,…)` ramp is superseded.)*

### Motion — Subtle *(ui-ux-pro-max, Motion 3/10)*

- **Transport:** plain CSS `transition` / `animation` only. No GSAP, no ScrollTrigger, no `startViewTransition` (nice-to-have per PRD, CSS fallback already accepted).
- **Durations (shipped):** `--dur-fast: 180ms` (press feedback, hover-equivalent) · `--dur-med: 320ms` (screen fade between Attract/Category/Range/Blend).
- **Principles:** movement ≤ 8–16px so it reads as a fade not a slide; exit faster than enter; motion conveys the browse-tree direction (forward = fade-in from slightly below/right, Back = reverse).
- **`prefers-reduced-motion: reduce`** → drop the fade, drop the **snow effect on Attract**, render final state immediately.
- **Snow / any auto-animating Attract content** must self-pause when not visible and under reduced motion *(ui-ux-pro-max: "Auto-Rotating Content Controls", severity High)*.

---

## 5. Component specs

Starting points from *(ui-ux-pro-max)*, retokenised to the STS palette. Treat as a baseline, match the shipped file's real classes.

### Tap target (global)

```css
/* Shipped: --tap-min: 3.6rem (~57px). Floor for ANY interactive element. */
.btn, .tile, .card[role="button"], .chip, .lang-option {
  min-block-size: var(--tap-min);
  min-inline-size: var(--tap-min);
}
/* WCAG 2.2 target-size floor is 24px; kiosk practice is 44px+; STS uses ~57px. Keep it generous. */
```

### Primary action / active state

```css
.btn-primary {
  background: var(--gold-600);
  color: var(--ink);                 /* verify 4.5:1 — gold is light, ink text not white */
  border-radius: var(--radius-md);
  padding: 0.9rem 1.6rem;
  font-weight: 600;
  transition: background var(--dur-fast) ease, transform var(--dur-fast) ease;
  cursor: pointer;
}
.btn-primary:active { background: var(--gold-400); transform: translateY(1px); }  /* press, not hover */
.btn-primary:focus-visible { outline: 3px solid var(--gold-400); outline-offset: 3px; }
```

### Category / Range / Blend card

```css
.card {
  background: var(--cream-100);
  color: var(--ink);
  border-radius: var(--radius-lg);
  padding: var(--space-md);
  box-shadow: var(--shadow-soft);
  transition: box-shadow var(--dur-fast) ease, transform var(--dur-fast) ease;
  cursor: pointer;
}
.card:active { box-shadow: var(--shadow-lift); transform: translateY(-2px); }
.card:focus-visible { outline: 3px solid var(--gold-600); outline-offset: 4px; }
```

### Search input

```css
.search {
  min-block-size: var(--tap-min);
  padding: 0.75rem 1rem;
  border: 2px solid color-mix(in srgb, var(--ink) 25%, transparent);
  border-radius: var(--radius-md);
  font-size: 1rem;                    /* scales with --textsize; never below 16px */
  background: var(--cream-050);
  color: var(--ink);
}
.search:focus-visible { border-color: var(--gold-600); outline: none; box-shadow: 0 0 0 3px var(--gold-400); }
```

### "Coming soon" state (tasting notes, ingredients, price, photography, cert badge)

PRD §6/§11: these fields are **empty by design** — never fabricate copy. Render a quiet placeholder, not an error:

```css
.pending { color: color-mix(in srgb, var(--ink) 55%, transparent); font-style: italic; }
```

### Modal / overlay (language picker, blend zoom)

```css
.modal { background: var(--cream-100); color: var(--ink); border-radius: var(--radius-lg);
         padding: var(--space-lg); box-shadow: var(--shadow-lift); max-inline-size: 32rem; }
.modal-scrim { background: rgba(20,4,12,0.55); }   /* wine-tinted; NO backdrop-filter blur — kiosk GPU budget */
```

---

## 6. Style direction (replaces the tool's "Liquid Glass")

**Warm editorial soft-UI.** Cream content cards floating on a deep wine ground; single gold metal accent; generous rounded corners; soft warm-tinted elevation; high-contrast serif display over clean sans body; restrained seasonal decoration (snow, stocking swatches). Closest catalogue references *(ui-ux-pro-max `--domain style`)*: **Soft UI Evolution** (evolved soft UI, better contrast, accessibility-focused) and **Editorial Grid / Magazine** for the Range/Blend reading layouts. **Not** glassmorphism / Liquid Glass (translucency + blur — GPU cost, contrast risk, wrong genre), **not** Swiss/enterprise minimalism, **not** bento/brutalism.

---

## 7. Navigation & screen pattern (replaces the tool's "Scroll-Triggered Storytelling")

| Screen | Job | Notes |
|--------|-----|-------|
| **Attract** | Draw a passer-by in; show the wordmark + season; single "Browse / Touch to start" affordance | snow effect; auto-reset target; language control reachable here |
| **Category** (3) | Choose a category | 3 tiles arranged around a circle/triangle with an oversized badge (`.tile .badge`) for this hero context; center short lists (`justify-content: safe center`), top-align long ones |
| **Range** (12) | Choose a range within the category | card grid; Santa's Stockings colour-swatch merge; allergen flag ("Contains licorice") only where PRD §6 places it |
| **Blend** (22) | Blend detail — name, range, brew time, allergen, (pending: photo / tasting notes / price / certs) | the design-heavy screen; wrap output in `.screen` so `.screen-scroll{flex:1 1 auto}` has real height |

Persistent chrome on every non-Attract screen: **Back** (`arrow-left`), **Home**, **Search** (`magnifying-glass`), **Language** (`globe`), **Text size**, **Contrast**. All ≥ `--tap-min`, ≥ 8px apart, all with a visible `:focus-visible` ring, none overlapping the scroll area's first item.

**Search** matches blend name / range name / style number → jumps straight to a Blend or highlights a Range card.

---

## 8. Kiosk & accessibility rules *(ui-ux-pro-max `--domain ux`, filtered to what applies)*

| Rule | Requirement | Severity |
|------|-------------|----------|
| **Touch target size** | ≥ `--tap-min` (~57px); never below 44px. Expand hit area when the glyph is smaller. | High |
| **Touch spacing** | ≥ 8px (`--space-sm`) between adjacent targets. | Medium |
| **Text reflow & spacing** | Fluid sizes, content-driven height, unitless `line-height`, `inline-size: min(100%, 60ch)` for copy. **Never** clip text in fixed-width/height boxes — it must survive `xlarge` × longest language. | Critical |
| **Focus visible** | Every interactive control (incl. inside modals) shows a `:focus-visible` ring; never `outline:none` without a replacement. Keep the focused control fully unobscured by sticky chrome. | High |
| **Colour contrast** | ≥ 4.5:1 normal text, ≥ 3:1 large/non-text — verified in default **and** high-contrast mode. | High |
| **Reduced motion** | `prefers-reduced-motion` drops fades + snow, renders final state. | High |
| **Auto-animating content** | Snow / any looping Attract animation pauses off-screen and under reduced motion. | High |
| **Colour not sole signal** | "Available", allergen, stocking colours — pair colour with text/icon. | High |
| **Idle reset** | Inactivity + explicit Home reset language / text-size / contrast / nav to defaults; Back one screen does **not**. | — |
| **Long content** | Truncate with `line-clamp` + an expand affordance; never overflow the card. | Medium |

---

## 9. Icons

Current build uses **hand-drawn inline SVG** placeholders (no ETS illustration sheet in-repo). Keep them inline (no icon-font, no network). Do **not** use emoji as UI icons. If a placeholder needs replacing before real ETS art lands, match Phosphor names for consistency *(ui-ux-pro-max `--domain icons`)*: Back `arrow-left`, Search `magnifying-glass`, Language `globe`, Reset/Home `arrow-counter-clockwise` / `house`. Meaningful standalone icons need a text label or `aria-label`; decorative ones get `aria-hidden="true"`.

---

## 10. Anti-patterns — do NOT

*(ui-ux-pro-max, kept)*
- ❌ Cheap / low-effort visuals; ❌ fast, snappy animations (premium brand → measured, soft).
- ❌ Emoji as icons. ❌ Missing press feedback. ❌ Layout-shifting hover/press (transform that moves neighbours). ❌ Low-contrast text. ❌ Instant (0ms) state changes. ❌ Invisible focus.

*(kiosk-specific, added)*
- ❌ `backdrop-filter: blur()` / heavy translucency — kiosk GPU budget + contrast risk.
- ❌ Any network request, CDN `@import`, or web-font fetch.
- ❌ Fabricated tasting notes / ingredients / prices / certification claims (PRD §6, §11 — needs Ray's sign-off).
- ❌ Persisting language / text-size / contrast across the idle reset.
- ❌ Fixed-height text containers; anything that assumes English-length strings.
- ❌ Hover-only affordances (no hover on a touch kiosk).
- ❌ Responsive-web breakpoint thinking — this is one fixed portrait viewport.

---

## 11. Pre-delivery checklist (fixed portrait kiosk)

- [ ] Every interactive element ≥ `--tap-min`, ≥ 8px apart, `cursor: pointer`, visible `:focus-visible`.
- [ ] Press feedback on every tile/button within ~180ms; no layout shift on press.
- [ ] Contrast ≥ 4.5:1 (text) / 3:1 (non-text) in **default and high-contrast** mode.
- [ ] Full walk-through at `xlarge` text size in **each** of en/ja/de/es/fr — no clipping, no overflow, no horizontal scroll.
- [ ] `prefers-reduced-motion`: fades + snow disabled, final state shown.
- [ ] Snow / auto animation pauses when Attract is not the active screen.
- [ ] Idle timeout + Home reset language/text-size/contrast/nav; Back does not.
- [ ] Pending fields (photo, tasting notes, price, certs) render the quiet "coming soon" state — no invented copy.
- [ ] Allergen flag ("Contains licorice") shows exactly where PRD §6 places it, colour + text.
- [ ] No network calls, no CDN imports, no external fonts — open `file://` with everything working.
- [ ] Icons inline SVG, none are emoji, standalone ones have `aria-label`.
- [ ] Keyboard: Tab reaches every control in visual order, focus never hidden behind sticky chrome (staff / a11y hardware).

---

## 12. Regenerating

```bash
# from the repo root (test01/)
python3 .claude/skills/ui-ux-pro-max/scripts/search.py "luxury premium tea seasonal gifting boutique" \
  --design-system --motion 3 --density 3 -p "Sip The Season" \
  --persist --output-dir sip-the-season-kiosk --force
```

`--force` overwrites this file — **it also discards the reconciliation above**. If you regenerate, re-apply sections 1, 2, 3, 6, 7 (the parts the tool gets wrong for a kiosk), or diff against this version first. For a single screen, add `--page blend` to write `pages/blend.md` instead of touching Master.
