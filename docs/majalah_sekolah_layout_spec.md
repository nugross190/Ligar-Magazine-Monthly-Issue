# Majalah Sekolah — Layout System Spec (Draft v0.1)
**SMAN 5 Garut**

Status: draft v0.2 — solo-editor, web-native (not print) confirmed. Push back on the rest.
Precedent: this extends the **Jadwal Seragam Banner** pattern (fixed slots → upload/pre-crop → export) from one bento page to a growing library of page layouts.

---

## 1. Concept

A browser-based tool with a growing library of page **templates**. Each template has predefined **slots** (photo, text, or video). You drop content into slots — no layout decisions, no design skill required per page. Output is a web-native, interactive magazine (scrollable sections, not simulated print pages) that also doubles as a source of individually-exportable visual assets for social media.

This is not a CMS and not a general page builder. It's deliberately narrow: a growing library of reusable layouts, applied page by page, solo editor.

---

## 2. What each reference actually contributes

| Reference | What to take | What to ignore |
|---|---|---|
| Jilster | Page-grid workflow, per-page approval status (✓ checkmarks), "select a page to start" pattern | The editor chrome itself — you're not building a general-purpose builder |
| Swiss Style Rating App | Bold sans-serif headline type, tight grid, black/white/one-accent restraint | The app-specific UI (phone frames, onboarding flow) — irrelevant here |
| Digital Explorers (kids mag) | Color-blocked TOC grid, bento-tile photo-dump pages, collage cover treatment | The illustration style — too juvenile for a Phase E/SMA context |
| School Yearbook (navy) | Formal photo-grid for teacher/student directories, consistent header band | The exact navy palette — pick your own school colors |

---

## 3. Architecture recommendation

Jadwal Seragam was 1 page, 7 tiles — vanilla HTML/CSS/JS was fine as one file. A magazine with 8–20 pages across ~11 reusable template *types* will get unwieldy fast if every page is hand-coded.

**Recommendation:** stay vanilla JS (no React/build step — matches your current stack and skill trajectory), but structure it as a **template registry**:

```js
templates = {
  COVER: { render: fn, slots: [...] },
  TOC: { render: fn, slots: [...] },
  PROFILE_SPOTLIGHT: { render: fn, slots: [...] },
  ...
}
```

Each page in the magazine is just `{ templateId, slotData }`. One render function per template type, reused across every page that uses it. This is the Excel-analogy version of "don't hardcode 20 sheets — build one template sheet and reference it."

Since it's solo-editor and web-only, there's no need for a backend — this stays a single self-contained HTML/JS tool, same shape as Jadwal Seragam, just with a bigger template registry and a page-list state instead of one fixed layout.

---

## 3a. Format — 9:16 story-card (confirmed)

Mobile-first, and each page also needs to work as a standalone social asset — so every page/section is a fixed 9:16 card (Stories/Reels proportions), not a free-flowing full-width page. On mobile it fills the screen; on desktop it centers as a tall column with the graph-paper background showing on either side, same as viewing a Story in a browser. Navigation between cards is vertical scroll-snap, one card at a time, jumpable via the floating menu.

**Consequence for future templates**: a 9:16 card holds noticeably less than a full page. Longer content — `FEATURE_SPREAD` interviews, `PROFILE_SPOTLIGHT` bios with real bio text — will likely need to paginate across 2+ cards rather than fit on one, the way an Instagram carousel splits a long caption across slides. Worth designing for that in Stage 2 rather than discovering it mid-build. Partly resolved already: see the browse → detail pair at the end of §4.

## 4. Page template taxonomy

| ID | Name | Use case | Inspired by | Image slots | Text slots |
|---|---|---|---|---|---|
| `COVER` | Cover | Front cover | Digital Explorers collage cover | 1 full-bleed hero (portrait) | Title, issue/date, tagline |
| `TOC` | Contents | Table of contents | Digital Explorers color-block grid | up to 6 thumbnails | Section titles + page numbers |
| `EDITOR_NOTE` | Editor's Note | Intro letter | Jilster sample spread | 0–1 photo | Body ~150–250 words |
| `SUBJECT_DIRECTORY` | Subject/Department Overview | Browse teachers within one mata pelajaran | Streaming-app hero + episode grid pattern, restructured for photo thumbnails | Grid of 4 small portraits | Subject name, curator credit, 4× name + role |
| `PROFILE_SPOTLIGHT` | Teacher/Student Spotlight | One person per card — name, subject, short quote | Yearbook grid, reworked | 1 full-bleed portrait | Name, subject/role, short quote |
| `GROUP_PHOTO` | Class/Graduation Grid | Grad rows, class photos | Yearbook grad page | N small headshots | Names |
| `FEATURE_SPREAD` | Event/Article Feature | Trips, activities, interviews | Swiss-style text page or Jilster "students" spread | 1–3 photos | Headline, body, caption |
| `PHOTO_COLLAGE` | Documentation Dump | OSIS/event documentation | Digital Explorers bento tiles | 4–6 photos, mixed aspect | Short caption per photo |
| `QUOTE_INTERLUDE` | Pull-quote | Section break | Swiss style | 0 | 1 quote + attribution |
| `BACK_COVER` | Closing | Back cover | — | 0–1 | Closing message, credits |
| `VIDEO_FEATURE` | Video Feature | Recap reels, embedded event footage | — (new, web-native) | 1 video slot + optional thumbnail photo | Headline, caption |

Any photo slot in `FEATURE_SPREAD` or `PHOTO_COLLAGE` can also be swapped to a video slot — see Section 5. Eleven template types to start; the library is meant to keep growing.

**`SUBJECT_DIRECTORY` + `PROFILE_SPOTLIGHT` form a browse → detail pair**: the directory card groups teachers by mata pelajaran and links out to individual spotlight cards, rather than one flat staff directory. This is the resolved answer to the pagination note in 3a — browse in small batches, go deep one person at a time.

---

## 5. Slot system spec

Each slot is defined as:

```js
{ id, type: "image" | "text", aspectRatio?, maxChars?, required }
```

- **Image slots**: lock aspect ratio at upload time (same pre-crop discipline as Jadwal Seragam's 4:6/1:1 tiles). Accept jpg/png/heic (heic2any, same CDN dependency you already planned). Auto-compress before canvas render so export doesn't choke on 12MP phone photos.
- **Text slots**: enforce `maxChars` in the input UI itself, not just visually — so nobody's caption silently overflows the slot.
- **Video slots**: two modes, since this is a solo-editor, no-backend tool:
  - **Embed mode** — paste a YouTube/Instagram/Drive share link, rendered via `<iframe>`. No file-size concerns, works reliably once published.
  - **File mode** — reference a video hosted somewhere you control. Don't embed raw video as base64 inside the exported HTML — a couple of 30-second clips at base64 turns a "single file" export into hundreds of MB and it won't load reliably in a browser.
  Default to embed mode; only use file mode if you already have hosting sorted.
- Flag low-resolution uploads (e.g. under ~1000px) at upload time, not after export — a phone photo that looks fine on screen can be too soft printed on an A4 page.

---

## 6. Workflow — confirmed

**Solo editor.** You collect and place everything yourself. No backend, no multi-user access, no HADIR integration needed for this. Keeps the whole tool a single client-side app — same shape as Jadwal Seragam, just with more template types and a page list instead of one fixed layout.

---

## 7. Export / publish

No print pipeline — confirmed web-only. Two separate export needs instead:

- **Whole magazine** → one self-contained HTML file (CSS/JS inlined, video as embeds not base64) you can host anywhere — school site, Google Sites, GitHub Pages — or just share directly.
- **Individual assets for social** → since you're generating these as head of social media, each page/section should also be independently exportable as a PNG via html2canvas, scoped to just that section's DOM node. A "Teacher Profile" page then doubles as an Instagram post without rebuilding it separately.
- **Navigation model (open question)** — continuous scroll through sections, or click/swipe page-to-page like a flipbook? Scroll is simpler, more native to the web, and doesn't fight with video embeds the way a flip transition can. Flipbook is closer to the print-magazine feel of your references but costs more JS. Recommend scroll unless the flipbook feel specifically matters to you.

---

## 8. Staged build plan

**The build has not followed this order.** Stage 1's template registry is still
not code, while templates from Stage 2 and the whole of Stage 4 have shipped.
Each stage below records what is actually done, so the plan can be read against
the repo rather than against intent. Template IDs here match the §4 taxonomy —
an earlier draft of this list referred to a `PROFILE_GRID` that §4 has since
split into `SUBJECT_DIRECTORY` + `PROFILE_SPOTLIGHT`.

1. **Stage 1** — Template registry + slot config for 3 templates (`COVER`, `TOC`,
   `PHOTO_COLLAGE`). Smallest set that proves upload → crop → render →
   per-section PNG export end to end, built directly off the Jadwal Seragam
   codebase.
   → *Partial.* All three templates exist and work end to end, but as hand-built
   sections in one file. The registry and slot config they were meant to prove
   are not written yet, so this stage's actual deliverable is still open.

2. **Stage 2** — Add the remaining templates (`SUBJECT_DIRECTORY`,
   `PROFILE_SPOTLIGHT`, `GROUP_PHOTO`, `FEATURE_SPREAD`, `EDITOR_NOTE`,
   `QUOTE_INTERLUDE`, `BACK_COVER`, `VIDEO_FEATURE`), including the video embed
   slot. Treat this as exploratory rather than a fixed checklist — add layouts as
   you find you want them, going back and forth on each one rather than speccing
   all of them up front.
   → *3 of 8 done.* `SUBJECT_DIRECTORY`, `PROFILE_SPOTLIGHT` and `VIDEO_FEATURE`
   are built (the video embed slot with them). Still to build: `GROUP_PHOTO`,
   `FEATURE_SPREAD`, `EDITOR_NOTE`, `QUOTE_INTERLUDE`, `BACK_COVER`.

3. **Stage 3** — Page management shell: add/remove/reorder pages, assign a
   template per page, whole-magazine HTML export.
   → *Not started, and the least specified stage in this document.* One clause
   is not a design. Before building it, this stage needs: where page state
   lives and how it survives a reload (the tool has no persistence today), what
   happens to uploaded photos and typed text when a page is deleted, and how the
   `TOC` card stays in sync as pages are added, removed, or reordered.

4. **Stage 4** — Polish pass: scroll navigation/anchors, per-section PNG export
   for social, mobile responsiveness (assume most readers are on a phone).
   → *Done, ahead of Stages 1 and 3.* Scroll-snap navigation with a floating
   jump menu, per-card PNG export via html2canvas, and 9:16 mobile-first cards
   are all shipped.

5. **Stage 5 (ongoing)** — Keep expanding the template library as new content
   types come up — "as many layouts as possible" is the actual goal here, not a
   fixed set.
   → *Ongoing.* 6 of the 11 templates in §4 are built.

**Reading the order.** Building the polish before the registry was cheap while
every page was hand-written, but it front-loaded the work that has to be redone
once Stage 1 lands: each shipped template becomes a registry entry, and the
per-card behaviors currently re-wired by hand per file become slot config. The
longer Stage 1 waits, the more hand-built templates there are to convert.

---

## 9. Open decisions

- [x] ~~Workflow~~ → solo editor, confirmed
- [x] ~~Print vs web~~ → web-only, confirmed
- [x] ~~Navigation~~ → continuous scroll + floating icon that expands to a jump menu, confirmed
- [x] ~~Video hosting~~ → YouTube (embed mode), confirmed
- [ ] How many layouts before you start actually publishing — open-ended, treating this as ongoing exploration rather than a fixed target (see Stage 2 note below)
