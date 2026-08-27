# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Daniel Weng's personal portfolio site — a single-page static Astro site aimed
at recruiters/hiring managers evaluating him for mechanical engineering
roles. Read `PRODUCT.md` (who it's for, what it must prove) and `DESIGN.md`
(the visual system) before making changes — they're the source of truth for
content and design decisions, not this file.

## Commands

```
npm install       # install deps (first time / after pulling changes)
npm run dev        # start dev server at http://localhost:4321
npm run build      # production build to dist/
npm run preview    # serve the built dist/ output locally
```

There is no test suite and no linter configured.

Node.js, Git, and FFmpeg were not preinstalled on this machine — all three
were installed via `winget` (OpenJS.NodeJS.LTS, Git.Git, Gyan.FFmpeg).
FFmpeg is only needed for one-off media prep (e.g. compressing a source
video down to a small GIF for a `card__figure` photo — see Architecture);
it's not a build dependency. If `node`/`npm`/`git`/`ffmpeg` aren't on PATH
in a fresh shell, refresh it with:
```powershell
$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User")
```

### Design linting

`impeccable` (github.com/pbakaus/impeccable) is installed as a project skill
at `.claude/skills/impeccable/` — it's an AI-design-guidance system (not an
npm package of this app). Its deterministic anti-pattern detector requires
`htmlparser2`, `css-select`, `css-tree`, and `domutils`, which are installed
as devDependencies here specifically so the detector can run at full
fidelity (without them it silently degrades to a weaker regex fallback).
Run it against the built output after any visual change:
```
npm run build
node .claude/skills/impeccable/scripts/detect.mjs --json dist/index.html
```
A clean run returns `[]`. Findings are advisory tells for AI-generated-design
smells (overused fonts/colors, em-dash overuse in body copy, low contrast,
etc.) — fix what's real, use judgment on the rest.

## Architecture

This is currently a **single-file site**: `src/pages/index.astro` contains
the entire page — content data (frontmatter JS objects for experience,
projects, education, skills), markup, a scoped `<style>` block, and an
inline `<script>`. There are no shared components yet; if the site grows
past one page, extract repeated pieces (the title-block header, page-rail
nav, card component) into `src/components/` before duplicating markup.

The page is **paged, not a long scroll**: each top-level section is a
`min-height: 100svh` panel with CSS scroll-snap (`[data-sheet]` attributes
number them 1–6), a sticky title block stays pinned across pages with a
live `SHEET n OF 6` counter (the title block's *only* field now — DWG NO./
SCALE/REV/name were cut; see `DESIGN.md`), and the old top-of-page BOM nav
is now a persistent fixed page-rail (`.bom`, right side desktop / bottom
strip mobile) whose tabs pop a tooltip with both the section name and a
short `desc`. The `<script>` block drives all of this: sticky-header height
measurement, the IntersectionObserver that tracks the active page,
Up/Down/PageUp/PageDown paging, and — separately — the Experience section's
tab-switching logic. Read `DESIGN.md`'s Layout/Motion sections before
touching any of this; it's easy to break the snap/sticky/rail interplay by
changing one piece in isolation.

The **Experience section is a deliberately-more-prominent "feature"
section** (`.sheet--feature`/`.card--feature`) than Other Projects: wider
container, larger type, a vertical ARIA tablist (one employer at a time,
`experience[i].tabLabel` drives the tab text, plus a `desc` shown next to
the date) instead of stacked cards, and a bigger photo-evidence gallery per
entry. Other Projects intentionally stayed plain (stacked cards, no photo
gallery, narrower container) — that contrast is the point, not an oversight.

Each `experience[i].images` is an array of `{ src, alt, caption }`, rendered
as one `.card__figure` panel per photo (`.card__figures` stacks them). FIG.
numbers are computed once in the frontmatter (`figCount`/`e.figs`) as a
running total across the *entire* experience array, not per card — so a
card with an empty `images` array still reserves the next number and shows
a dashed "Photo pending" placeholder, and adding a second photo to an entry
just pushes every later FIG. number up by one. Both current entries have
real media: Caltech's is a GIF (compressed from a source video with
`ffmpeg` — see Commands — since browsers autoplay/loop `<img src="*.gif">`
natively, no video player needed); Cypress's is a CAD screenshot PNG.

All page content (name, dates, bullet points, skills) is sourced from
`Resume (Engineering).pdf` (originally at
`C:\Users\Daniel\Downloads\Resume (Engineering).pdf`), transcribed
verbatim into the frontmatter data arrays in `index.astro` — do not invent
or embellish facts there; update PRODUCT.md's "Evidence on Hand" section
first if the underlying facts change, then sync `index.astro`. One
confirmed correction since the original transcription: languages is
"Mandarin Chinese," not "Shanghainese."

The visual direction (graphite ground, electric-blue accent, Archivo +
JetBrains Mono type, "engineering drawing sheet" metaphor: title block, BOM
page-rail, dimension-line dividers, parts-list-style project cards) is
recorded in full in `DESIGN.md`. Preserve that system when adding sections
rather than introducing new colors/components ad hoc — extend the existing
CSS custom properties (`--graphite-950`, `--ink`, `--accent`, etc., named
hue-agnostically on purpose in case the accent hue changes) defined in
`index.astro`'s `:root` block.

The hero is text-only (kicker, name, subtitle, location, Contact + Resume
buttons) — no headshot or hero art. Two earlier hero-art directions (a
hand-authored exploded-bolt SVG, then a procedurally-generated spinning
ASCII gear/hex-bolt built with a small isInsideX/buildXFrame generator in
the frontmatter plus a frame-cycling `<script>`) were both tried and then
removed; if hero art comes back, generate it rather than resurrecting
either of those from history. See `PRODUCT.md` / `DESIGN.md` "Open items"
for what photos are still missing (headshot, Other Projects).

The Resume button (`/Daniel-Weng-Resume.pdf`) opens a copy of
`Resume (Engineering).pdf` placed in `public/` under a cleaner name — keep
that file in sync if the source resume changes.

## Deployment

Not yet configured. The Astro `output: 'static'` build in `dist/` is
deployable as-is to Vercel, Netlify, or GitHub Pages; no target has been
chosen (see `PRODUCT.md` under Capabilities and Constraints).
