# 05 — PHASE PLAN (v2)

**Replaces:** 04_PHASE_BREAKDOWN.md (2026-09-24, 13 phases).
**Shape:** 9 phases. Each phase is a commit on `rebuild` with a report in `docs/reports/PHASE_N.md`.
**Estimate:** 45–60 hours part-time (about 3–4 weeks at 15–20 hours/week). Estimates include review time.

---

## 0. Gate: decisions required before Phase 1

Resolve these in `00_OPEN_QUESTIONS.md`:
- #1 headline · #5 degree · #9 Neon Genesis role · #11 Google role · #26 music source · #29 default theme · #30 accent · #34 scope

Phase 1 can start without them. Phase 2 cannot.

---

## 1. Working rules

1. Work only on `rebuild`. Never push to `main`.
2. Run the checks at the end of every phase (listed in each phase).
3. Do not start phase N+1 with failing checks from phase N.
4. Record every dependency added, with its reason, in the phase report.
5. No personal text in components. Everything goes through `data/`.
6. Every `[C …]` marker must be resolved or removed before phase 9.
7. Keep the old site live until launch.

## 2. Standard checks (run every phase)

```bash
npm run dev         # runs without errors; check the page in the browser
npx tsc --noEmit    # no type errors
npm run lint        # no lint errors
npm run build       # production build succeeds
```

Visual phases also check at 360, 390, 768, 1024, 1440 px, in both themes, and with keyboard only.

## 3. Phase report template

```
## PHASE N — <name>
Date:
Commit:
Done: (list)
Not done / deferred: (list)
Dependencies added: (name — reason)
Checks: dev ✓/✗ · tsc ✓/✗ · lint ✓/✗ · build ✓/✗ (size of home JS: __ KB)
Responsive: 360 ✓/✗ · 390 ✓/✗ · 768 ✓/✗ · 1024 ✓/✗ · 1440 ✓/✗
Themes: light ✓/✗ · dark ✓/✗
Keyboard: ✓/✗
Open issues:
Ready for phase N+1: yes / no (reason)
```

---

## PHASE 1 — Inspect and set up (2–3 h)

**Goal:** Know what exists and set up a clean `rebuild` branch.

**Tasks**
- [ ] Create branch `rebuild` from `main`
- [ ] Record current versions: Next, React, Tailwind, Node, package manager
- [ ] Check whether Tailwind is v3 or v4 in the existing `package.json`; decide and record
- [ ] List existing components and data; mark what will be replaced
- [ ] Confirm the Vercel project and that `rebuild` gets preview deployments
- [ ] Add `docs/` with the six v2 specs
- [ ] Note the first build's home JS size as a baseline

**Deliverables:** `docs/reports/PHASE_1.md`, `docs/` committed, branch exists.

**Done when:** Standard checks pass on the existing code, and the preview deployment for `rebuild` loads.

---

## PHASE 2 — Foundation: tokens, theme, fonts (3–4 h)

**Goal:** Light and dark themes working from the first paint, with the type scale and base primitives.

**Tasks**
- [ ] Install Geist and Geist Mono via `next/font`
- [ ] Write `globals.css` with `@theme` tokens and both theme blocks (design §2, architecture §9.2)
- [ ] Build `ThemeProvider`, `ThemeToggle` (System/Light/Dark), `themeScript`
- [ ] Add `themeScript` to `<head>` in `layout.tsx`; `suppressHydrationWarning`
- [ ] Build `components/ui/`: Button, IconButton, Tag, Container, Section, Heading, Text, Surface
- [ ] Add reduced-motion CSS
- [ ] Create `data/types.ts` and `data/site.ts` (placeholders allowed here only)
- [ ] Verify every contrast pair (design §2.2) in both themes; record results

**Deliverables:** Working toggle, primitives, token file, contrast table in the report.

**Done when:**
- No flash of wrong theme on hard refresh (test with throttled network)
- Every primitive renders in both themes
- Contrast pairs verified
- Standard checks pass

---

## PHASE 3 — Audio core and intro gate (4–5 h)

**Goal:** Enter gate starts audio correctly; the player works across pages.

**Tasks**
- [ ] Create `data/music.ts` with at least one track (own or royalty-free with license note; otherwise an embed-only entry)
- [ ] Build `PlayerProvider` with state from architecture §5.1
- [ ] Single `HTMLAudioElement`; `preload="none"` until Enter
- [ ] `start()` called from the Enter click in the same handler
- [ ] Handle `play()` rejection with the "Tap play" state
- [ ] `PlayerBar` (desktop and mobile variants), with all controls labelled
- [ ] Progress as `role="slider"` with keyboard support (arrow keys seek 5 s)
- [ ] `IntroGate`: loader (waveform), Enter button, sessionStorage skip, 2 s max wait
- [ ] Mount provider, gate, and player in `layout.tsx`

**Deliverables:** Gate, player, provider, track data.

**Done when:**
- Enter starts playback on Chrome, Safari (desktop and iOS), and Firefox
- Refreshing within the session skips the gate and does not auto-play
- Playback continues across a client-side route change (test once a second page exists; use placeholder route for now)
- Reduced motion: loader static, Enter still works
- No audio request before Enter (check the Network tab)
- Standard checks pass

---

## PHASE 4 — Home page (4–5 h)

**Goal:** The minimal home page with hero, Now Playing, featured work, capabilities, and contact strip.

**Tasks**
- [ ] `Hero`: name at `display-xl`, headline, tagline, two buttons, profile image (`priority`)
- [ ] Text reveal on the name and headline (split text, 60 ms stagger)
- [ ] `NowPlayingCard` using the fixed track from `site.ts`; play button wired to the player
- [ ] `FeaturedWork`: three `ProjectRow` components with placeholder data until phase 5 (use `featured: true` entries from `projects.ts`)
- [ ] `Capabilities`: four groups, text only
- [ ] `ContactStrip`: email, GitHub, LinkedIn, resume
- [ ] `Header` (logo, nav, theme toggle, mobile menu)
- [ ] Skip link and landmarks
- [ ] Section reveal on scroll (once, 15% threshold)

**Deliverables:** Full home page.

**Done when:**
- Home JS within budget (architecture §10)
- LCP on the hero image under 2.5 s on throttled mobile
- No layout shift when the image loads (CLS checked)
- Every link resolves
- Responsive and both themes checked
- Standard checks pass

---

## PHASE 5 — Work: index and case studies (5–6 h)

**Goal:** `/work` and `/work/[slug]` working for all projects.

**Tasks**
- [ ] Fill `data/projects.ts` from the verified facts in content §3; mark unknowns as `[C]` (build fails until resolved)
- [ ] `/work` page: all projects, featured first; category filter (client component, URL query param)
- [ ] `ProjectCard` / `ProjectRow` shared components
- [ ] `/work/[slug]` with `generateStaticParams` and `generateMetadata`
- [ ] Case study sections: render only the optional fields that exist
- [ ] Previous / next project links
- [ ] Project images: AVIF/WebP, explicit sizes, alt text
- [ ] Links: "Live" and "Code" only when present; never a dead link

**Deliverables:** Work index, detail pages, filter.

**Done when:**
- Every project has a page that builds
- Filter works with keyboard and updates the URL
- Metadata (title, description, OG image) set per project
- Standard checks pass

---

## PHASE 6 — About, Experience (git log), Contact (5–6 h)

**Goal:** The remaining three pages.

**Tasks**
- [ ] `/about`: story (intro), quick facts, education summary, achievements list
- [ ] `/experience`: `GitLog` and `GitLogEntry`; hash from `lib/gitHash.ts` (deterministic, `aria-hidden`)
- [ ] Git-log entries render from `data/experience.ts`, newest first
- [ ] `/contact`: email (mailto), social links, resume download, location (no phone)
- [ ] `scripts/check-content.ts` (architecture §12, content §11) wired into `npm run build`
- [ ] Empty `[C]` fields block the build

**Deliverables:** Three pages; content check script.

**Done when:**
- Content check passes with no `[C` markers left (or the build is blocked with a clear message)
- Git log readable on mobile (single column, no horizontal scroll)
- All socials open in a new tab with `rel="noopener noreferrer"`
- Standard checks pass

---

## PHASE 7 — Command palette and navigation polish (3–4 h)

**Goal:** Ctrl+K palette, mobile menu, and route transitions.

**Tasks**
- [ ] `CommandPalette` with `cmdk`, dynamically imported
- [ ] Commands: Work, About, Experience, Contact, Resume, GitHub, LinkedIn, Email, Toggle theme, Play/pause
- [ ] Trigger button in the header with `aria-keyshortcuts`
- [ ] `MobileMenu`: full-screen panel, focus trapped, Escape closes, focus returns
- [ ] Route transition (fade, `dur-page`)
- [ ] Active nav state (`aria-current="page"`)

**Deliverables:** Palette, mobile menu, transitions.

**Done when:**
- Palette fully keyboard operable
- Mobile menu focus trap verified
- Reduced motion disables the route transition animation (instant change)
- Standard checks pass

---

## PHASE 8 — Motion and effects pass (3–4 h)

**Goal:** The effects that make the site feel designed, kept within the rules.

**Tasks**
- [ ] Magnetic button (fine pointer only, max 6px, off in reduced motion)
- [ ] Project row hover: image scale, title color, arrow move (pointer only)
- [ ] Player progress and play-state cross-fade
- [ ] Generated project covers (optional): SVG from project data, committed to `public/covers/`
- [ ] Review every animation against design §7 and PRD §11: remove any that don't explain something
- [ ] Verify no animation runs longer than 600 ms except the loader

**Deliverables:** Effects, and a list of effects removed.

**Done when:**
- Reduced motion verified: no loops, no parallax, no magnetic effect
- Frame rate stays smooth on a mid-range phone (Chrome DevTools throttling, 4× CPU)
- Standard checks pass

---

## PHASE 9 — Accessibility, performance, SEO (5–6 h)

**Goal:** Launch-ready quality.

**Tasks**
- [ ] Accessibility: axe DevTools on every page; fix all serious and critical issues
- [ ] Keyboard-only walkthrough of every page; record in report
- [ ] Screen reader check on home and one project page (VoiceOver or NVDA)
- [ ] Lighthouse on the preview URL: desktop ≥ 90, mobile ≥ 85 (Performance, Accessibility, Best Practices, SEO)
- [ ] `sitemap.ts`, `robots.ts`
- [ ] Metadata: unique title and description per page; OG and Twitter images; JSON-LD `Person` on home
- [ ] `og.png` and favicons
- [ ] `not-found.tsx`
- [ ] Playwright smoke test: home loads, Enter starts playback, theme toggles, all nav links resolve

**Deliverables:** Audit results, smoke test, SEO files.

**Done when:**
- Lighthouse targets met (or the gap is written down with a plan)
- Zero serious/critical axe issues
- Smoke test passes on the preview URL
- Standard checks pass

---

## PHASE 10 — Cleanup, content review, launch (3–4 h)

**Goal:** Remove what's unused, verify every fact, and ship.

**Tasks**
- [ ] Delete old components and the old `.docs/` folder, after confirming nothing imports them
- [ ] Remove unused dependencies; re-run build
- [ ] Remove debug logs and commented code
- [ ] Resolve every `[C …]` marker in `00_OPEN_QUESTIONS.md` (or drop the item)
- [ ] Final fact check: every line of site copy traced to a source tag in `04_CONTENT_AND_DATA.md`
- [ ] Update resume PDF if the resume changes (#38)
- [ ] Update `README.md` (setup, scripts, how to add a project or track)
- [ ] Set up the custom domain in Vercel once decided (#35); add redirect from the old `vercel.app` URL
- [ ] Merge `rebuild` to `main` after the PRD launch checklist (§12) passes

**Deliverables:** Clean repo, updated README, launch report.

**Done when:**
- Every PRD launch criterion checked
- Production preview reviewed on phone and desktop
- Merged to `main`; deployment live

---

## 4. Timeline summary

| Phase | Name | Hours |
|---|---|---|
| 1 | Inspect and set up | 2–3 |
| 2 | Foundation: tokens, theme, fonts | 3–4 |
| 3 | Audio core and intro gate | 4–5 |
| 4 | Home page | 4–5 |
| 5 | Work: index and case studies | 5–6 |
| 6 | About, Experience, Contact | 5–6 |
| 7 | Command palette and navigation | 3–4 |
| 8 | Motion and effects | 3–4 |
| 9 | Accessibility, performance, SEO | 5–6 |
| 10 | Cleanup and launch | 3–4 |
| | **Total** | **37–47** plus review time → **45–60** |

---

## 5. Scope control

If time runs short, cut in this order (lowest value first):
1. Generated project covers (phase 8)
2. Command palette (phase 7) — keep the header nav
3. Playwright smoke test (phase 9) — keep manual checks
4. Experience git-log hash decoration — keep the layout
5. Mobile collapsed player — keep play/pause and mute only

Do not cut: intro gate, theme toggle, accessibility, content checks, and the `rebuild` branch workflow.
