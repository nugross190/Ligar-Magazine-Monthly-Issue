# Majalah Sekolah — Layout System Spec (Draft v0.3)
**SMAN 5 Garut**

Status: draft v0.3 — solo-editor, web-native (not print), and PowerPoint-style authoring (§1, §6a) confirmed. Push back on the rest.
Precedent: this extends the **Jadwal Seragam Banner** pattern (fixed slots → upload/pre-crop → export) from one bento page to a growing library of page layouts.

---

## 1. Concept

A browser-based tool with a growing library of page **templates**. Each template has predefined **slots** (photo, text, or video). You drop content into slots — no layout decisions, no design skill required per page. Output is a web-native, interactive magazine (scrollable sections, not simulated print pages) that also doubles as a source of individually-exportable visual assets for social media.

**The authoring model is PowerPoint's.** An issue starts as a single `COVER`
page and nothing else. From there you add pages one at a time, each by picking a
layout from the library and filling its slots — the same loop as "new slide →
pick layout." You never draw a page; you choose one and fill it. Pages can be
removed and reordered as freely as slides.

That analogy is also the argument for keeping §4 a fixed, curated list rather
than an open canvas: the constraint is the product. PowerPoint's layout picker
offers a dozen arrangements and no way to invent a thirteenth mid-deck, and that
is precisely why nobody has to be a designer to use it.

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

Note what the deck model does to that sentence: the *tool* stays one file, but an
*issue* stops being one. Under §6a an issue is saved state — a page list the tool
loads — not a copy of the tool with content typed into it. One app, many issues,
the same split PowerPoint has between the program and a .pptx.

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

### 6a. Authoring loop

Building an issue is one loop, repeated:

1. A new issue opens with a single `COVER` page and nothing else.
2. **Add page** → pick a layout from the §4 library → the page is appended with
   its slots empty.
3. Fill the slots — upload a photo, type into a text slot, paste a video link.
4. Reorder or delete pages at any point.
5. Repeat 2–4 until the issue is done, then export (§7).

`TOC` is the one page that cannot be authored like the others: its entries are a
function of the page list, so it has to regenerate whenever pages are added,
removed, or reordered. Treat it as a **derived** page rather than a filled one —
the editor supplies section titles, the tool supplies order and numbering. A
`TOC` that has to be hand-corrected after every page change would undo the point
of the whole loop.

### 6b. Deck state and persistence — required, mechanism open

The loop above only works if a part-finished issue survives closing the tab. A
magazine is not filled in one sitting, and today nothing is saved at all: every
upload and edit lives in the live DOM, and a reload loses all of it. **In the
deck model, persistence stops being a polish item and becomes a precondition** —
"add pages as you like" is not a usable offer if the deck evaporates.

The state itself is small and already well-shaped: the page list of
`{ templateId, slotData }` from §3 *is* the saved document. The photos are the
awkward part — they are held as base64 data URLs, so a dozen of them comfortably
exceed `localStorage`'s ~5MB quota. Any mechanism has to account for that.

Recommended: autosave the page list to **IndexedDB** (no practical quota problem
at this scale) on every slot edit, restoring on open. The alternative is explicit
save/load of a `.json` project file — portable between machines and backup-able,
at the cost of more UI and no protection against forgetting to save. These are
not exclusive, and autosave plus import/export of the same JSON is a reasonable
end state. Recorded as an open decision in §9 rather than settled here.

**Side effect worth having:** this resolves the blank-template-vs-filled-instance
problem that `templates/_shared/README.md` currently mitigates with a written
warning ("duplicate the file before typing anything"). Once the tool is an app
and an issue is a saved document, the two separate by construction — the same
way PowerPoint distinguishes a .potx from the deck you built with it. The
Save-As discipline stops being load-bearing.

---

## 7. Export / publish

No print pipeline — confirmed web-only. Two separate export needs instead:

- **Whole magazine** → one self-contained HTML file (CSS/JS inlined, video as embeds not base64) you can host anywhere — school site, Google Sites, GitHub Pages — or just share directly.
- **Individual assets for social** → since you're generating these as head of social media, each page/section should also be independently exportable as a PNG via html2canvas, scoped to just that section's DOM node. A "Teacher Profile" page then doubles as an Instagram post without rebuilding it separately.
- **Navigation model (settled — see §9)** — continuous scroll through sections won over click/swipe flipbook paging: simpler, more native to the web, and it doesn't fight with video embeds the way a flip transition can. Flipbook would have been closer to the print-magazine feel of the references, at more JS. Shipped as scroll-snap plus a floating jump menu.

---

## 8. Staged build plan

**Reordered for the deck model (§1, §6a).** The page shell used to sit at Stage 3,
behind the template library. That ordering does not survive the PowerPoint
framing: "start with a cover and add pages as you like" *is* the shell, so it
cannot be the last thing built. It now merges into Stage 1, which is where it
belonged anyway — the template registry and the page list are one architecture,
and building the registry without the list just defers the same work.

**The build so far has not followed any of this.** Stage 4 shipped first, while
Stage 1 is still not started. Each stage records what is actually done, so the
plan reads against the repo rather than against intent. Template IDs match the
§4 taxonomy — an earlier draft referred to a `PROFILE_GRID` that §4 has since
split into `SUBJECT_DIRECTORY` + `PROFILE_SPOTLIGHT`.

1. **Stage 1 — Deck core.** The stage that makes the tool PowerPoint-shaped, and
   now the critical path. Four pieces, one architecture:
   - Template registry + slot config (`{ id, type, aspectRatio?, maxChars?, required }`
     per §5), with the six shipped templates converted into registry entries.
   - Page list state — an issue is `[{ templateId, slotData }]`, opening with one
     `COVER`.
   - Page shell — add (via layout picker), remove, reorder; `TOC` derived from
     the list per §6a.
   - Persistence, mechanism per §6b once decided.

   → *Not started.* Formerly Stage 1 (registry) plus Stage 3 (shell), split
   across the plan; merged here because neither half is useful alone.

2. **Stage 2 — Fill out the library.** The five templates not yet built:
   `GROUP_PHOTO`, `FEATURE_SPREAD`, `EDITOR_NOTE`, `QUOTE_INTERLUDE`,
   `BACK_COVER`. Treat this as exploratory rather than a fixed checklist — add
   layouts as you find you want them, going back and forth on each one rather
   than speccing all of them up front. Cheap once Stage 1 lands: each new layout
   is a registry entry, not a hand-built file.

   → *6 of 11 templates built* — `COVER`, `TOC`, `PHOTO_COLLAGE`,
   `VIDEO_FEATURE`, `SUBJECT_DIRECTORY`, `PROFILE_SPOTLIGHT`, all hand-written
   rather than registry-driven.

3. **Stage 3 — Whole-magazine export.** One self-contained HTML file per §7,
   generated from the page list. Independent of the shell, so it can follow the
   library.

   → *Not started.* Per-card PNG export (also §7) already ships; this is the
   other half.

4. **Stage 4 — Polish pass.** Scroll navigation/anchors, per-section PNG export
   for social, mobile responsiveness (assume most readers are on a phone).

   → *Done, ahead of everything else.* Scroll-snap navigation with a floating
   jump menu, per-card PNG export via html2canvas, and 9:16 mobile-first cards
   all ship today.

5. **Stage 5 (ongoing)** — Keep expanding the template library as new content
   types come up — "as many layouts as possible" is the actual goal here, not a
   fixed set.

   → *Ongoing*, and the stage the deck model is ultimately in service of.

**Reading the order.** Building the polish first was cheap while every page was
hand-written, but it front-loaded work that Stage 1 will redo: each shipped
template becomes a registry entry, and the per-card behaviors currently re-wired
by hand in every file become slot config. The longer Stage 1 waits, the more
hand-built templates there are to convert — which is the concrete argument for
doing it next rather than adding a seventh template first.

---

## 9. Open decisions

- [x] ~~Workflow~~ → solo editor, confirmed
- [x] ~~Print vs web~~ → web-only, confirmed
- [x] ~~Navigation~~ → continuous scroll + floating icon that expands to a jump menu, confirmed
- [x] ~~Video hosting~~ → YouTube (embed mode), confirmed
- [x] ~~Authoring model~~ → PowerPoint-style: an issue opens on a `COVER`, pages are added by picking a layout, removed and reordered freely, confirmed (§1, §6a)
- [ ] **Deck persistence mechanism** — IndexedDB autosave, an explicit `.json` project file, or both. Required either way once the deck model is real; §6b carries the recommendation and the reason `localStorage` is not a candidate
- [ ] How many layouts before you start actually publishing — open-ended, treating this as ongoing exploration rather than a fixed target (see the Stage 2 note in §8)
