# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

---

## Project Status

**Complete and deployed to GitHub.** The site is a fully functional single-property luxury real estate listing page. It is ready to be dropped onto Netlify for a live public URL. The Formspree contact form is wired up and tested — it works once the Formspree account email is verified.

**GitHub:** https://github.com/everettmiller05-a11y/5100-SW-86th-Street  
**Local preview:** `http://localhost:7845`

---

## File Structure

```
ponce-davis-estate/
├── index.html                                          # Entire site — HTML + CSS + JS, ~2273 lines
├── wwfcmsprodimagesNational_Park_of_.width-800.format-webp copy.jpg  # Active background (forest corridor)
├── imagereader copy.jpg                                # Alternate background (inactive)
├── CLAUDE.md                                           # This file
└── .claude/launch.json                                 # Claude Code session config
```

No `node_modules`, no `package.json`, no build output. Everything is in `index.html`.

---

## Stack & Tools

- **Vanilla HTML/CSS/JS** — no React, no Vue, no npm, no bundler
- **Fonts:** Google Fonts — `Cormorant Garamond` (headings/display) + `Inter` (body/UI)
- **Photos:** Sotheby's CDN (`img-v2.gtsstatic.net`) — all 40 photos pulled from listing `1194i215`
- **Contact form:** Formspree (`mgobzene`) — JSON POST via native `fetch`
- **Hosting:** GitHub → Netlify Drop
- **Git auth:** `/opt/homebrew/bin/gh` (GitHub CLI, not on default PATH)
- **Dev server:** `python3 -m http.server 7845`

---

## Design System

### Color Palette (CSS variables)
```css
--navy:       #0D2B4E   /* primary dark — nav, stats bar */
--navy-deep:  #091e38   /* darker navy */
--gold:       #B8966A   /* accent — dividers, highlights */
--gold-light: #D4B48A   /* lighter gold — eyebrow text */
--cream:      #D8CEBC   /* page background, section fills */
--cream-dark: #C9BDA8   /* darker cream */
--stone:      #9E8E7A   /* muted text accent */
--text:       #1C1C1C   /* body text */
--text-muted: #5A5248   /* secondary text */
```

### Typography
- **Display/headings:** `Cormorant Garamond` — used for hero headline, section titles, room numbers
- **UI/body:** `Inter` weight 300 — used for everything else
- Heading pattern: `section-eyebrow` (small caps, letter-spacing) + `section-title` (large serif) + `divider` (thin gold line)

### Design Decisions Made
- Title: **"Where the Jungle Meets Home"**
- Background: forest corridor photo (not the alternate lianas photo)
- Vines: dark forest green (`#0a1f06`–`#112e0b`), 90 strands, drop from top with spring bounce
- Room panels: numbered 1–6 in circled Cormorant Garamond numerals (not SVG icons)
- Interior bento grid: `7fr 3fr 4fr 4fr` columns, rows `420px 340px`
- Bedroom accordion: 6 panels only (bathroom panel was removed)
- `html { scroll-behavior: auto }` — smooth scroll applied per-click via JS only

---

## What's Built

### All Sections (top to bottom)
1. **Fixed nav** — transparent → navy on scroll, hamburger on mobile, "Schedule Showing" CTA
2. **Vine overlay** — 90 animated SVG vines, fixed behind hero, fade on scroll
3. **Smooth scroll hero** — clip-path polygon expands from 25%/75% to full bleed as user scrolls; title "Where the Jungle Meets Home"; two CTAs
4. **Stats bar** — 6 bed / 5 bath / 6,580 sq ft / 29,403 SF lot / Dual heated pools / Ponce Davis
5. **Overview** — property description + feature highlights
6. **Pool & Outdoor** — hero pool image + 4-photo gallery (3 pool shots + 1 exterior grounds)
7. **Interior Tour** — 6-photo bento grid (foyer, living room, kitchen, breakfast nook, sunroom, balcony terrace)
8. **Feature Strip** — property feature columns (dark navy band)
9. **Bedrooms & Baths** — interactive accordion with 6 panels, each showing a different bedroom photo, numbered 1–6
10. **Full Gallery** — all 40 photos in a responsive grid, opens lightbox on click
11. **Neighborhood** — aerial photos + Ponce Davis location copy
12. **Contact form** — Formspree-connected form with first/last name, email, phone, message
13. **Footer** — agent credit, MLS number, disclaimer

### Interactive Features
- Lightbox (arrow key navigation, click-outside-to-close)
- Bedroom accordion (click to expand panel, CSS flex transition)
- Nav scroll state (class toggle at 60px scroll)
- Mobile hamburger menu
- Vine spring animation (JS rAF loop, not CSS)
- Smooth scroll on nav link clicks (JS `scrollIntoView`)

---

## Key Technical Decisions & Why

| Decision | Reason |
|---|---|
| `<div id="bgLayer" style="position:fixed">` instead of `background-attachment:fixed` | `background-attachment:fixed` breaks/disappears at non-100% browser zoom |
| CSS Individual Transform Properties (`scale`, `rotate`) were abandoned | Caused flicker when two animations ran on same element — replaced with JS rAF loop setting `style.transform` directly |
| `history.scrollRestoration = 'manual'` + scroll reset on `window.load` | Chrome was restoring scroll position from previous session, jumping to `#gallery` on open |
| `html { scroll-behavior: auto }` | CSS smooth scroll fired during the page-load hash reset, causing a visible animated jump |
| Formspree via `JSON.stringify` + `Content-Type: application/json` | More reliable cross-origin than `FormData`; avoids multipart boundary issues |
| Seeded LCG RNG (seed=137) for vine generation | Makes vines reproducible — same layout every page load |
| No CSS keyframe animations on vines | CSS `scale` + `rotate` on SVG `<g>` elements conflicted, causing glitch on drop |

---

## Photo System

**Source:** All 40 photos from Sotheby's listing `1194i215`  
**CDN pattern:**
```
https://img-v2.gtsstatic.net/reno/imagereader.aspx?url=https%3A%2F%2Fm.sothebysrealty.com%2F1194i215%2F{id}&w={width}&q=85&option=N&permitphotoenlargement=false
```

**Indexing:** `PHOTOS` array is 0-based. The gallery UI shows photos 1–40 (1-based).  
**Rule:** Gallery photo N = `PHOTOS[N-1]` = lightbox index `N-1`.

**Confirmed correct photo→room assignments (gallery numbers, 1-based):**
```
1   Front Facade — Twilight (hero)
3   Pool at Twilight (pool section hero)
7   Grand Entry Foyer (interior grid)
9   Formal Living Room (interior grid)
14  Chef's Kitchen (interior grid)
12  Breakfast Nook (interior grid)
28  Sunroom / Florida Room (interior grid)
22  Upper Balcony Terrace (interior grid)
15  Bedroom 1 (accordion)
16  Bedroom 2 (accordion)
18  Bedroom 3 (accordion)
23  Bedroom 4 (accordion)
25  Bedroom 5 (accordion)
26  Bedroom 6 (accordion)
32  Side Exterior — grounds (outdoor gallery)
33  Long Pool — Daytime (outdoor gallery)
30  Rear Exterior (outdoor gallery)
37  Twilight Pool — Dramatic (outdoor gallery)
```

**Note:** Many labels in the `PHOTOS` array are wrong — the IDs were assigned before the images were verified. Trust the gallery numbers above, not the array labels.

---

## Architecture (Key Systems)

### Z-index Stack
```
#bgLayer          -1    fixed background div
.vine-overlay      2    SVG vine canvas, fixed, pointer-events:none
.ssh-sticky        3    hero photo, position:sticky
nav              100    fixed navbar
.lightbox        200    fullscreen photo overlay
```

### Vine Animation (buildVines IIFE, ~line 1973)
- 90 strands, seeded RNG (seed=137), viewBox `0 0 1280 900`
- Each strand: cubic bezier path + dense leaf pairs every 10–16px + 1–2 sub-branches
- Spring: `STIFFNESS=160`, `DAMPING=14` — underdamped for natural bounce
- Sway: `sin()` oscillation applied as `rotate()` after spring settles
- Depth layering: 35% of strands at 0.38–0.56 opacity (background), 65% at 0.82–1.0 (foreground)
- Vines fade via `vineOverlay.style.opacity` driven by scroll position

### Smooth Scroll Hero (~line 1889)
- `SSH_SCROLL_HEIGHT = 1500`, `SSH_INIT_CLIP = 25`, `SSH_FINAL_CLIP = 75`
- Hero image (`ssh-bg`) background: `s1n3mencr7hp4c2hs4m6p1cs15i215` (front facade wider view)
- Clip polygon at scroll=0: `25% 25% → 75% 75%` (centered). At full scroll: `0% 0% → 100% 100%`
- bgSize: 170% → 100% (subtle zoom-out as image expands)
- Text fades out between 55%–88% of scroll height

### Contact Form
- Endpoint: `https://formspree.io/f/mgobzene`
- Fields: `fname`, `lname`, `email`, `phone`, `message`
- All fields have `autocomplete` attributes (given-name, family-name, email, tel)
- Success state: green button, `form.reset()`
- Error state: red button, re-enabled, shows Formspree error string

---

## What's Next / Potential Improvements

- **Netlify deploy** — drag folder to netlify.com/drop for a shareable public URL
- **Formspree verification** — confirm email on formspree.io account so submissions are delivered
- **Custom domain** — can be added in Netlify settings once deployed
- **Image filenames** — both local images have spaces/special chars in filenames; rename before any server deployment that isn't Netlify/local (e.g. `bg-forest.jpg`)
- **Mobile nav** — hamburger button exists but the mobile menu open/close JS may need review
- **Photo labels in PHOTOS array** — many are mislabeled; if the full gallery is ever re-curated, the array labels should be corrected to match actual image content
- **Vine performance** — 90 strands × dense leaves = heavy SVG on low-end devices; `STRAND_COUNT` can be reduced to 50–60 for better mobile performance

---

## Running Commands

```bash
# Start dev server
python3 -m http.server 7845

# Authenticate GitHub CLI (if needed)
/opt/homebrew/bin/gh auth login

# Push changes
git add -A && git commit -m "message" && git push

# Test Formspree endpoint
curl -s -X POST https://formspree.io/f/mgobzene \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -d '{"fname":"Test","lname":"User","email":"test@example.com","message":"Test"}' | python3 -m json.tool
```
