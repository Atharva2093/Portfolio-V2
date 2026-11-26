# 04 — CONTENT AND DATA (v2)

**Replaces:** 05_DATA_SCHEMA.md (2026-09-24), which contained invented metrics.

Every fact below has a source tag:
- **[R]** resume PDF (uploaded)
- **[S]** old site (atharvajondhale.vercel.app)
- **[U]** your answer in the setup questions
- **[C]** conflict. Blocked until resolved in `00_OPEN_QUESTIONS.md`.

Items with no tag are placeholders and must not ship.

---

## 1. TypeScript types (`data/types.ts`)

```ts
export type Theme = 'system' | 'light' | 'dark';

export interface SiteConfig {
  name: string;               // 'Atharva Jondhale'
  headline: string;           // [C #1]
  tagline: string;            // [C #3]
  intro: string;              // [C #2]
  location: string;           // 'Nashik, Maharashtra, India'
  email: string;              // 'atharvajondhale7@gmail.com'
  resumePath: string;         // '/resume.pdf'
  socials: {
    github: string;           // https://github.com/Atharva2093
    linkedin: string;         // https://www.linkedin.com/in/atharvajondhale7
    linktree: string;         // https://linktr.ee/atharva_jondhale
    x?: string;               // [C #24]
    instagram?: string;       // [C #24]
  };
  domain?: string;            // [C #35]
  nowPlayingTrackId: string;  // references data/music.ts
}

export interface Track {
  id: string;
  title: string;
  artist: string;
  cover: string;              // /covers/*.jpg
  src?: string;               // /audio/*.mp3 (own or royalty-free only)
  embedUrl?: string;          // Spotify/YouTube embed or external link
  license: 'own' | 'royalty-free' | 'embed-only';
  licenseNote?: string;       // source and license name, required for royalty-free
}

export type ProjectCategory = 'Design' | 'Development' | 'AI' | 'Open Source';

export interface Project {
  id: string;
  slug: string;
  title: string;
  summary: string;            // one line
  category: ProjectCategory;
  year: number;
  role: string;               // your role, exact wording [C #9 for Neon Genesis]
  tech: string[];
  image: string;
  imageAlt: string;
  featured: boolean;
  links: { live?: string; code?: string };
  credit?: string;            // e.g. template or collaborator credit
  case?: {                    // optional sections, none required
    overview?: string;
    problem?: string;
    approach?: string;
    build?: string;
    outcome?: string;         // only verified results
    learnings?: string;
  };
}

export type EntryKind = 'work' | 'education' | 'leadership' | 'event';

export interface Entry {
  id: string;
  kind: EntryKind;
  title: string;              // role or degree
  org: string;
  location: string;
  start: string;              // 'Oct 2024'
  end: string;                // '2025' | 'Present'
  bullets: string[];
  tags?: string[];
}

export interface Capability {
  group: 'Design' | 'Development' | 'AI' | 'Engineering';
  items: string[];            // no levels or percentages
}

export interface Achievement {
  id: string;
  title: string;
  issuer: string;
  date: string;               // 'Mar 2025' or '2025'
  type: 'hackathon' | 'certification' | 'open-source' | 'recognition';
  link?: string;
}

export interface QuickFact { label: string; emoji?: never; }
```

Note: `emoji?: never` in `QuickFact` is a placeholder that does nothing. Use `label` only.

---

## 2. Site config (`data/site.ts`)

```ts
import type { SiteConfig } from './types';

export const siteConfig: SiteConfig = {
  name: 'Atharva Jondhale',                                          // [R][S]
  headline: 'UI/UX & Frontend Developer · AI Builder',              // [C #1] proposed
  tagline: 'Still learning, still building, but it feels right.',   // [U #3] pending
  intro: '',                                                        // [C #2] write from verified facts
  location: 'Nashik, Maharashtra, India',                           // [R][S]
  email: 'atharvajondhale7@gmail.com',                              // [U]
  resumePath: '/resume.pdf',                                        // [U]
  socials: {
    github: 'https://github.com/Atharva2093',                       // [S][U]
    linkedin: 'https://www.linkedin.com/in/atharvajondhale7',       // [S][U]
    linktree: 'https://linktr.ee/atharva_jondhale',                 // [U]
    // x and instagram: [C #24]
  },
  nowPlayingTrackId: '',                                            // [C #26]
};
```

---

## 3. Projects (`data/projects.ts`)

### 3.1 BrainBuddy
| Field | Value | Source |
|---|---|---|
| title | BrainBuddy | [S] |
| summary | AI mental-health companion with gamified adventures for emotional growth | [S] |
| category | AI | proposed |
| tech | TypeScript, Next.js, Tailwind CSS, Supabase, Gemini API | [S] |
| links | live: brainbuddy-kohl.vercel.app · code: github.com/Atharva2093/brain-buddy (folder brainy-buddy-adventures-main) | [S] |
| featured | yes | proposed |
| role | `[C]` not stated. Confirm your role (builder? lead?). | |
| outcome | leave empty unless verified | |

### 3.2 KAREN — AI Personal Assistant
| Field | Value | Source |
|---|---|---|
| title | KAREN — AI Personal Assistant | [R] |
| summary | Voice-enabled assistant for natural-language commands and task automation through integrated APIs | [R] |
| category | AI | [R] |
| tech | AI, voice, API integration, local-first design | [R] (specific stack not stated) |
| links | `[C #16]` repo or demo needed | |
| featured | yes, proposed (#15) | |
| role | `[C]` confirm | |

### 3.3 Chrome Secure — Profile Security Manager
| Field | Value | Source |
|---|---|---|
| title | Chrome Secure — Profile Security Manager | [R] |
| summary | Shell utility to isolate Chrome profiles with multi-factor access and cryptographic password validation | [R] |
| category | Open Source or Development `[C]` | |
| tech | Linux, Shell scripting, security | [R] |
| links | `[C #16]` | |
| featured | no (archive) | proposed |

### 3.4 Neon Genesis
| Field | Value | Source |
|---|---|---|
| title | Neon Genesis | [S] |
| summary | Futuristic 3D portfolio template with interactive animations | [S] |
| category | Design | proposed |
| tech | React, Vite, Framer Motion, Tailwind CSS | [S] |
| links | live: neon-genesis.vercel.app · code: github.com/ankushkhakale/Neon-Genesis | [S] |
| credit | **Template by another developer.** Your role `[C #9]`. | [S][R] |
| role | `[C #9]` resume says "Lead UI/UX Designer"; old site says "UI/UX Specialist" | [C] |
| featured | no (archive, with credit) | proposed |

### 3.5 Personal Portfolio (v0 version)
| Field | Value | Source |
|---|---|---|
| title | Personal Portfolio | [S] |
| summary | Earlier portfolio with interactive animations and responsive design | [S] |
| tech | Next.js, TypeScript, Tailwind CSS, Framer Motion, EmailJS | [S] |
| links | live: v0-atharva-jondhale-portfolio.vercel.app · code: github.com/Atharva2093/Portfolio | [S] |
| featured | no (archive; this is the site being replaced) | proposed |

### 3.6 JARVIS — pending `[C #17]`
Not on either source for public display. Add only after the decision in #17.

---

## 4. Experience and education (`data/experience.ts`)

Entries appear as git-log commits. Ordered newest first.

| Kind | Title | Org | Location | Start | End | Source | Status |
|---|---|---|---|---|---|---|---|
| work | ERP Developer Intern | Praktan Technologies | Nashik, Maharashtra | Oct 2024 | 2025 `[C #10]` | [R][S] | confirmed |
| work | Google Student Ambassador | Google | Remote | `[C #11]` | Present | [R][S] | `[C]` title |
| leadership | GDG On Campus Co-Organizer | Google Developer Groups, MCOERC | Nashik | `[C #11]` | Present | [S] only | `[C]` |
| leadership | Director of Marketing | AWS Student Builders Club | `[C #12]` | `[C #12]` | `[C #12]` | [R] only | `[C]` |
| leadership | Lead UI/UX Designer | Neon Genesis | Remote | Oct 2024 | Present `[C #9]` | [R] | `[C]` |
| event | Media & storytelling | Matoshri Technical Events | Nashik | `[C #13]` | `[C #13]` | [R] | `[C]` |
| event | Member | Matoshri E-Cell | Nashik | `[C #13]` | Present | [S] | `[C]` |
| education | B.Tech, Computer Engineering | MCOERC (Savitribai Phule Pune University) | Nashik | 2024 | 2028 | [R][S] | `[C #5, #6]` |

### 4.1 Praktan bullets (verified, [R] wording, tightened)
- Developed ERP modules for User Management and Purchase System using C#, Avalonia UI, and MVVM.
- Implemented real-time data synchronization with gRPC and improved backend performance.

### 4.2 Google Student Ambassador bullets
- Represent Google developer programs on campus and promote emerging technologies. `[R]`
- Support developer community initiatives. `[R]`

(Do not add counts such as "500+ students" unless verified. The earlier spec had invented numbers; they are removed.)

### 4.3 Event bullets
- Matoshri Technical Events: led event media production and storytelling; represented college at E-Cell Summit 2025, IIT Bombay. `[R]`

### 4.4 Education detail
- Core coursework (per [S]): Operating Systems, DBMS, OOP, Software Engineering, Data Structures, Algorithms. `[S]` — confirm before showing.
- GPA 8.67/10 `[R]` — show `[C #7]`.
- Not shown: school results from [S] (`[C #8]`, proposed omit).

---

## 5. Capabilities (`data/skills.ts`)

Grouped, no levels. Sources: [R] technical skills section and [S].

| Group | Items |
|---|---|
| Design | UI/UX Design, Figma `[S]`, Canva `[S]`, Photoshop `[S]`, Adobe Premiere Pro `[S]`, CapCut `[S]` |
| Development | React.js, Next.js, TypeScript `[S]`, JavaScript, HTML5, CSS3, Tailwind CSS `[S]`, Node.js, Framer Motion `[S]` |
| AI | LLMs, Agentic AI, RAG, Prompt Engineering `[R]`, Python |
| Engineering | C#, C++, SQL, MSSQL, gRPC, Avalonia UI, MVVM, Firebase, Supabase, Docker, Git, GitHub, Linux, Google Cloud, AWS |

Notes:
- Design tools from [S] (Figma, Canva, Photoshop, Premiere, CapCut) are not in the resume `[C]`. Confirm before showing.
- Resume lists C++ and Node.js; [S] does not. Both are fine if you use them.
- Skill icons on the old site (emoji) are removed in v2.

---

## 6. Achievements (`data/achievements.ts`)

### 6.1 Hackathons `[R]` (confirm list #19)
- Bharatiya Antariksh Hackathon 2025 (ISRO)
- Felicity '25 (IIIT Hyderabad)
- HackGround India (Thoughtworks)
- Nexothon
- Prayatna 2.0

Resume says "Performer across 6+ hackathons". Five are named. Show the five, or confirm the sixth.

### 6.2 Certifications `[R]`
- Google Cloud: completed hands-on training programs. Certificate names and dates `[C #20]`.

### 6.3 Open source `[R]`
- Contributor to ECWOC, Aperture 3.0, WOCS. Merged PR count `[C #21]` (resume says 5+; confirm and link).

### 6.4 Recognition `[R][S]`
- Represented college at E-Cell Summit 2025, IIT Bombay.

---

## 7. Quick facts (`data/quickFacts.ts`) `[C #23]`

Candidates from [S]:
- Night owl, fueled by caffeine and code
- Loves testing new tech stacks
- Plays chess (slow, sharp, strategic)
- Plays basketball

Keep 3–4. Remove if you prefer a stricter professional tone.

---

## 8. Music (`data/music.ts`)

| id | Title | Artist | License | Status |
|---|---|---|---|---|
| `own-01` | `[U]` your track | Atharva Jondhale | own | needs file |
| `rf-01` | `[C #26]` royalty-free option | `[C]` | royalty-free | needs file and license note |
| `embed-01` | `[C #26]` Spotify embed | `[C]` | embed-only | needs URL |

Mockup tracks (Golden Hour, Bet On Myself, Kho Gaye Hum Kahan) are **not** used as hosted files. They are commercial recordings.

---

## 9. Contact

| Field | Value | Status |
|---|---|---|
| Email | atharvajondhale7@gmail.com | [U] |
| GitHub | github.com/Atharva2093 | [S] |
| LinkedIn | linkedin.com/in/atharvajondhale7 | [S] |
| Linktree | linktr.ee/atharva_jondhale | [U] |
| X | x.com/atharva_j2093 | [S] `[C #24]` |
| Instagram | instagram.com/atharva_j2093 | [S] `[C #24]` |
| Location | Nashik, Maharashtra, India | [R][S] |
| Phone | +91 7588195521 | [R][S] — **not shown** `[C #25]` |

---

## 10. Content removed from v1 (and why)

| Item | Reason |
|---|---|
| "2+ Years Experience", "1+ Years", "4 Active Roles", "3+ Projects", "8+ Technologies" | Inconsistent across sources; not verifiable |
| "500+ students", "3+ workshops", "95%+ accuracy" (earlier spec) | Invented |
| Skill percentages / "High Impact" labels | Not in the approved visual direction |
| Phone on public page | Privacy default (#25) |
| Emoji skill icons | Replaced by Lucide icons |
| School percentages | Not needed for a portfolio (#8) |

---

## 11. Validation (`scripts/check-content.ts`, phase 6)

The script fails the build if:
- A project has no `slug`, `title`, `summary`, or `role`
- A project with `featured: true` has no `image`
- A track has neither `src` nor `embedUrl`
- A track with `license: 'royalty-free'` has no `licenseNote`
- Any `links` value is not a valid URL
- Any `[C` marker remains in a field (text search of `data/`)
- Any `intro`, `headline`, or `nowPlayingTrackId` is empty

The last check keeps placeholders out of production.
