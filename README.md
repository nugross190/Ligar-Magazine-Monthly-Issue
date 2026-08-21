# Ligar Magazine — Monthly Issue

A browser-based, template-driven tool for building a school magazine (SMAN 5
Garut) as a web-native, mobile-first publication — not a print layout.
Solo-editor, no backend, no install.

## How to use

**Open `index.html` in a browser. That's the whole setup.**

The tool works like PowerPoint: a new issue starts as a single cover page, and
you grow it by pressing **⊞ → + Tambah Halaman** and picking a layout from the
library (11 layouts). Fill each page in place — click a dashed area to upload a
photo (auto-compressed, low-res warning), click text to type (hard character
limits), paste a YouTube link into a video slot. Pages can be renamed,
reordered, and deleted (with undo); the **Daftar Isi page builds itself** from
the page list.

Everything **autosaves in the browser** (IndexedDB). For backup or moving
between computers use *Simpan Berkas Proyek* (a `.json` file) and *Buka
Proyek…* — which also accepts a previously exported magazine HTML, since the
project data is embedded in it.

Two exports, matching the spec's §7:

- **Unduh Majalah** — the whole issue as one self-contained HTML file
  (scroll-snap reader, jump menu, video embeds) you can host anywhere or share
  directly.
- **Per-card PNG** (download icon on each card) — each 9:16 page doubles as a
  social media asset. Needs internet on first use (html2canvas from CDN).

## Contents

- `index.html` — **the deck tool** (editor + exporter). Single file, vanilla
  JS, template registry inside.
- `docs/majalah_sekolah_layout_spec.md` — Layout system spec (draft v0.4):
  concept, PowerPoint authoring model, template taxonomy, slot system,
  persistence, staged build plan (all stages shipped; Stage 5 ongoing).
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

Adding a layout = one entry in the `TEMPLATES` registry in `index.html`
(slot definitions + a render function that serves both the editor and the
exported reader + a picker thumbnail).

## Status

All build stages of the spec are shipped: the template registry and page shell
(Stage 1), the full 11-template library (Stage 2), whole-magazine HTML export
(Stage 3), and the polish pass (Stage 4, which historically shipped first).
Stage 5 — growing the layout library — is the ongoing work.

The old warnings no longer apply: work persists across reloads (autosave), and
the blank-template-vs-filled-instance problem is gone by construction — the
tool is the app, an issue is a saved document, the same split as PowerPoint's
template vs deck.
