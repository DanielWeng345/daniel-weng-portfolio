# Design

<!-- impeccable:design-schema 1 -->

## Direction

**Assembly / technical drawing sheet.** The site reads as an engineering
drawing rather than a conventional portfolio: a title block header (DWG NO.,
SCALE, SHEET, REV), a bill-of-materials-style section index, dimension-line
section dividers, and project cards framed as parts entries. Chosen because
it's grounded in what the subject (a mechanical engineer) actually produces
in Fusion 360 and on real drawing sheets — not the generic dark-gradient
"AI portfolio" default, and not a literal blueprint-grid cliché either.

## Palette

Committed strategy: one neutral (graphite) carries the full page ground; a
single saturated accent (safety yellow) marks callouts, links, and active
state. Originally a blueprint-blue/safety-orange pairing; recolored to a
shop-floor/CNC register (graphite + hazard-yellow, evoking machine paint and
caution signage) rather than a drafting-table one — same one-ground/
one-accent strategy and variable roles, different hue family. Variable names
kept the old `--graphite-*`/`--yellow` shape rather than the previous
`--blue-*`/`--orange` naming.

| Role | Value | Usage |
|---|---|---|
| `--graphite-950` | `#1b1d21` | page background |
| `--graphite-900` | `#24272c` | (reserved) panel background |
| `--graphite-800` | `#2f3339` | (reserved) raised surface |
| `--graphite-700` | `#3c4149` | (reserved) hover surface |
| `--ink` | `#f4f5f6` | primary text, line art |
| `--ink-dim` | `rgba(244,245,246,.66)` | secondary text |
| `--ink-faint` | `rgba(244,245,246,.42)` | labels, captions, dimension lines |
| `--yellow` | `#ffc531` | accent: callouts, links, active state, CTA fill |
| `--line` / `--line-strong` | `rgba(244,245,246,.16/.36)` | hairline dividers, borders |

Dark ground is a fixed art direction (not tied to OS light/dark preference) —
a deliberate single-world choice for this surface, not an oversight.

## Type

- **Archivo** (400–900, plus italic 500) — headers and body. Bold/900
  uppercase for display; 400–600 for body copy.
- **JetBrains Mono** (400–700) — labels, dates, callouts, tabular data,
  anything meant to read like a drawing annotation or spec value.

Both loaded via Google Fonts. Avoided the default AI-portfolio faces
(Space Grotesk/Mono, DM Sans, Inter-as-display, serif-display pairings)
deliberately — Archivo + JetBrains Mono was chosen for the technical/
tabular register the drawing-sheet metaphor needs, not for novelty.

## Components

- **Title block** (`.titleblock`) — top-of-page header mimicking a drawing
  sheet's title block: name as DWG title, DWG NO./SCALE/SHEET/REV fields.
- **BOM nav** (`.bom`) — numbered section index styled as a bill-of-materials
  table; doubles as the page's navigation.
- **Hero art** — hand-authored inline SVG: an exploded bracket/washer/bolt
  assembly with yellow leader-line callouts and a dimension mark. Not a
  stock icon; specific to the mechanical-assembly theme.
- **Sheet section** (`.sheet`) — numbered section header (`.sheet__n`, boxed
  mono numeral) + hairline top border; header fades/lifts in on scroll via
  IntersectionObserver (respects `prefers-reduced-motion`).
- **Card** (`.card`) — experience/project entries; header row with role/org
  left, mono date right in yellow; bullets marked with a small chevron glyph
  (not an em dash — kept out of body copy to avoid AI-cadence tells).
- **Photo evidence panel** (`.card__figure`) — Experience cards only
  (`.card--media`): a dashed-border placeholder panel beside the bullets
  (below them on mobile) with an authored line-art image glyph and
  "FIG. 0n — Photo pending" caption, ready to swap for a real photo per
  entry once Daniel provides one.
- **Experience tabs** (`.tabs`/`.tabpanel`) — one employer shown at a time
  instead of stacking every role: a vertical ARIA tablist ("Caltech" /
  "Cypress Envirosystems", each with its date) down the left, content panel
  to the right. Active tab gets a left accent bar + tint. Full keyboard
  support (Up/Down/Home/End, roving tabindex) per the vertical-tabs pattern.
- **Experience is the feature section** (`.sheet--feature`, `.card--feature`)
  — deliberately more prominent than Other Projects: a wider container
  (1160px vs. the shared 900px), a larger role heading and body copy, and a
  bigger photo-evidence panel (260px vs. a plain unillustrated project
  card). Reinforces "real work experience is the headline, projects are
  supporting" per PRODUCT.md's positioning.
- **Spec table** (`.spec__row`) — skills grouped by category, rendered as
  label + chip row.
- **Sign-off** (`.signoff`) — contact section styled as a drawing's
  approval block.

## Motion

Section headers translate up + fade in on first scroll into view (one-shot,
threshold 0.4) — supporting motion, unchanged. The focal moment is the page
turn itself: the title block's SHEET field ticks (`n OF 6`, brief yellow
flash) each time the active sheet changes, driven by an IntersectionObserver
picking whichever `[data-sheet]` section currently has the greatest visible
ratio (robust to sections taller than one viewport). The BOM/page-rail tab
for the current section fills yellow in sync. Both paths are gated on
`prefers-reduced-motion`: reduced motion drops CSS scroll-snap entirely
(plain continuous scroll) and the tick animation, keeping only the instant
text/state update.

## Layout

Paged, not a single scrollable bar: the hero and each `.sheet`/`.signoff`
section is `min-height: 100svh` (viewport minus the measured title block
height) with `scroll-snap-align: start` on `html { scroll-snap-type: y
proximity }` — proximity, not mandatory, so a section taller than the
viewport (long card lists) never traps the scroll. The title block is
`position: sticky` at the top, acting as a persistent drawing-sheet frame
across every page. The BOM nav (`.bom`) moved from a one-time top-of-page
index into that same persistent frame: a fixed vertical rail of numbered
tabs on the right (desktop) showing labels as hover/focus tooltips, or a
fixed bottom tab strip with always-visible short labels (mobile, `<760px`,
since touch has no hover). ArrowDown/ArrowUp/PageDown/PageUp jump to the
next/previous sheet explicitly (`scrollIntoView`, respects reduced motion).
Content column widths (900–1120px via `clamp()`) and the mobile hero/title
block collapse are unchanged.

## Open items / not yet real

- No photos yet (headshot, project photos) — currently text/line-art only,
  per PRODUCT.md. Replace hero art once available. Experience cards now
  reserve visual space for photos (`.card__figure` placeholders); swap each
  in for a real `<img>` once Daniel provides one.
- Deploy target undecided (Vercel/Netlify/GitHub Pages all compatible with
  the static Astro build).
- `DW-2026-001` / `REV A` in the title block are stylistic drawing-sheet
  numbering, not real document IDs — cosmetic only.
