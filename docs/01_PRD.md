# 01 — PRODUCT REQUIREMENTS: ATHARVA JONDHALE PORTFOLIO (v2)

**Status:** Draft for approval · **Owner:** Atharva Jondhale · **Replaces:** the 2026-09-24 PRD
**Live site (kept until v2 launches):** https://atharvajondhale.vercel.app/
**Repo:** Atharva2093/Portfolio (work on `rebuild` branch)

Items marked `[CONFIRM]` are open in `00_OPEN_QUESTIONS.md`.

---

## 1. Purpose

A personal portfolio that makes a visitor remember Atharva within 10 seconds, and gives recruiters, collaborators, and clients what they need in under 2 minutes: who he is, what he has built, and how to reach him.

The concept is **"A developer's playlist"**: the site is framed as a music player. Projects are tracks, experience is a changelog, and a song plays after the visitor clicks Enter.

## 2. Audience

| Audience | Needs |
|---|---|
| Recruiters / hiring managers | Resume, projects with roles, contact in one click |
| Design-focused teams | Case studies, process, visual quality |
| Clients / freelance | Projects, availability, contact |
| Peers / open source | GitHub, experiments, writing |

## 3. Positioning

Headline `[CONFIRM #1]`: **UI/UX & Frontend Developer · AI Builder**

Supporting facts (all verified in [R] or [S]):
- B.Tech Computer Engineering, MCOERC Nashik `[CONFIRM #5, #6]`
- ERP Developer Intern at Praktan Technologies (C#, Avalonia UI, MVVM, gRPC)
- Google Student Ambassador `[CONFIRM #11]`
- Built BrainBuddy (AI mental-health companion) and KAREN (AI voice assistant)
- Open source contributor (ECWOC, Aperture 3.0, WOCS)

## 4. Goals and success metrics

| Goal | Measure |
|---|---|
| Strong first impression | Visitor reaches Enter and at least one project in the first session (analytics, after launch) |
| Easy contact | Contact links reachable in one click from every page |
| Fast | Lighthouse Performance ≥ 90 on desktop, ≥ 85 on mobile (hero page) |
| Accessible | WCAG 2.1 AA, keyboard-only use of every control |
| Maintainable | Adding a project or track touches one data file only |

## 5. Scope

### 5.1 Version 1 (must have)
- Intro gate: loader, then an **Enter** button
- Global music player: play/pause, next/previous, track list, volume, mute
- Light and dark theme toggle, system default
- Home page: hero, Now Playing card, featured work (3), capabilities (short), contact strip
- `/work`: all projects · `/work/[slug]`: case study
- `/about`: story, quick facts, education
- `/experience`: experience as a git-log timeline
- `/contact`: links, email, resume download
- Command palette (Ctrl+K / Cmd+K) for navigation
- SEO: metadata, Open Graph, sitemap, robots.txt
- Reduced-motion support

### 5.2 Version 1 (should have, phase 6 if time allows)
- Generated project covers (SVG/canvas from project data)
- Live GitHub stats on the About page (build-time fetch)
- Interactive component playground (one real UI component with controls)

### 5.3 Out of scope for v1
- Blog / CMS
- Contact form with server (links only in v1; form in v1.1 via Resend)
- Live Spotify integration (fixed track in v1)
- Multilingual content
- Analytics dashboard
- Server-side anything beyond the Next.js build

## 6. Information architecture

```
/                 Home (intro gate → hero → featured → contact strip)
/work             All projects (featured first, then archive)
/work/[slug]      Case study
/about            Story, quick facts, education, GitHub stats (if built)
/experience       Git-log timeline + education
/contact          Links, email, resume
/resume.pdf       Downloadable resume (static file)
```

Global: header with nav and theme toggle · persistent player bar · footer · command palette.

## 7. Feature requirements

### 7.1 Intro gate
- Shows a loader for at most 2 seconds, then an **Enter** button.
- Loader = waveform bars (proposed) `[CONFIRM #32]`.
- Waits for fonts and hero image before showing Enter.
- Clicking Enter unlocks audio and starts the default track (muted fallback if blocked).
- Skipped on later visits in the same session (sessionStorage flag).
- Keyboard: Enter or Space activates the button; focus moves to the hero heading after.

### 7.2 Music player
- Audio never plays before a user gesture. Browsers block autoplay; the Enter click is the gesture.
- Controls: play/pause, previous, next, shuffle (optional), progress bar (seekable), volume, mute.
- Persists across pages (layout-level component).
- Track data in `data/music.ts`: `title`, `artist`, `src` or `embedUrl`, `license`, `cover`.
- Copyrighted commercial tracks are **not hosted**. Use a Spotify embed or link instead (see `[CONFIRM #26]`).
- Mobile: player collapses to a compact bar with play/pause and expand. Mute always visible.
- Pause button is the default focus after Enter is not used for audio.

### 7.3 Theme
- Three options in the toggle: System, Light, Dark. Default: System `[CONFIRM #29]`.
- Saved in localStorage. A small script in `<head>` applies it before first paint (no flash).
- Every color comes from CSS variables. No hard-coded hex in components.
- Meets contrast in both themes (see design system).

### 7.4 Home page
- **Hero:** name, headline, one-line positioning, two buttons (View work, Contact), profile image.
- **Now Playing card:** current track title and artist, play button linking to the player.
- **Featured work:** 3 project rows. Image, title, one-line summary, year, role tag.
- **Capabilities:** four short groups (Design, Development, AI, Engineering). Text only, no percentages or progress bars.
- **Contact strip:** email, GitHub, LinkedIn, resume.
- Everything else lives on subpages. The home page stays minimal.

### 7.5 Work
- `/work`: grid or list of all projects. Filter by category (Design, Development, AI, Open Source).
- Project card: title, summary, category, year, tech tags, links.
- `/work/[slug]` sections, each optional:
  Overview · Problem · Approach · Build · Outcome · Learnings
- Links: Live (if available), Code (if available). Never show a broken link.
- Previous / next project at the bottom.

### 7.6 Experience (git log)
- Each role is a "commit": short hash, date, role as the message, organization and location as the author line, two or three bullets.
- Education as earlier commits in the same list.
- No star ratings, no "impact" labels.

### 7.7 About
- Short story (3–4 sentences) `[CONFIRM #2]`.
- Quick facts `[CONFIRM #23]`.
- Education summary `[CONFIRM #5–8]`.
- Hackathons and certifications as a list `[CONFIRM #19–22]`.

### 7.8 Contact
- Email (mailto link) `[CONFIRM #39 for form]`.
- Social links: GitHub, LinkedIn, Linktree; X and Instagram if confirmed `[CONFIRM #24]`.
- Resume download.
- Location: Nashik, India. Phone not shown publicly `[CONFIRM #25]`.

### 7.9 Command palette
- Opens on Ctrl+K / Cmd+K and from a visible button.
- Commands: Work, About, Experience, Contact, Resume, GitHub, LinkedIn, Email, Toggle theme, Play/pause.
- Fully keyboard operable. Escape closes. Focus returns to the trigger.

## 8. Content rules

- Only verified facts. Every claim on the site traces to [R], [S], or a user answer in `00_OPEN_QUESTIONS.md`.
- No counters or percentages unless they can be checked (see #4).
- No invented outcomes, metrics, or testimonials.
- English only.
- Credit collaborators and template sources clearly (for example, Neon Genesis).

## 9. Non-functional requirements

| Area | Requirement |
|---|---|
| Performance | LCP < 2.5 s on 4G mobile; JS on the home page under 180 KB gzipped (target) |
| Audio | Not loaded until Enter; no blocking network calls before the first paint |
| Images | AVIF/WebP via `next/image`, explicit sizes, no layout shift (CLS < 0.1) |
| Accessibility | WCAG 2.1 AA; keyboard; visible focus; skip link; prefers-reduced-motion |
| Responsive | Tested at 360, 390, 768, 1024, 1440 px |
| Browsers | Latest two versions of Chrome, Edge, Firefox, Safari; iOS and Android latest |
| SEO | Unique title and description per page; OG image; sitemap; robots; JSON-LD `Person` on home |
| Privacy | No analytics or tracking in v1 unless added deliberately; mention in footer if added |

## 10. Explicitly rejected (from the old direction)

Kept out of v1 to protect speed and readability:
- Full-screen 3D background or 3D hero object
- Particle backgrounds, infinite marquees
- Custom cursor on every page
- Skill percentage bars
- Glassmorphism on every card
- Autoplaying audio

The Spotify-style visual language is kept, but the effects serve the content.

## 11. Motion principles

- Motion explains, it does not decorate. Every animation answers "what changed?" or "where did this come from?".
- Durations: 150–300 ms for UI, 400–600 ms for reveals.
- Transforms and opacity only. No animating width, height, or top/left.
- Reduced motion: no loop animations, no parallax, no scroll-jacking. Fades allowed.

## 12. Launch criteria

- All v1 must-haves complete (section 5.1)
- Lighthouse, accessibility, and responsive checks pass (section 9)
- Every `[CONFIRM]` item resolved or removed
- Resume PDF updated and matches the site
- Preview deployment reviewed on phone and desktop
- Merged to `main`; old site retired or redirected

## 13. Risks

| Risk | Mitigation |
|---|---|
| Copyright on music | Use royalty-free or own tracks, or embed Spotify |
| Autoplay blocked | Enter gate as user gesture; mute fallback |
| Visual scope creep | Phase gates; v1 must-haves only until launch |
| Stale facts on site | Single data file; checklist before each release |
| Inflated claims from old site | Remove counters; verify every item in `00_OPEN_QUESTIONS.md` |
| Accidental live deploy from `main` | Work on `rebuild` branch; merge only at launch |
