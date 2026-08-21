# Shared conventions for templates

> **Era note:** these conventions were written for the hand-built-template era
> (`../majalah_functional_v1.html`, one standalone file per template). The deck
> tool at the repo root (`../../index.html`) has since absorbed that template's
> six sections into its `TEMPLATES` registry — new layouts are added there as
> registry entries, not as new standalone files. The *card-design* conventions
> below (tokens, 9:16 shell, slot behaviors, stacking traps) still describe how
> every card should be built; the *file* conventions (one HTML file per
> template, Save-As discipline) are retired.

Canonical, working implementation of everything below lives in
`../../index.html` — the registry's render functions are the current form of
these patterns; `../majalah_functional_v1.html` is the historical standalone
form.

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

   Two stacking traps, both already hit once on the cover — don't re-derive
   them:

   - The `.slot.has-image::before` "Ganti Foto" scrim must be
     `pointer-events: none`. `.slot` is `position: relative` with
     `z-index: auto`, so it opens no stacking context and the scrim's
     `z-index: 3` resolves against the *card*, painting above the
     `z-index: 2` text overlay. On a hero slot that is `inset: 0` over the
     whole card, and `opacity: 0` still takes clicks — so every text field
     on the card goes dead the moment a photo lands. With pointer-events
     off, small slots are unaffected: the click falls through to the
     `.slot`, which is what carries the listener anyway.
   - Don't absolutely pin chrome to the top corners of a hero card — the
     `.top-row` eyebrow already puts text in both (the label on the left,
     edition badge or profile index on the right), and a corner button lands
     on top of it. Both `.photo-edit-btn` and the per-card `.export-btn` hit
     this. Keep them in flow instead: `.overlay-head` is a two-row grid
     holding the eyebrow across the top and the two buttons on the line
     beneath it, export left and "Ganti Foto" right, so nothing has to be
     offset by hand for whatever height the eyebrow turns out to be.

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

4. **Per-card PNG export** — one `.export-btn` per `.card`, added via JS
   (`document.querySelectorAll('.card').forEach(...)`) rather than hand-
   written per section, so a new template gets it for free just by using the
   `.card` class. It defaults to a floating top-left corner button; on a card
   with an `.overlay-head` it is inserted into that grid instead (see the
   stacking traps under photo upload) — placed after the eyebrow row so DOM
   order matches what's on screen. Lazy-loads `html2canvas` from a CDN on
   first click (not bundled inline, so the file still opens instantly before
   anyone exports anything), toggles a `body.exporting` class that hides all
   editing chrome (`display: none !important` on nav/info/photo-edit/export
   buttons, the progress gauge, low-res badges) so the captured PNG is clean,
   then downloads `majalah-<section-slug>.png`. Wrap the whole thing in
   try/catch/finally — a blocked or offline CDN load must fail with a plain
   alert and reset the button, not hang or throw. This satisfies the spec's
   "each page also doubles as a social asset" requirement (§7).

## Blank template vs. filled instance

**Solved by the deck tool.** The tool is an app; an issue is a saved document
(IndexedDB autosave + `.json` project file). Blank template and filled
instance separate by construction — PowerPoint's template-vs-deck split — so
the old mitigation (a written "duplicate the file before typing" warning) is
retired along with the standalone-file workflow it protected.

## Floating chrome

Two fixed circular buttons, bottom corners: nav toggle (bottom-right, jump
menu built from `[data-nav]` sections + `IntersectionObserver` for active-
section highlighting) and info toggle (bottom-left, a short "Cara Pakai"
panel — this is what makes the file usable by someone who isn't you).
Every new template should keep the info panel and update its steps if the
template introduces a new kind of slot.

## Still open

- Multi-card pagination for long-form content (`FEATURE_SPREAD` interviews
  longer than one card) — spec §3a.
- Aspect-ratio pre-cropping at upload time (spec §5 mentions the Jadwal
  Seragam pre-crop discipline; the tool currently uses CSS `cover` cropping,
  which is fine on screen but crops without asking).
- HEIC uploads (spec §5 mentions heic2any) — currently whatever
  `<input accept="image/*">` + canvas can decode.
