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

Committed strategy: one saturated color (blueprint blue) carries the full
page ground; a single accent (safety orange) marks callouts, links, and
active state.

| Role | Value | Usage |
|---|---|---|
| `--blue-950` | `#0a1f38` | page background |
| `--blue-900` | `#0f2a4a` | (reserved) panel background |
| `--blue-800` | `#17395f` | (reserved) raised surface |
| `--blue-700` | `#1f4674` | (reserved) hover surface |
| `--ink` | `#f4f7fb` | primary text, line art |
| `--ink-dim` | `rgba(244,247,251,.66)` | secondary text |
| `--ink-faint` | `rgba(244,247,251,.42)` | labels, captions, dimension lines |
| `--orange` | `#ff6a1f` | accent: callouts, links, active state, CTA fill |
| `--line` / `--line-strong` | `rgba(244,247,251,.28/.5)` | hairline dividers, borders |

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
  assembly with orange leader-line callouts and a dimension mark. Not a
  stock icon; specific to the mechanical-assembly theme.
- **Sheet section** (`.sheet`) — numbered section header (`.sheet__n`, boxed
  mono numeral) + hairline top border; header fades/lifts in on scroll via
  IntersectionObserver (respects `prefers-reduced-motion`).
- **Card** (`.card`) — experience/project entries; header row with role/org
  left, mono date right in orange; bullets marked with a small chevron glyph
  (not an em dash — kept out of body copy to avoid AI-cadence tells).
- **Spec table** (`.spec__row`) — skills grouped by category, rendered as
  label + chip row.
- **Sign-off** (`.signoff`) — contact section styled as a drawing's
  approval block.

## Motion

Section headers translate up + fade in on first scroll into view (one-shot,
threshold 0.4). No other motion. Kept deliberately restrained — the drawing-
sheet metaphor is about legibility and proof, not spectacle.

## Layout

Single long-scroll page, content column capped at 900–1120px depending on
section, generous padding via `clamp()`. Mobile collapses the hero to one
column (art above copy), title block fields wrap, tables stack.

## Open items / not yet real

- No photos yet (headshot, project photos) — currently text/line-art only,
  per PRODUCT.md. Replace hero art or add photo sections once available.
- Deploy target undecided (Vercel/Netlify/GitHub Pages all compatible with
  the static Astro build).
- `DW-2026-001` / `REV A` in the title block are stylistic drawing-sheet
  numbering, not real document IDs — cosmetic only.
