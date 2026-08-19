# Ligar Magazine — Monthly Issue

A browser-based, template-driven tool for building a school magazine (SMAN 5 Garut) as a web-native, mobile-first publication — not a print layout. Solo-editor, no backend: drop content into predefined slots (photo/text/video) and export.

## Contents

- `docs/majalah_sekolah_layout_spec.md` — Layout system spec (draft v0.2): concept, architecture, template taxonomy, slot system, export plan, staged build roadmap.
- `prototypes/` — Visual-only mockups (round 1 of a template: matches the reference, nothing is fillable yet).
- `templates/` — Functional, standalone templates another teacher can actually open and fill in (real photo upload, editable text, video embed). `templates/_shared/README.md` documents the conventions reused across all of them.

## Format

Every page is a fixed 9:16 story card (Stories/Reels proportions) navigated via vertical scroll-snap, so each card also works standalone as an exportable social asset.

## Template registry

| File | Templates covered | Status | Notes |
|---|---|---|---|
| `prototypes/majalah_prototype_v2_profile_guru.html` | COVER, TOC, PHOTO_COLLAGE, VIDEO_FEATURE, SUBJECT_DIRECTORY, PROFILE_SPOTLIGHT | Visual mockup (round 1) | Superseded by the functional version below; kept as the round-1 reference. |
| `templates/majalah_functional_v1.html` | COVER, TOC, PHOTO_COLLAGE, VIDEO_FEATURE, SUBJECT_DIRECTORY, PROFILE_SPOTLIGHT | **Functional** (round 2) | Click-to-upload photos (auto-compressed, low-res warning), click-to-edit text (hard character limits), paste-a-link YouTube embed, and now a per-card "unduh PNG" button for social posting. Blank-vs-filled-instance is a written warning only, not enforced in code yet. |

## Status

Stage 1 of the staged build plan in the spec: template registry pattern isn't
code yet (each template is still one hand-built file), but the first batch of
sections is functionally usable end-to-end — upload → edit → embed → export —
matching the "COVER, TOC, PHOTO_COLLAGE" proof-of-concept goal, plus three
more section types along for the ride, plus the §7 per-page PNG export for
social. Not yet built: enforced blank-template protection (currently just a
warning), editable TOC/index labels, and the page-management shell (Stage 3).
