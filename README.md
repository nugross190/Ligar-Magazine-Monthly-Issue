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

Six of the spec's eleven templates are built and functionally usable end to end
— upload → edit → embed → export — plus the §7 per-card PNG export for social.

Against the staged plan in §8, though, the build has run out of order: what
ships is **Stage 4** (polish — scroll navigation, PNG export, mobile-first
cards) plus 6 templates' worth of **Stage 2**. **Stage 1 has not started.**
Each template is still a hand-built section in one file, so the template
registry and slot config that Stage 1 exists to deliver are not written, and
neither is the page shell that makes the tool PowerPoint-shaped — an issue
opening on a cover and growing by "add page → pick layout" (§1, §6a). That
shell is now the critical path, and every hand-built template added before it
is one more to convert afterwards.

Also not built: any persistence at all — a reload loses every upload and edit,
which §6b marks as a precondition of the deck model rather than a polish item.
Enforced blank-template protection is still a written warning only, and the TOC
and profile-index labels are still static rather than derived from a page list.
