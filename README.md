# Ligar Magazine — Monthly Issue

A browser-based, template-driven tool for building a school magazine (SMAN 5 Garut) as a web-native, mobile-first publication — not a print layout. Solo-editor, no backend: drop content into predefined slots (photo/text/video) and export.

## Contents

- `docs/majalah_sekolah_layout_spec.md` — Layout system spec (draft v0.2): concept, architecture, template taxonomy, slot system, export plan, staged build roadmap.
- `prototypes/majalah_prototype_v2_profile_guru.html` — Working HTML/CSS/JS prototype: cover, table of contents, documentation collage, video feature, subject directory, and teacher profile spotlight cards, wired up with scroll-snap navigation, a floating jump menu, and scroll-reveal animation.

## Format

Every page is a fixed 9:16 story card (Stories/Reels proportions) navigated via vertical scroll-snap, so each card also works standalone as an exportable social asset.

## Status

Early prototype stage (Stage 1–2 of the staged build plan in the spec). Template registry pattern is recommended but not yet implemented in code — the current prototype hand-codes each section as a proof of concept for the visual language and interaction model.
