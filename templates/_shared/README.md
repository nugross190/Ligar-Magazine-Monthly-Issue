# Shared conventions for templates

Every shipped template must stay a **single standalone HTML file** — all CSS
and JS inlined, no build step, openable by double-clicking. So this folder is
not imported code; it's a reference to copy from when building the next
template, so the same patterns don't get re-derived (or drift) each round.
Canonical, working implementation of everything below lives in
`../majalah_functional_v1.html` — read that file for the real code.

## Design tokens (CSS custom properties)

```css
:root {
  --paper: #EEF0EC;      /* page background */
  --paper-alt: #E3E7DE;  /* secondary panel background */
  --ink: #14171F;        /* primary text / dark surfaces */
  --ink-soft: #4B5563;   /* secondary text */
  --blue: #2451FF;       /* accent 1 — links, active states, progress */
  --amber: #FFB100;      /* accent 2 — tags, highlights */
  --line: #B8C4BE;       /* borders, dashed slot outlines */
  --font-display: 'Space Grotesk', sans-serif;  /* headings */
  --font-body: 'IBM Plex Sans', sans-serif;     /* body copy */
  --font-mono: 'IBM Plex Mono', monospace;      /* labels, eyebrows, meta */
  --radius: 2px;         /* sharp, near-square corners throughout */
}
```

Reuse these variable names verbatim in new templates so a future palette
change is a find-and-replace across files, not a redesign.

## Card shell

Every page is a fixed **9:16 story card** (`aspect-ratio: 9 / 16`, capped at
`100svh`), stacked vertically with `scroll-snap-type: y mandatory` on `html`.
Don't build a template as a free-flowing full-width page — it has to work
standalone as a Stories/Reels-shaped social export too.

## Reusable JS behaviors

Three behaviors get re-wired per template; keep the same shape each time:

1. **Photo upload** — `requestUpload(targetEl, {maxDim, quality})` opens a
   single shared hidden `<input type="file">`, reads it via `FileReader`,
   draws it to an offscreen `<canvas>` to resize (`maxDim`) and compress
   (`quality`, JPEG), and sets the result as `background-image` on the
   target. Flags `lowRes` when the *original* image's shorter side is under
   ~1000px and renders a small `.low-res-badge`. Direct-click slots (grid
   thumbnails, collage tiles) wire the `.slot` itself as the target;
   full-bleed hero slots behind a text overlay (cover, profile spotlight)
   need a dedicated `.photo-edit-btn` instead, because the overlay div sits
   on top and swallows clicks meant for the background slot underneath.

2. **Editable text** — any element gets `class="editable" data-max="N"`.
   Bracketed placeholder text (`[Nama Guru]`) is stored in
   `data-placeholder`, shown dimmed/italic (`.is-placeholder`), cleared on
   focus, restored on blur if left empty. `input` handler hard-truncates at
   `data-max` — this is the actual enforcement the spec calls for, not just
   a visual counter. Split a sentence with a fixed prefix/suffix
   (e.g. "Dikurasi oleh **[Nama Koordinator]** — ...") into a static text
   node plus an inline `<span class="editable">` so the boilerplate wording
   can't be deleted by accident.

3. **Video embed** — the video `.slot` swaps its own contents for an inline
   `<form>` (URL input + submit) on click, extracts the 11-char YouTube ID
   via regex, and replaces itself with an `<iframe>` embed on submit. Embed
   only — never inline a video as base64; it bloats the single-file export
   past what a browser will load reliably (see the layout spec, §5).

## Floating chrome

Two fixed circular buttons, bottom corners: nav toggle (bottom-right, jump
menu built from `[data-nav]` sections + `IntersectionObserver` for active-
section highlighting) and info toggle (bottom-left, a short "Cara Pakai"
panel — this is what makes the file usable by someone who isn't you).
Every new template should keep the info panel and update its steps if the
template introduces a new kind of slot.

## Still open / not yet solved by any template

- Per-section PNG export (html2canvas) for social — spec §7, not built yet.
- Multi-card pagination for long-form content (`FEATURE_SPREAD`) — spec §3a.
- Whole-magazine page management shell (add/remove/reorder pages) — spec §8,
  Stage 3.
