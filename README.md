# Ligar Magazine — Monthly Issue

A browser-based, template-driven tool for building a school magazine (SMAN 5
Garut) as a web-native, mobile-first publication — not a print layout.
Solo-editor, no backend, no install.

## Two pages, two audiences

- **`index.html` — the public archive.** What's live at the hosted URL
  (GitHub Pages: `https://nugross190.github.io/Ligar-Magazine-Monthly-Issue/`).
  Lists published issues, newest first — read-only, for students/parents/
  anyone with the link. Discovers issues automatically from the `issues/`
  folder; nothing to hand-maintain when a new one goes up.
- **`editor.html` — the deck tool.** Where issues get built. Open it directly
  (double-click the file, or its own hosted URL) to start composing.

**Just want to build an issue? Open `editor.html`. That's the whole setup —
no install, works offline.**

## How the editor works

Works like PowerPoint: a new issue starts as a single cover page, and you grow
it by pressing **⊞ → + Tambah Halaman** and picking a layout from the library
(11 layouts). Fill each page in place — click a dashed area to upload a photo
(auto-compressed, low-res warning), click text to type (hard character
limits), paste a YouTube link into a video slot. Pages can be renamed,
reordered, and deleted (with undo); the **Daftar Isi page builds itself** from
the page list.

Everything **autosaves in the browser** (IndexedDB). For backup or moving
between computers use *Simpan Berkas Proyek* (a `.json` file) and *Buka
Proyek…* — which also accepts a previously exported magazine HTML, since the
project data is embedded in it.

Two exports, matching the spec's §7:

- **Unduh Majalah** — the whole issue as one self-contained HTML file
  (scroll-snap reader, jump menu, video embeds) named `YYYY-MM-slug.html` —
  see "Publishing an issue" below.
- **Per-card PNG** (download icon on each card) — each 9:16 page doubles as a
  social media asset. Needs internet on first use (html2canvas from CDN).

Video slots show a thumbnail "lite card" that links out to YouTube. Opened
via `file://` that's the ceiling — YouTube refuses embeds with no referrer
(player Error 153). Served over http(s) from `issues/`, the exported reader
upgrades the same card to a real inline player automatically.

## Publishing an issue

1. Build the issue in `editor.html`, click **Unduh Majalah**.
2. Drop the downloaded file into `issues/` (the filename already matches the
   convention — no renaming needed), commit, push to `claude/main`.
3. Done. The archive page (`index.html`) picks it up on the next visit —
   nothing else to edit. See `issues/README.md` for the naming convention and
   what happens to a file that doesn't match it.

## Contents

- `index.html` — the public archive (reader-facing).
- `editor.html` — the deck tool (editor + exporter). Single file, vanilla JS,
  template registry inside.
- `issues/` — published issues live here as exported HTML files.
  `issues/README.md` documents the naming convention.
- `docs/majalah_sekolah_layout_spec.md` — Layout system spec (draft v0.4):
  concept, PowerPoint authoring model, template taxonomy, slot system,
  persistence, public archive, staged build plan (all stages shipped; Stage 5
  ongoing).
- `templates/` — the hand-built-era functional template
  (`majalah_functional_v1.html`), kept as the round-2 reference the app grew
  out of. `templates/_shared/README.md` documents the card conventions, which
  still apply to new registry entries.
- `prototypes/` — round-1 visual-only mockup, kept for history.

## Format

Every page is a fixed 9:16 story card (Stories/Reels proportions) navigated via
vertical scroll-snap, so each card also works standalone as an exportable
social asset.

## Template library (11)

`COVER` · `TOC` (derived) · `EDITOR_NOTE` · `PHOTO_COLLAGE` · `FEATURE_SPREAD`
· `VIDEO_FEATURE` · `SUBJECT_DIRECTORY` · `PROFILE_SPOTLIGHT` (auto-numbered)
· `GROUP_PHOTO` · `QUOTE_INTERLUDE` · `BACK_COVER`

Adding a layout = one entry in the `TEMPLATES` registry in `editor.html`
(slot definitions + a render function that serves both the editor and the
exported reader + a picker thumbnail).

## Status

All build stages of the spec are shipped: the template registry and page shell
(Stage 1), the full 11-template library (Stage 2), whole-magazine HTML export
(Stage 3), and the polish pass (Stage 4, which historically shipped first).
On top of that, a public archive (spec §7a) turns a published issue into a
URL a reader can just open. Stage 5 — growing the layout library — is the
ongoing work.

The old warnings no longer apply: work persists across reloads (autosave), and
the blank-template-vs-filled-instance problem is gone by construction — the
tool is the app, an issue is a saved document, the same split as PowerPoint's
template vs deck.
