# Phase A — Foundation (2–3 days)

## Objective

Scaffold the entire project infrastructure: Next.js 16 + TypeScript project, all dependencies, design token system, font configuration, animation library setup, smooth scroll, state management, type definitions, and Sanity CMS. This is the bedrock — every subsequent phase builds on it.

## Done When

`npm run dev` shows a blank dark page (`#0C0F14`) with smooth scroll active, correct fonts loading (Instrument Serif, Satoshi, JetBrains Mono), and film grain overlay visible.

## Dependencies from Prior Phases

None — this is the first phase.

## What to Build

### 1. Project Scaffold

Initialize Next.js 16.2 with TypeScript, App Router, `src/` directory. Install all dependencies per `package.json` below.

### 2. Configuration Files

#### `next.config.ts`

```typescript
import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  images: {
    formats: ["image/avif", "image/webp"],
    deviceSizes: [640, 768, 1024, 1280, 1536, 1920],
    imageSizes: [16, 32, 48, 64, 96, 128, 256, 384],
    minimumCacheTTL: 60 * 60 * 24 * 365,
    remotePatterns: [
      { protocol: "https", hostname: "cdn.sanity.io" },
    ],
  },
  logging: {
    fetches: { fullUrl: true },
  },
  // Turbopack is default in Next.js 16 — no flag needed
};

export default nextConfig;
```

#### `tsconfig.json`

```json
{
  "compilerOptions": {
    "target": "ES2017",
    "lib": ["dom", "dom.iterable", "esnext"],
    "allowJs": true,
    "skipLibCheck": true,
    "strict": true,
    "noEmit": true,
    "esModuleInterop": true,
    "module": "esnext",
    "moduleResolution": "bundler",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "jsx": "preserve",
    "incremental": true,
    "plugins": [{ "name": "next" }],
    "paths": {
      "@/*": ["./src/*"]
    }
  },
  "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx", ".next/types/**/*.ts"],
  "exclude": ["node_modules"]
}
```

#### `package.json`

```json
{
  "name": "bamyan-storyworks",
  "version": "1.0.0",
  "private": true,
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint",
    "type-check": "tsc --noEmit"
  },
  "dependencies": {
    "next": "^16.2.0",
    "react": "^19.2.0",
    "react-dom": "^19.2.0",
    "gsap": "^3.14.2",
    "@gsap/react": "^2.1.2",
    "motion": "^12.37.0",
    "lenis": "^1.3.19",
    "tailwindcss": "^4.0.0",
    "clsx": "^2.1.0",
    "tailwind-merge": "^3.0.0",
    "zustand": "^5.0.12",
    "next-sanity": "^9.0.0",
    "@sanity/image-url": "^1.1.0",
    "@sanity/client": "^7.0.0",
    "zod": "^3.24.0",
    "resend": "^4.0.0"
  },
  "devDependencies": {
    "typescript": "^5.7.0",
    "@types/react": "^19.0.0",
    "@types/react-dom": "^19.0.0",
    "@types/node": "^22.0.0",
    "eslint": "^9.0.0",
    "eslint-config-next": "^16.2.0",
    "prettier": "^3.4.0",
    "sanity": "^3.80.0",
    "styled-components": "^6.1.0"
  }
}
```

#### `.env.example`

```bash
# Sanity CMS
NEXT_PUBLIC_SANITY_PROJECT_ID=your_project_id
NEXT_PUBLIC_SANITY_DATASET=production
NEXT_PUBLIC_SANITY_API_VERSION=2024-07-11
SANITY_API_READ_TOKEN=your_read_token

# Email (Resend)
RESEND_API_KEY=re_your_api_key
CONTACT_EMAIL=hello@bamyanstoryworks.com

# Analytics
NEXT_PUBLIC_GA_MEASUREMENT_ID=G-XXXXXXXXXX

# Site
NEXT_PUBLIC_SITE_URL=https://bamyanstoryworks.com
```

#### `.prettierrc`

```json
{
  "semi": true,
  "singleQuote": false,
  "tabWidth": 2,
  "trailingComma": "all",
  "printWidth": 100
}
```

### 3. Design Token System — `src/styles/globals.css`

```css
@import "tailwindcss";

/* ============================================
   BAMYAN STORYWORKS — Design Token System
   ============================================ */

@theme {
  /* --- Colors: "Fog" Palette --- */
  --color-background: #0C0F14;
  --color-surface: #161B24;
  --color-surface-elevated: #1C2230;
  --color-primary: #7B93B0;
  --color-primary-muted: #5A7091;
  --color-accent: #7B93B0;
  --color-text: #D4D4D8;
  --color-text-heading: #E8E8EC;
  --color-muted: #64748B;
  --color-border: #1E2736;
  --color-border-hover: #2A3548;
  --color-overlay: rgba(12, 15, 20, 0.85);
  --color-overlay-solid: #0C0F14;

  /* --- Typography: Font Families --- */
  --font-display: "Instrument Serif", "Georgia", "Times New Roman", serif;
  --font-body: "Satoshi", "system-ui", "-apple-system", "Segoe UI", sans-serif;
  --font-mono: "JetBrains Mono", "Fira Code", "Consolas", monospace;

  /* --- Typography: Type Scale (Major Third — 1.25 ratio) --- */
  --text-xs: 0.75rem;       /* 12px */
  --text-sm: 0.875rem;      /* 14px */
  --text-base: 1rem;         /* 16px */
  --text-lg: 1.125rem;       /* 18px */
  --text-xl: 1.25rem;        /* 20px */
  --text-2xl: 1.5rem;        /* 24px */
  --text-3xl: 2rem;          /* 32px */
  --text-4xl: 2.5rem;        /* 40px */
  --text-5xl: 3.5rem;        /* 56px */
  --text-6xl: 4.5rem;        /* 72px */
  --text-7xl: 6rem;          /* 96px */
  --text-hero: 8rem;         /* 128px — hero display size */
  --text-hero-mobile: 3.5rem; /* 56px — hero on mobile */
  --text-footer-cta: 5rem;   /* 80px — footer CTA */

  /* --- Typography: Font Weights --- */
  --font-thin: 200;
  --font-light: 300;
  --font-regular: 400;
  --font-medium: 500;
  --font-semibold: 600;
  --font-bold: 700;
  --font-black: 900;

  /* --- Typography: Letter Spacing --- */
  --tracking-tighter: -0.04em;
  --tracking-tight: -0.02em;
  --tracking-normal: 0em;
  --tracking-wide: 0.04em;
  --tracking-wider: 0.08em;
  --tracking-widest: 0.16em;
  --tracking-hero: 0.06em;   /* BAMYAN wordmark spacing */

  /* --- Typography: Line Heights --- */
  --leading-none: 1;
  --leading-tight: 1.15;
  --leading-snug: 1.3;
  --leading-normal: 1.6;
  --leading-relaxed: 1.75;

  /* --- Spacing: Base unit 4px × multiplier --- */
  --space-1: 0.25rem;   /* 4px */
  --space-2: 0.5rem;    /* 8px */
  --space-3: 0.75rem;   /* 12px */
  --space-4: 1rem;      /* 16px */
  --space-6: 1.5rem;    /* 24px */
  --space-8: 2rem;      /* 32px */
  --space-12: 3rem;     /* 48px */
  --space-16: 4rem;     /* 64px */
  --space-24: 6rem;     /* 96px */
  --space-32: 8rem;     /* 128px */
  --space-48: 12rem;    /* 192px */
  --space-64: 16rem;    /* 256px */

  /* --- Motion: Duration Tokens --- */
  --duration-instant: 100ms;
  --duration-fast: 200ms;
  --duration-normal: 400ms;
  --duration-slow: 800ms;
  --duration-cinematic: 1200ms;
  --duration-hero: 3500ms;

  /* --- Motion: Easing Tokens (CSS cubic-bezier + GSAP equivalents in comments) --- */
  --ease-out: cubic-bezier(0.33, 1, 0.68, 1);             /* GSAP: power3.out */
  --ease-in-out: cubic-bezier(0.65, 0, 0.35, 1);          /* GSAP: power2.inOut */
  --ease-out-expo: cubic-bezier(0.16, 1, 0.3, 1);         /* GSAP: expo.out */
  --ease-out-circ: cubic-bezier(0, 0.55, 0.45, 1);        /* GSAP: circ.out */

  /* --- Borders --- */
  --radius-none: 0;
  --radius-sm: 0.25rem;    /* 4px */
  --radius-md: 0.5rem;     /* 8px */
  --radius-lg: 0.75rem;    /* 12px */
  --radius-full: 9999px;

  /* --- Layout --- */
  --max-width: 90rem;       /* 1440px */
  --max-width-content: 72rem; /* 1152px */
  --max-width-narrow: 48rem;  /* 768px */
  --grid-gap: 1.5rem;       /* 24px */
  --section-padding-y: 8rem; /* 128px vertical section spacing */
  --section-padding-y-mobile: 4rem; /* 64px on mobile */
  --container-padding-x: 2rem; /* 32px horizontal page padding */
  --container-padding-x-mobile: 1rem; /* 16px on mobile */
  --header-height: 5rem;    /* 80px */
}

/* --- Film Grain Overlay --- */
.grain-overlay::after {
  content: "";
  position: fixed;
  inset: 0;
  z-index: 9999;
  pointer-events: none;
  opacity: 0.04;
  background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 256 256' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noise'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noise)'/%3E%3C/svg%3E");
  background-repeat: repeat;
  mix-blend-mode: overlay;
}

/* --- Reduced Motion Preferences --- */
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}

/* --- Global Resets & Base --- */
html {
  background-color: var(--color-background);
  color: var(--color-text);
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

::selection {
  background-color: var(--color-primary);
  color: var(--color-background);
}

/* --- Lenis Smooth Scroll --- */
html.lenis,
html.lenis body {
  height: auto;
}

.lenis.lenis-smooth {
  scroll-behavior: auto !important;
}

.lenis.lenis-smooth [data-lenis-prevent] {
  overscroll-behavior: contain;
}

.lenis.lenis-stopped {
  overflow: hidden;
}
```

### 4. Font Configuration — `src/lib/fonts.ts`

```typescript
import { Instrument_Serif } from "next/font/google";
import localFont from "next/font/local";

export const instrumentSerif = Instrument_Serif({
  subsets: ["latin"],
  weight: ["400"],
  display: "swap",
  variable: "--font-display",
  preload: true,
});

// Satoshi from Fontshare — downloaded as local font files
export const satoshi = localFont({
  src: [
    { path: "../public/fonts/Satoshi-Light.woff2", weight: "300", style: "normal" },
    { path: "../public/fonts/Satoshi-Regular.woff2", weight: "400", style: "normal" },
    { path: "../public/fonts/Satoshi-Medium.woff2", weight: "500", style: "normal" },
    { path: "../public/fonts/Satoshi-Bold.woff2", weight: "700", style: "normal" },
  ],
  display: "swap",
  variable: "--font-body",
  preload: true,
});

// JetBrains Mono for metadata/mono accent
export const jetbrainsMono = localFont({
  src: [
    { path: "../public/fonts/JetBrainsMono-Regular.woff2", weight: "400", style: "normal" },
  ],
  display: "swap",
  variable: "--font-mono",
  preload: false, // not critical for FCP
});
```

### 5. GSAP Setup — `src/lib/gsap.ts`

```typescript
import gsap from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";
import { SplitText } from "gsap/SplitText";
import { Flip } from "gsap/Flip";

gsap.registerPlugin(ScrollTrigger, SplitText, Flip);

export { gsap, ScrollTrigger, SplitText, Flip };
```

### 6. Easing Tokens — `src/lib/easings.ts`

```typescript
export const easings = {
  smooth: "power3.out",
  smoothInOut: "power2.inOut",
  sharp: "expo.out",
  sharpCirc: "circ.out",
  entrance: "power3.out",
  exit: "power2.in",
  stateChange: "power2.inOut",
} as const;

export type EasingToken = keyof typeof easings;
```

### 7. LazyMotion — `src/lib/motion-features.ts`

```typescript
import { domAnimation } from "motion/react";
export default domAnimation;
```

### 8. Providers — `src/providers/Providers.tsx`

```typescript
// Client Component — wraps app with GSAP context, Lenis smooth scroll, LazyMotion
"use client";

import { LazyMotion } from "motion/react";

const features = () => import("@/lib/motion-features").then((mod) => mod.default);

export function Providers({ children }: { children: React.ReactNode }) {
  return (
    <LazyMotion features={features} strict>
      {children}
    </LazyMotion>
  );
}
```

Lenis setup within Providers:
- `lerp: 0.06` (cinematic slow)
- `duration: 1.4`
- `autoRaf: false` — sync to GSAP ticker for frame-perfect coordination
- GSAP ticker calls `lenis.raf(time)` each frame

### 9. Zustand Store — `src/stores/ui-store.ts`

Initial shape:
```typescript
interface UIState {
  menuOpen: boolean;
  isTransitioning: boolean;
  transitionType: "wipe" | "morph";
  morphSource: Flip.FlipState | null;
  scrollProgress: number;
  activeSection: string;
  // Actions
  setMenuOpen: (open: boolean) => void;
  startTransition: (type: "wipe" | "morph", morphState?: Flip.FlipState) => void;
  endTransition: () => void;
  setScrollProgress: (progress: number) => void;
  setActiveSection: (section: string) => void;
}
```

### 10. Type Definitions — `src/types/`

Create these files:
- `project.ts` — Album, AlbumMeta, SanityImage interfaces
- `animation.ts` — AnimationPreset, ScrollTriggerConfig types
- `ui.ts` — NavItem type
- `index.ts` — barrel exports

### 11. Root Layout — `src/app/layout.tsx`

Server Component. Apply font CSS variables to `<html>` tag, wrap `{children}` in `<Providers>`, add `grain-overlay` class to `<body>`.

### 12. Sanity Setup

- Create Sanity project (via `sanity init` or sanity.io/manage)
- `src/sanity/client.ts` — configured client
- `src/sanity/queries.ts` — GROQ queries with `defineQuery`
- `src/sanity/schemas/album.ts` — album document schema
- `src/sanity/schemas/about.ts` — about singleton schema
- `src/sanity/lib/image.ts` — image URL builder

## Project Structure (for reference)

```
src/
├── app/
│   ├── layout.tsx
│   ├── template.tsx
│   ├── not-found.tsx
│   ├── error.tsx
│   ├── loading.tsx
│   ├── page.tsx
│   ├── work/
│   │   ├── page.tsx
│   │   └── [slug]/page.tsx
│   ├── about/page.tsx
│   ├── contact/
│   │   ├── page.tsx
│   │   └── action.ts
│   ├── studio/[[...tool]]/page.tsx
│   ├── opengraph-image.tsx
│   ├── sitemap.ts
│   └── robots.ts
├── components/
│   ├── animations/
│   ├── layout/
│   ├── sections/
│   └── ui/
├── hooks/
├── lib/
├── types/
├── stores/
├── sanity/
├── styles/
├── providers/
└── proxy.ts
```
