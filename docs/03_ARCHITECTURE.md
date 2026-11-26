# 03 — TECHNICAL ARCHITECTURE (v2)

**Replaces:** 03_ARCHITECTURE.md (2026-09-24). Version numbers are *targets*: install the current stable release at Phase 1 and record it in `PHASE_1_INSPECTION.md`.

---

## 1. Stack

| Concern | Choice | Why |
|---|---|---|
| Framework | Next.js (App Router), latest stable | Server components, static output, metadata API, Vercel-native |
| Language | TypeScript, `strict: true` | Typed data files |
| Styling | Tailwind CSS v4 (CSS-first `@theme` config) | Current version; tokens live in CSS variables |
| Theme | `next-themes` or a 40-line custom provider | Flash-free theme switching |
| Motion | `motion` (Framer Motion's current package name) `[verify at install]` | Layout and reveal animations |
| Icons | `lucide-react` | Consistent stroke icons |
| Audio | Native `HTMLAudioElement` in a client context | No library needed for play/pause/seek/volume |
| Fonts | `next/font/google` (Geist, Geist Mono) | Self-hosted, no layout shift |
| Images | `next/image` | AVIF/WebP, sizes, lazy load |
| Command palette | `cmdk` | Accessible combobox behavior |
| Lint / format | ESLint (Next config), Prettier | Standard |
| Testing | Playwright smoke test (phase 7 onward) | Catches broken links and the audio gate |
| Hosting | Vercel | Preview deployments per branch |
| 3D | **Not included in v1** | Out of scope (PRD §10) |

Do not install anything not listed here without a note in the phase report.

---

## 2. Rendering model

- **Server components by default.** Pages, data reading, and static sections are server components.
- **Client components only for:** intro gate, audio player, theme toggle, command palette, magnetic button, reveal wrappers, mobile menu.
- Client components are leaves. The layout passes data down as props; it does not import data inside client components.
- Everything is static at build time (`generateStaticParams` for `/work/[slug]`). No runtime API routes in v1.

---

## 3. Folder structure

```
portfolio/
├── app/
│   ├── layout.tsx              Root: fonts, theme script, providers, player, header, footer
│   ├── page.tsx                Home
│   ├── globals.css             Tailwind import, @theme tokens, base styles, reduced-motion
│   ├── work/
│   │   ├── page.tsx            All projects
│   │   └── [slug]/page.tsx     Case study (generateStaticParams)
│   ├── about/page.tsx
│   ├── experience/page.tsx
│   ├── contact/page.tsx
│   ├── not-found.tsx
│   ├── sitemap.ts              Generated sitemap
│   └── robots.ts               Generated robots
│
├── components/
│   ├── intro/                  IntroGate.tsx, Loader.tsx
│   ├── player/                 PlayerProvider.tsx (context), PlayerBar.tsx, NowPlayingCard.tsx,
│   │                           TrackList.tsx, usePlayer.ts
│   ├── theme/                  ThemeProvider.tsx, ThemeToggle.tsx, themeScript.ts
│   ├── layout/                 Header.tsx, Nav.tsx, MobileMenu.tsx, Footer.tsx, SkipLink.tsx
│   ├── home/                   Hero.tsx, FeaturedWork.tsx, Capabilities.tsx, ContactStrip.tsx
│   ├── work/                   ProjectRow.tsx, ProjectCard.tsx, ProjectFilter.tsx, CaseStudySection.tsx
│   ├── experience/             GitLog.tsx, GitLogEntry.tsx
│   ├── command/                CommandPalette.tsx
│   ├── motion/                 Reveal.tsx, SplitText.tsx, MagneticButton.tsx
│   └── ui/                     Button.tsx, Tag.tsx, Container.tsx, Section.tsx, Heading.tsx,
│                               Text.tsx, IconButton.tsx, Surface.tsx
│
├── data/                       All personal content. Typed. No copy in components.
│   ├── types.ts
│   ├── site.ts                 Name, title, email, socials, resume path, now-playing fixed track id
│   ├── music.ts                Tracks
│   ├── projects.ts             Projects
│   ├── experience.ts           Experience + education entries (for the git log)
│   ├── skills.ts               Capabilities groups
│   ├── achievements.ts         Hackathons, certifications, open source
│   ├── quickFacts.ts           About page facts
│   └── index.ts
│
├── lib/
│   ├── utils.ts                cn(), formatTime(), slugify()
│   ├── gitHash.ts              Deterministic short hash from id (for the git log)
│   ├── metadata.ts             Per-page metadata helpers
│   └── constants.ts            Nav items, storage keys
│
├── hooks/
│   ├── useReducedMotion.ts
│   ├── useInView.ts
│   ├── useMediaQuery.ts
│   └── useHasEntered.ts        sessionStorage flag for the intro gate
│
├── public/
│   ├── resume.pdf
│   ├── images/projects/        Project images (AVIF + WebP generated at build)
│   ├── images/profile/         Profile photo
│   ├── covers/                 Track covers
│   ├── audio/                  Only royalty-free or own tracks, each with a LICENSE note
│   ├── favicon.ico, apple-touch-icon.png, og.png
│
├── docs/                       These specs (not the old .docs/ folder)
├── next.config.ts
├── tsconfig.json
├── package.json
└── README.md
```

Rules:
- Components never contain personal text. Text comes from `data/`.
- One component per file. Name = file name.
- `components/motion/*` are the only place that touches animation libraries; everything else uses those wrappers.

---

## 4. Data layer

Typed data files in `data/`, described fully in `04_CONTENT_AND_DATA.md`.

- `site.ts` exports a single `siteConfig` object.
- Each collection is an array with a stable `id` string.
- Pages import from `data/index.ts` only.
- Validation: a `scripts/check-content.ts` script (Phase 6) fails the build if a project has no slug, a track has no `src` or `embedUrl`, or an external link is malformed.

---

## 5. Audio system

### 5.1 State
`PlayerProvider` (client) holds:
- `queue: Track[]`, `index: number`
- `status: 'idle' | 'loading' | 'playing' | 'paused' | 'error'`
- `currentTime`, `duration`, `volume`, `muted`
- `hasEntered: boolean` (set by intro gate)

### 5.2 Audio element
- One `HTMLAudioElement` created once, held in a ref inside the provider.
- `preload="none"` until Enter is clicked. After Enter, `preload="auto"` on the current track only.
- Events (`timeupdate`, `ended`, `error`, `loadedmetadata`) update state. Progress uses these events, not `setInterval`.
- `play()` returns a promise; catch the rejection and set `status = 'paused'` with a message "Tap play to start" (autoplay policy fallback).

### 5.3 Autoplay contract
1. Intro gate Enter button → `onClick` calls `player.start()`.
2. `start()` sets `hasEntered`, sets the source, and calls `play()` in the same handler (keeps the user gesture).
3. If `play()` is rejected, the player shows paused state. No retry loops.
4. Reloads within the same session skip the gate; the player does **not** auto-play. The visitor presses play.

### 5.4 Embedded tracks
If a track has `embedUrl` instead of `src`, the player shows a link card ("Open in Spotify") and does not control playback. The controls are disabled for that track and labeled.

### 5.5 Persistence across pages
The `PlayerProvider` and `PlayerBar` are in `app/layout.tsx`, so the audio element survives route changes (App Router keeps layout state).

---

## 6. Theme system

- `data-theme` attribute on `<html>`, values `light` or `dark`.
- Stored preference in localStorage key `aj-theme`: `system | light | dark`.
- `themeScript.ts` exports a string inlined in `<head>`:
  - reads `aj-theme`
  - resolves `system` through `matchMedia`
  - sets `data-theme` and `color-scheme`
- `suppressHydrationWarning` on `<html>`.
- No color hex values outside `globals.css`.

---

## 7. Intro gate

- `IntroGate` wraps page content in the root layout.
- Shows until `hasEntered` is true **or** `sessionStorage['aj-entered']` is set.
- Loader ends when `document.fonts.ready` and the hero image has loaded, with a 2-second max timeout.
- Gate is client-only; server HTML renders the page content with `hidden` until hydration, so search engines still index content.
  - Alternative considered: render content behind a CSS overlay. Rejected: harder to make accessible.

---

## 8. Build-time data

- **GitHub stats** (optional, phase 9): `lib/github.ts` calls the public GitHub API at build time with a token in `GITHUB_TOKEN`. Result is written to `data/generated/github.json`. If the token is missing, the section is hidden. No runtime API calls.
- **Project covers** (optional, phase 9): SVG generated from project data, committed to `public/covers/`.

---

## 9. Config files

### 9.1 `next.config.ts`
```ts
import type { NextConfig } from 'next';

const nextConfig: NextConfig = {
  images: {
    formats: ['image/avif', 'image/webp'],
  },
  poweredByHeader: false,
};

export default nextConfig;
```
Do not add `dangerouslyAllowSVG` unless an SVG from an untrusted source is used. Do not set image CSP here unless a specific need appears.

### 9.2 Tailwind v4 (`app/globals.css`)
```css
@import "tailwindcss";

@theme {
  --font-sans: var(--font-geist-sans), system-ui, sans-serif;
  --font-mono: var(--font-geist-mono), ui-monospace, monospace;
  --color-bg: var(--bg);
  --color-surface: var(--surface);
  --color-surface-2: var(--surface-2);
  --color-text: var(--text);
  --color-muted: var(--text-muted);
  --color-border: var(--border);
  --color-accent: var(--accent);
  --color-accent-fg: var(--accent-fg);
  --color-accent-text: var(--accent-text);
  --radius-card: 16px;
}

:root, [data-theme="light"] {
  --bg: #FAFAF7; --surface: #FFFFFF; --surface-2: #F1F1EC;
  --text: #0E0F0E; --text-muted: #5F6661; --border: #E2E3DD;
  --accent: #0F9D63; --accent-fg: #FFFFFF; --accent-text: #0B7A4D;
  color-scheme: light;
}

[data-theme="dark"] {
  --bg: #0B0D0C; --surface: #141816; --surface-2: #1C211E;
  --text: #EDF0EC; --text-muted: #9AA39E; --border: #242A27;
  --accent: #86EFAC; --accent-fg: #0B0D0C; --accent-text: #86EFAC;
  color-scheme: dark;
}
```
Values are `PROPOSED` (design system §2). A `:root` default of light plus `[data-theme]` override is used so the system-default path works without JavaScript for the light case; the theme script handles dark.

Note: the earlier spec used `tailwind.config.ts`. Tailwind v4 uses CSS-based config, so that file is not created. Confirm the Tailwind major version in Phase 1.

### 9.3 `tsconfig.json`
Use the Next.js default with `"strict": true`, `"noUncheckedIndexedAccess": true`, and the `@/*` path alias. Do not copy the old placeholder config.

### 9.4 Environment
```
GITHUB_TOKEN=          # optional, build-time only, never NEXT_PUBLIC
NEXT_PUBLIC_SITE_URL=  # canonical URL, set once the domain is decided
```
Do not use `NEXT_PUBLIC_VERCEL_URL` (Vercel's own variable).

---

## 10. Performance budget

| Metric | Target |
|---|---|
| Home JS (gzipped, first load) | < 180 KB |
| Audio file loaded before Enter | 0 bytes |
| LCP (mobile, 4G) | < 2.5 s |
| CLS | < 0.1 |
| INP | < 200 ms |
| Images | AVIF/WebP, `sizes` set, `priority` only on hero image |

Practices:
- Dynamic import for command palette and mobile menu.
- No animation library on pages that don't use it (motion wrappers are only imported where used).
- Fonts: two families, two weights each at most.
- Check the bundle with `next build` output after every phase.

---

## 11. Accessibility implementation

- Skip link as first focusable element, targets `#main`.
- Landmarks: `header`, `nav` (labelled), `main`, `footer`.
- One `h1` per page. Headings in order.
- Player: buttons have labels ("Play", "Pause", "Next track"). Progress bar is `role="slider"` with `aria-valuetext` in time format.
- Theme toggle: radio group with labels.
- Intro gate: focus moves to Enter; after entering, focus moves to `h1`.
- Command palette: `cmdk` handles combobox roles; trigger has `aria-keyshortcuts="Control+K Meta+K"`.
- Live region: "Now playing: {title}" announced once on track change (polite).

---

## 12. Testing and validation

| Check | When | Tool |
|---|---|---|
| Type check | Every phase | `npx tsc --noEmit` |
| Lint | Every phase | `npm run lint` |
| Build | Every phase | `npm run build` |
| Content check | Phase 6 onward | `scripts/check-content.ts` |
| Responsive | Every visual phase | Browser devtools at 360, 390, 768, 1024, 1440 |
| Accessibility | Phase 8 and 10 | Lighthouse, axe DevTools, keyboard-only pass |
| Smoke test | Phase 10 | Playwright: home loads, Enter starts player, theme toggles, links resolve |
| Performance | Phase 10 | Lighthouse on the preview URL |

---

## 13. Deployment

- Branch `rebuild` → Vercel preview URL per push. Share the preview for review.
- Production `main` only after the launch checklist in the PRD (§12).
- Old site stays live until then.
- Custom domain added in Vercel once decided (`[CONFIRM #35]`). Redirect old `vercel.app` URL to the new domain for SEO.
- Rollback: Vercel "Promote previous deployment".

---

## 14. Git workflow

- Branch: `rebuild`. One commit per phase at minimum.
- Commit message format: `phase-N: short summary`.
- Phase reports in `docs/reports/PHASE_N.md`.
- Do not force-push `main`.
- Do not delete the old components until phase 11 (cleanup), and only after the new ones pass.

---

## 15. Dependency policy

- Add a dependency only when the phase plan names it.
- Record each install in the phase report with the reason.
- Remove unused dependencies in phase 11.
- Avoid packages that pull in 3D, analytics, or CSS-in-JS runtimes in v1.
