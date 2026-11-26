# 02 — DESIGN SYSTEM (v2)

**Replaces:** 02_DESIGN_SYSTEM.md (2026-09-24). Values marked `PROPOSED` need approval in `00_OPEN_QUESTIONS.md` (#29–#33).

---

## 1. Principles

1. **Content first.** The player, the cards, and the type carry the design. Color supports, it does not lead.
2. **One accent.** Used for the active state, primary action, and the play button. Never more than one accent color per view.
3. **Two equal themes.** Light and dark are designed together, not one derived from the other.
4. **Quiet surfaces.** Thin borders, soft radii, low contrast surfaces. No heavy shadows or glow except on the play button.

---

## 2. Color tokens

All colors are CSS variables. Components use semantic names only.

### 2.1 Semantic tokens

| Token | Light | Dark | Use |
|---|---|---|---|
| `--bg` | `#FAFAF7` | `#0B0D0C` | Page background |
| `--surface` | `#FFFFFF` | `#141816` | Cards, player bar |
| `--surface-2` | `#F1F1EC` | `#1C211E` | Hover, inputs, track rows |
| `--text` | `#0E0F0E` | `#EDF0EC` | Headings, body |
| `--text-muted` | `#5F6661` | `#9AA39E` | Labels, metadata |
| `--border` | `#E2E3DD` | `#242A27` | Dividers, card borders |
| `--accent` | `#0F9D63` | `#86EFAC` | Play button, active nav, focus (PROPOSED mint) |
| `--accent-fg` | `#FFFFFF` | `#0B0D0C` | Text on accent |
| `--accent-text` | `#0B7A4D` | `#86EFAC` | Accent used as text |
| `--focus` | `#0F9D63` | `#86EFAC` | Focus ring (4px, 2px offset) |

Note: the accent on light backgrounds is darker (`#0F9D63`) so text meets contrast. The bright mint `#86EFAC` is used only on dark.

### 2.2 Contrast (WCAG AA targets)

| Pair | Light | Dark | Target |
|---|---|---|---|
| `--text` on `--bg` | ≥ 15:1 | ≥ 15:1 | 4.5:1 |
| `--text-muted` on `--bg` | ≥ 5.5:1 | ≥ 6:1 | 4.5:1 |
| `--accent-text` on `--bg` | ≥ 4.5:1 | ≥ 10:1 | 4.5:1 |
| `--accent-fg` on `--accent` | ≥ 4.5:1 | ≥ 10:1 | 4.5:1 |

**Verify every pair in the browser with a contrast checker before Phase 2 sign-off.** Values above are targets, not measured results.

### 2.3 Rules
- Muted text is never used for body paragraphs, only for labels and metadata.
- Never place text on an image without a scrim that meets 4.5:1.
- No gradients in v1 except one subtle background wash on the hero (optional, reduced-motion safe).

---

## 3. Typography

### 3.1 Families (PROPOSED `[CONFIRM #33]`)
- **Sans:** Geist (UI and headings)
- **Mono:** Geist Mono (labels, timestamps, git-log hashes, player time)
- Fallback: `system-ui, -apple-system, Segoe UI, sans-serif` and `ui-monospace, monospace`
- Load with `next/font` (self-hosted, `display: swap`, no FOUT on hero)

### 3.2 Scale

Sizes use `clamp()` where they scale with the viewport. Line-heights are unitless.

| Token | Size | Line | Weight | Tracking | Use |
|---|---|---|---|---|---|
| `display-xl` | `clamp(3rem, 9vw, 7.5rem)` | 0.92 | 600 | -0.04em | Hero name |
| `display-l` | `clamp(2.25rem, 5vw, 4rem)` | 1.0 | 600 | -0.03em | Page titles |
| `heading-m` | `clamp(1.5rem, 2.5vw, 2rem)` | 1.15 | 600 | -0.02em | Section titles |
| `heading-s` | `1.25rem` | 1.3 | 600 | -0.01em | Card titles |
| `body-l` | `1.125rem` | 1.6 | 400 | 0 | Intro paragraphs |
| `body` | `1rem` | 1.6 | 400 | 0 | Default text |
| `body-s` | `0.9375rem` | 1.5 | 400 | 0 | Secondary |
| `label` | `0.8125rem` | 1.4 | 500 | 0.06em, uppercase | Section labels, tags |
| `mono` | `0.8125rem` | 1.5 | 400 | 0 | Hashes, time, metadata |

Minimum text size anywhere: 13px.

---

## 4. Spacing and layout

- Base unit: 4px. Scale: 4, 8, 12, 16, 24, 32, 48, 64, 96, 128.
- Content max-width: 1200px. Reading max-width: 65ch.
- Page gutters: 20px mobile, 32px tablet, 48px desktop.
- Section vertical rhythm: 96px desktop, 64px mobile.
- Grid: 12 columns desktop, 4 columns mobile.

---

## 5. Shape, borders, elevation

| Element | Radius | Border | Shadow |
|---|---|---|---|
| Cards, player | 16px | 1px `--border` | none |
| Buttons | 999px (pill) | 1px `--border` or none | none |
| Tags | 999px | 1px `--border` | none |
| Images | 12px | none | none |
| Play button | 999px | none | `0 8px 24px` accent at 25% (dark only) |

Rule: pill buttons, card-level radius of 16px. No mixing of sharp and rounded in one view.

---

## 6. Components

### 6.1 Button
- **Primary:** `--accent` fill, `--accent-fg` text, 48px min height, pill.
- **Secondary:** transparent, 1px `--border`, `--text` label, pill.
- **Icon button:** 44×44 minimum touch target.
- **Hover:** primary darkens 6%; secondary background `--surface-2`. Arrow icon moves 3px right.
- **Focus:** 4px outline in `--focus`, 2px offset.
- **Disabled:** 50% opacity, `cursor: not-allowed`.

### 6.2 Player bar (global)
- Position: fixed bottom on desktop (16px inset, max-width 960px, centered); fixed bottom full-width on mobile.
- Surface: `--surface`, 1px border, 16px radius (desktop), 0 radius with top border (mobile).
- Content: cover thumbnail (48px), title + artist, controls, progress bar, volume.
- Progress bar: 4px track, accent fill, 16px hit area for keyboard use.
- Time readout in `mono`.
- Collapsed mode on mobile: cover, title, play/pause only.
- Must not cover page content: body gets bottom padding equal to the bar height.

### 6.3 Now Playing card (home)
- Same surface as player. Cover, title, artist, play button.
- Shows fixed track in v1 `[CONFIRM #28]`.

### 6.4 Project row (featured work)
- Desktop: 2-column. Image 58%, text 42%. Alternating left/right on the home page.
- Mobile: stacked. Image full width, 4:3 or 16:10 ratio, `object-cover`.
- Hover (pointer devices only): image scale 1.02, title to `--accent-text`, arrow +3px.
- The whole row is a link. Focus ring on the row.

### 6.5 Git-log entry (experience)
- Row layout: hash (mono, muted) · date (mono) · title (heading-s) · org and location (body-s, muted) · bullets (body-s).
- Hash is a short fake commit ID generated from the entry `id`, styled as a code label. It is decorative; label it `aria-hidden`.
- Vertical line connecting entries, 1px `--border`.

### 6.6 Tag
- `label` type, pill, 1px `--border`, `--surface-2` fill.

### 6.7 Theme toggle
- Three-state segmented control: System, Light, Dark. Icons 16px with visible text labels on desktop.
- Current state exposed via `aria-pressed` or radio group semantics.

### 6.8 Intro gate
- Full screen, `--bg`. Centered waveform (5–7 bars, staggered height animation, 1.2 s loop, stops after load).
- Enter button appears after load. Text: "Enter".
- Reduced motion: bars static, Enter still appears.

### 6.9 Command palette
- Dialog, `--surface`, 16px radius, max-width 560px, input at top, list below.
- Keyboard: arrow keys move, Enter runs, Escape closes.

---

## 7. Motion

### 7.1 Tokens

| Token | Value | Use |
|---|---|---|
| `ease-out` | `cubic-bezier(0.16, 1, 0.3, 1)` | Reveals, entrances |
| `ease-in-out` | `cubic-bezier(0.65, 0, 0.35, 1)` | Toggles, progress |
| `dur-fast` | 150 ms | Hover, focus |
| `dur-base` | 250 ms | Buttons, tags |
| `dur-reveal` | 500 ms | Section reveal |
| `dur-page` | 300 ms | Route transition |

### 7.2 Patterns

| Pattern | Spec |
|---|---|
| Section reveal | Opacity 0→1 and translateY 16px→0, `dur-reveal`, once per section, at 15% in view |
| Text reveal (hero, intro) | Lines slide up from a clipped mask, 60 ms stagger |
| Route change | Fade out 150 ms, fade in `dur-page`. No layout jump |
| Player progress | Continuous, `linear`, driven by audio time (not timers) |
| Play/pause icon | Cross-fade 150 ms |
| Magnetic button | Desktop with fine pointer only, max 6px offset, off in reduced motion |

### 7.3 Reduced motion

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.001ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.001ms !important;
    scroll-behavior: auto !important;
  }
}
```

Plus: JS-driven effects (magnetic, text split) check `prefers-reduced-motion` and skip.

---

## 8. Responsive breakpoints

| Name | Min width | Notes |
|---|---|---|
| base | 0 | 360–414 phones, design first |
| sm | 640 | Large phones |
| md | 768 | Tablets, header switches to desktop nav |
| lg | 1024 | Two-column project rows |
| xl | 1280 | Full layout |

Test at 360, 390, 768, 1024, 1440 px.

---

## 9. Iconography

- Lucide icons, stroke 1.75, 20px default, 16px inside tags.
- Player icons: play, pause, previous, next, shuffle, repeat, volume, mute.
- No emoji in the UI. (Old site used emoji for skills; replace with Lucide or text.)

---

## 10. Imagery

- Profile photo: `profiel.png` from the old site, optimized to AVIF/WebP `[CONFIRM #31]`.
- Project images: 16:10 or 4:3, minimum 1600 px wide, under 180 KB after conversion.
- Covers for tracks: 512 × 512, square.
- Alt text required for every image. Decorative images get `alt=""`.

---

## 11. Dark/light implementation contract

- Root attributes: `data-theme="light" | "dark"` on `<html>`, plus `color-scheme` CSS.
- Default: follow `prefers-color-scheme` when no saved preference.
- Script in `<head>` reads localStorage and sets the attribute before paint.
- Images and logos that need a theme variant use two files or CSS `filter` (prefer two files).
