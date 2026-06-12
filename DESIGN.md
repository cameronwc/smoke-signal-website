# Smoke Signal — Design System
## "Topographic Command Surface"

> Art direction reference for all Smoke Signal web properties.
> Self-contained: everything needed to extend this system lives in this file.

---

## 1. Concept

**Topographic Command Surface.** A backcountry mission-control aesthetic: the page is
an instrument you read, not a brochure you scroll. The base is cold, near-black
wilderness — deep blue-green-black, the color of an alpine basin at dusk — textured
with faint topographic contour lines and a coordinate grid. Against that cold field
sits exactly one warm color: the ember. A single point of warm light in cold
wilderness is literally the product promise, so the accent is never decoration —
it marks *signal*: the CTA, the live dot, the propagating pulse. Monospaced
technical readouts (coordinates, elevation, bearings, step indices) are used as
typographic texture, the way a map sheet carries its marginalia.

**Honesty constraint:** this is a safety/communications product. Decorative readouts
must be scene-setting values (coordinates, elevation, headings) — never invented
product specs (no fabricated range km, SF, or dBm figures).

---

## 2. Color Tokens

Elevation is expressed by *light* (each surface step is slightly lighter and slightly
warmer-gray) — never by drop shadow.

```css
:root {
    /* Basin ramp — cold blue-green neutrals, darkest = page */
    --basin-0: #060a0c;   /* page background, deepest field            */
    --basin-1: #0a1114;   /* alternating section surface               */
    --basin-2: #0e171b;   /* card / panel resting surface              */
    --basin-3: #142025;   /* raised panel, hover surface, inputs       */
    --basin-4: #1b2b31;   /* highest surface: active states            */

    /* Linework */
    --ridge:        #22343b;                 /* borders, frames, dividers   */
    --ridge-bright: #31474f;                 /* hover borders, active frames*/
    --contour:      rgba(76, 110, 122, 0.16);/* topo lines, grid texture    */

    /* Ember — the ONLY warm color on the page */
    --ember:      #f97316;                   /* signal: CTA, live dot, pulse */
    --ember-high: #fb923c;                   /* hover tint, accent text      */
    --ember-glow: rgba(249, 115, 22, 0.14);  /* radial light, never fill     */

    /* Text */
    --text-hi:  #e9eef0;   /* headlines, primary copy                   */
    --text-mid: #a3b2b9;   /* body, descriptions                        */
    --text-low: #76878f;   /* mono marginalia, footer meta (AA on basin-0/1/2) */

    /* Functional */
    --ok: #34d399;         /* success state ONLY (form success, live badge dot) */
    --danger: #f87171;     /* error state ONLY (form errors)            */
}
```

**Enforcement rules**
- `--ember` appears only on: primary CTA, the live "coming soon" dot is `--ok` (status),
  signal pulses/nodes in the hero, section eyebrow labels, focus rings, link hover.
- `--ember` is **never** a panel fill, never a large background, never a border
  default. If a surface needs emphasis, step it up the basin ramp instead.
- Text on `--ember` fills is `--basin-0` (dark-on-ember), never white.
- `--text-low` is the floor: nothing dimmer may carry meaning. Verify ≥ 4.5:1
  against its actual surface (it passes on basin-0 → basin-2; on basin-3 use `--text-mid`).

---

## 3. Type System

No webfonts (CSP + performance): the system is built from the platform's best faces.

```css
--font-display: -apple-system, BlinkMacSystemFont, "SF Pro Display",
                "Segoe UI", Roboto, sans-serif;       /* weight 300 at size  */
--font-body:    -apple-system, BlinkMacSystemFont, "SF Pro Text",
                "Segoe UI", Roboto, sans-serif;       /* weight 400          */
--font-mono:    ui-monospace, "SF Mono", "Cascadia Code", Menlo,
                Consolas, monospace;                   /* readouts, labels    */
```

Headlines earn presence through **size and restraint, not weight**. Light (300)
display type at large sizes reads as confident and instrument-like; bold would
read as advertising.

| Token         | Size                       | Weight | Line-height | Tracking  | Use |
|---------------|----------------------------|--------|-------------|-----------|-----|
| `display`     | `clamp(2.75rem, 7vw, 5.25rem)` | 300 | 1.04        | `-0.025em`| Hero h1 only |
| `headline`    | `clamp(1.9rem, 4vw, 2.9rem)`   | 300 | 1.12        | `-0.02em` | Section h2 |
| `title`       | `1.2rem`                   | 500    | 1.3         | `-0.01em` | Card h3 |
| `body-lg`     | `1.15rem`                  | 400    | 1.7         | 0         | Hero sub, section intros |
| `body`        | `1rem`                     | 400    | 1.65        | 0         | Default copy |
| `body-sm`     | `0.9rem`                   | 400    | 1.6         | 0         | Card copy |
| `mono-label`  | `0.75rem`                  | 500    | 1.4         | `0.14em`, uppercase | Eyebrows, axis labels |
| `mono-value`  | `clamp(1.9rem, 3.5vw, 2.6rem)` | 400 | 1.1       | `0.02em`  | Instrument numerals |
| `mono-meta`   | `0.72rem`                  | 400    | 1.5         | `0.06em`  | Coordinates, marginalia |

Emphasis inside a light headline = `--ember-high` color on a word, same weight.
Never bold a headline word for emphasis.

---

## 4. Spacing & Shape

- **Base unit:** 4px. All spacing is a multiple of it.
- **Section rhythm:** 96–128px vertical gap between sections (`clamp(6rem, 12vh, 8rem)`).
- **Content width:** max 1200px; instrument panels and hero art may run wider (full-bleed) while text stays inside.
- **Density:** spacious. Card padding 32px; never crowd a readout.

**Shape language — committed:**
- **Surfaces are sharp.** Panels, cards, frames: `border-radius: 0`, 1px `--ridge`
  border. Key frames carry **corner ticks** (small L-shaped marks at the corners,
  like a map sheet or camera reticle) — this is the signature container.
- **Controls are the single rounded element type.** Buttons, inputs, and the badge
  use `border-radius: 6px` (badge may run pill). Nothing else rounds. The contrast
  between sharp instrument frames and rounded touch targets *is* the shape system.

```css
--radius-control: 6px;   /* buttons, inputs            */
--radius-pill: 999px;    /* status badge only          */
/* everything else: 0    */
```

---

## 5. Motion Principles

Motion is *signal behavior*, not decoration.

1. **Propagation pulses** — rings expanding from node points (hero, SOS icon).
   Transform/opacity only (GPU-cheap), 5–8s cycles, staggered. Never more than
   3 concurrent ring animations per node cluster.
2. **Light-up hovers** — border shifts `--ridge → --ridge-bright`, surface steps
   one basin level, 0.2s ease. No translateY lifts, no glow shadows.
3. **Reveal** — sections fade up 12px / 0.6s on first intersection. Once.
4. **No scroll-jacking. No parallax that fights the scrollbar.** Contour drift, if
   used, is a single slow transform loop ≥ 60s, opacity ≤ 0.2.

```css
@media (prefers-reduced-motion: reduce) {
    /* ALL animation and transition durations forced to 0.01ms;
       pulses render as static concentric rings — the frozen frame must
       be composed, not broken. */
}
```

Mobile: pulse animations are capped (fewer rings, or static) below 768px.

---

## 6. Iconography

- **Custom inline SVG only. Zero emoji anywhere** — content, favicon, decoration.
- Grid: 24×24 viewBox. Stroke: `1.5`, `stroke="currentColor"`, `fill="none"`,
  `stroke-linecap="square"`, `stroke-linejoin="miter"` — square caps and mitered
  joins keep the technical/drafting character (round caps read as friendly-SaaS).
- Drawn from the topographic vocabulary: contour fragments, node-and-ring, pin
  with crosshair, route polyline, radio mast. An icon should look like it was
  lifted from a map legend.
- Decorative icons: `aria-hidden="true"`. Meaningful icons: text label adjacent,
  never icon-alone.
- Favicon: node-and-rings mark in `--ember` on `--basin-0`, inline SVG data URI.

---

## 7. Texture & Surface

- **Contour layer:** inline SVG of nested irregular closed paths (topographic
  contours), stroke `--contour`, no fill. Fixed/absolute behind hero and final CTA.
  Opacity ≤ 0.2. One asset, reused.
- **Coordinate grid:** CSS `repeating-linear-gradient` hairlines at 64px intervals,
  `--contour` color, on instrument sections (stats, footer spec sheet).
- **Film grain:** SVG `feTurbulence` data-URI tile, opacity ≤ 0.04, on `body::after`
  (pointer-events none). Kills the flat-gradient banding, reads as paper/film.
- **Elevation = light.** A raised element gets a lighter basin step and a brighter
  ridge line. **Drop shadows are banned.** `box-shadow` may appear only as a focus
  ring (`0 0 0 2px`).
- Marginalia: `mono-meta` coordinates / elevation / bearing strings may annotate
  section frames (e.g. `46.8520, -121.7260 · ELEV 4392M`). Scene-setting values
  only — never invented product performance figures.

---

## 8. Do / Don't

| DO | DON'T |
|----|-------|
| One warm accent, reserved for signal and CTA | Don't introduce a second hue family (no blues/purples for variety) |
| Light-weight display headlines at large sizes | Don't use 700–800 weight headlines — this isn't a SaaS template |
| Elevation via lighter surface + brighter border | Don't use drop shadows or glows for depth |
| Sharp frames with corner ticks; rounded controls only | Don't round cards/panels; don't sharpen buttons |
| Mono for every number, coordinate, and label | Don't set numerals in the display face |
| Real coordinates/elevation as marginalia texture | Don't print fabricated specs (range km, SF, dBm) anywhere |
| Custom map-legend SVG icons, stroke 1.5, square caps | Don't use emoji, icon fonts, or rounded-friendly icon sets |
| Pulses, light-ups, one-time reveals — all reduced-motion safe | Don't scroll-jack, autoplay video, or animate layout properties |
| Dark-on-ember text for CTA | Don't put white text on ember fills |
| Spacious 96–128px section rhythm | Don't compress sections to "fit above the fold" |

---

## 9. Reference Family

Synthesized from the command-center / blueprint / midnight family (Mercury,
Antimetal, AuthKit, Active Theory): borrowed *principles* — disciplined single
accent, instrument typography, elevation-by-light, restrained motion — not any
single layout. The topographic/wilderness layer is Smoke Signal's own and is what
keeps this from reading as a fintech clone.

---

## 10. Page Architecture (canonical order)

1. **Hero** — atmospheric: contour terrain + signal-propagation nodes (the one
   signature animated element), display headline, badge, email CTA, anchor link.
2. **Stat strip** — precision instrument row: mono numerals, hairline-divided,
   coordinate-grid surface. Reads as a device readout.
3. **Features** — asymmetric bento grid on sharp panels; custom icons; elevation
   by light on hover.
4. **How It Works** — field-manual sequence: mono step indices (01 / 02 / 03 / 04),
   hairline rail, calm technical copy.
5. **Use cases** — terrain-tagged grid; each case carries a map-legend icon and a
   mono terrain tag.
6. **Final CTA** — contour layer returns; ember CTA; affiliate link as secondary.
7. **Footer** — requirements as a mono spec-sheet block; links in quiet columns.
