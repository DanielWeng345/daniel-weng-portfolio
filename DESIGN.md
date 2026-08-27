# Design

<!-- impeccable:design-schema 1 -->

## Direction

**Assembly / technical drawing sheet.** The site reads as an engineering
drawing rather than a conventional portfolio: a minimal sticky title block
(now just a live SHEET counter — see Components), a bill-of-materials-style
section index, dimension-line section dividers, and project cards framed as
parts entries. Chosen because it's grounded in what the subject (a
mechanical engineer) actually produces in Fusion 360/Onshape and on real
drawing sheets — not the generic dark-gradient "AI portfolio" default, and
not a literal blueprint-grid cliché either.

## Palette

Committed strategy: one neutral (graphite) carries the full page ground; a
single saturated accent (electric blue) marks callouts, links, and active
state — a blueprint-table register (graphite + blue, evoking a drafting
sheet) rather than a shop-floor one. A hazard-yellow variant was drafted at
one point but never shipped; the code has stayed on blue since the first
commit. Variable names (`--graphite-*`, `--accent`) are hue-agnostic by
design in case the accent changes again.

| Role | Value | Usage |
|---|---|---|
| `--graphite-950` | `#1b1d21` | page background |
| `--graphite-900` | `#24272c` | (reserved) panel background |
| `--graphite-800` | `#2f3339` | (reserved) raised surface |
| `--graphite-700` | `#3c4149` | hover surface |
| `--ink` | `#f4f5f6` | primary text, line art |
| `--ink-dim` | `rgba(244,245,246,.66)` | secondary text |
| `--ink-faint` | `rgba(244,245,246,.42)` | labels, captions, dimension lines |
| `--accent` | `#4d8dff` | callouts, links, active state, CTA fill |
| `--accent-dim` | `rgba(77,141,255,.16)` | active-state tint fills |
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

- **Title block** (`.titleblock`) — sticky top bar, deliberately stripped
  down from a full drawing title block to just the one live field that
  earns its permanent screen real estate: a right-aligned `SHEET n OF 6`
  counter. No border, no background seam — it blends into the page ground
  rather than reading as a separate chrome bar. The DWG NO./SCALE/REV/name
  fields from the original drawing-sheet title block were cut as clutter;
  the sign-off footer still carries a cosmetic `DW-2026-001 · REV A` line
  for that drawing-sheet flavor (see Open items).
- **BOM nav** (`.bom`) — numbered section index (01–05) styled as a
  bill-of-materials table; doubles as the page's navigation. Hovering/
  focusing a tab pops a tooltip with the section name *and* a short
  description (e.g. "Experience" → "Caltech & Cypress Envirosystems
  roles"); the mobile bottom strip shows only the short label, no
  description, since there's no room there.
- **Hero** — text-only: kicker, name, subtitle, location, and two CTAs
  (Contact, Resume — the latter opens `/Daniel-Weng-Resume.pdf` in a new
  tab). Earlier drafts explored hero art (a hand-authored exploded-bolt SVG,
  later a procedurally-generated spinning ASCII gear/hex-bolt) but both were
  cut; the hero is copy-only for now.
- **Sheet section** (`.sheet`) — numbered section header (`.sheet__n`, boxed
  mono numeral) + hairline top border; header fades/lifts in on scroll via
  IntersectionObserver (respects `prefers-reduced-motion`).
- **Card** (`.card`) — experience/project entries; header row with role/org
  left, mono date right in accent blue; bullets marked with a small chevron
  glyph (not an em dash — kept out of body copy to avoid AI-cadence tells).
- **Photo evidence gallery** (`.card__figures`/`.card__figure`) — Experience
  cards only (`.card--media`): each entry's `images` array renders as one
  bordered figure panel per photo, stacked vertically, so a card can carry
  any number of photos. FIG. numbers are sequential across the *whole
  section* (like a real BOM sheet), not per-card, computed once at build
  time; a card with no photos yet still reserves its number and shows a
  dashed-border "Photo pending" placeholder. Currently real: Caltech (FIG.
  01, a field-test GIF of the quadruped) and Cypress Envirosystems (FIG. 02,
  a CAD screenshot of the housing bracket).
- **Experience tabs** (`.tabs`/`.tabpanel`) — one employer shown at a time
  instead of stacking every role: a vertical ARIA tablist ("Caltech" /
  "Cypress Envirosystems") down the left, content panel to the right. Each
  tab shows the org name, then a second line with the date and a short
  italic description (e.g. "Robotic arm mounting & diagnostics") to its
  right. Active tab gets a left accent bar + tint. Full keyboard support
  (Up/Down/Home/End, roving tabindex) per the vertical-tabs pattern.
- **Experience is the feature section** (`.sheet--feature`, `.card--feature`)
  — deliberately more prominent than Other Projects: a wider container
  (1160px vs. the shared 900px), a larger role heading and body copy, and a
  bigger photo-evidence gallery (260px-tall panels vs. a plain
  unillustrated project card). Reinforces "real work experience is the
  headline, projects are supporting" per PRODUCT.md's positioning.
- **Spec table** (`.spec__row`) — skills grouped by category, rendered as
  label + chip row.
- **Sign-off** (`.signoff`) — contact section styled as a drawing's
  approval block.

## Motion

Section headers translate up + fade in on first scroll into view (one-shot,
threshold 0.4) — supporting motion, unchanged. The focal moment is the page
turn itself: the title block's SHEET field ticks (`n OF 6`, brief accent-blue
flash) each time the active sheet changes, driven by an IntersectionObserver
picking whichever `[data-sheet]` section currently has the greatest visible
ratio (robust to sections taller than one viewport). The BOM/page-rail tab
for the current section fills accent blue in sync. Both paths are gated on
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
Content column widths (900–1120px via `clamp()`) are unchanged. The hero is
a single centered copy column at every width now (no art column to collapse
on mobile), and the title block is one row at every width too, since it
only holds the single right-aligned SHEET field.

## Open items / not yet real

- No headshot yet, and Other Projects (Dog Feeder, Electric Skateboard)
  still has no photos — text-only for now. Experience has real photos for
  both entries (see Photo evidence gallery above); extend the same
  `images` array pattern to Other Projects whenever Daniel provides some.
- Deploy target undecided (Vercel/Netlify/GitHub Pages all compatible with
  the static Astro build).
- `DW-2026-001` / `REV A` in the sign-off footer are stylistic drawing-sheet
  numbering, not real document IDs — cosmetic only.
