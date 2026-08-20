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
   - Don't absolutely pin `.photo-edit-btn` to the top-right corner — the
     `.top-row` eyebrow already puts text there (edition badge, profile
     index) and the button covers it. Keep it in flow instead: wrap the
     eyebrow row and the button in `.overlay-head`
     (`flex-direction: column; align-items: flex-end`) so the button stacks
     underneath, right-aligned, with no offsets to tune per card.

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
   `.card` class. Lazy-loads `html2canvas` from a CDN on first click (not
   bundled inline, so the file still opens instantly before anyone exports
   anything), toggles a `body.exporting` class that hides all editing chrome
   (`display: none !important` on nav/info/photo-edit/export buttons, the
   progress gauge, low-res badges) so the captured PNG is clean, then
   downloads `majalah-<section-slug>.png`. Wrap the whole thing in
   try/catch/finally — a blocked or offline CDN load must fail with a plain
   alert and reset the button, not hang or throw. This satisfies the spec's
   "each page also doubles as a social asset" requirement (§7).

## Blank template vs. filled instance

There's no tooling yet to protect the reusable blank template from being
overwritten by whoever fills it in — the current mitigation is a written
warning in both the file's header comment and the in-page "Cara Pakai"
panel: duplicate the file (Save As) before typing anything in. If this
becomes a real problem in practice (someone forgets, loses the blank), the
next step would be a "Reset to blank" control rather than relying on
discipline — not built yet, flagged here so it isn't lost.

## Floating chrome

Two fixed circular buttons, bottom corners: nav toggle (bottom-right, jump
menu built from `[data-nav]` sections + `IntersectionObserver` for active-
section highlighting) and info toggle (bottom-left, a short "Cara Pakai"
panel — this is what makes the file usable by someone who isn't you).
Every new template should keep the info panel and update its steps if the
template introduces a new kind of slot.

## Still open / not yet solved by any template

- Blank-vs-filled-instance protection beyond a written warning (see above).
- The per-card `.export-btn` (top-left, `z-index: 20`) overlaps the start of
  the `.top-row` eyebrow text on the two hero cards. Cosmetic only — the text
  it covers is static, and the button is hidden during export — but it's the
  same corner collision as the `.photo-edit-btn` one above, not yet resolved.
- TOC section labels and the "01 / 12" profile index counter are still
  static, not editable.
- Multi-card pagination for long-form content (`FEATURE_SPREAD`) — spec §3a.
- Whole-magazine page management shell (add/remove/reorder pages) — spec §8,
  Stage 3.
