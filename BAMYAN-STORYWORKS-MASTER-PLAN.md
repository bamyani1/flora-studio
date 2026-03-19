# MASTER IMPLEMENTATION PLAN — Bamyan Storyworks

---

## 1. Project Overview

**Bamyan Storyworks** is a cinematic photography portfolio and business site showcasing personal, event, sports, and solo photography across 5–6 curated albums. The site targets potential clients (individuals, couples, event organizers, athletes) and creative industry peers. The emotional direction is **cinematic & moody meets minimal & precise** — atmospheric tension with Swiss-level restraint, where every frame carries weight and every interaction feels intentional.

**Project Name:** Bamyan Storyworks
**Defining Keywords:** Cinematic, Moody, Minimal, Photographic, Exhibition
**Technical Skill Context:** The developer is implementing this themselves and is experienced — this plan focuses on architectural decisions and specifications rather than tutorial-level code snippets.

**Reference Sites & What to Draw From Each:**

- **Anima (anima-cc.com):** Editorial cleanliness, typographic discipline, precise spacing, the way text and imagery coexist without competing. Draw from their restraint in animation — nothing moves without purpose.
- **Griflan (griflan.com):** Dark immersive atmosphere, slow-reveal cinematic energy, how photography dominates the viewport. Draw from their image pacing and the weight they give each visual.

**Signature Moments:**

1. **Kinetic Typography Hero** — the first 3.5 seconds on homepage load where "BAMYAN STORYWORKS" choreographs into existence through masked line reveals, animated letter-spacing, and a photograph breathing in behind the text.
2. **Scroll-Driven Album Narratives** — each album page unfolds like a film: horizontal pinned gallery sections, narrative text that highlights word-by-word as you scroll, photographs paced like scenes in a story rather than dumped in a grid.

---

## 2. Design Token System

### `styles/globals.css`

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

### `lib/fonts.ts` — next/font Configuration

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

---

## 3. Animation Presets Library

All animations are defined as named presets in `lib/animations.ts`. Each preset is a factory function returning GSAP configuration objects. Plugin registration happens once in `lib/gsap.ts`.

### `lib/gsap.ts` — Centralized Plugin Registration

```typescript
import gsap from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";
import { SplitText } from "gsap/SplitText";
import { Flip } from "gsap/Flip";

gsap.registerPlugin(ScrollTrigger, SplitText, Flip);

export { gsap, ScrollTrigger, SplitText, Flip };
```

### `lib/easings.ts` — Easing Token Map

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

### `lib/animations.ts` — Named Preset Factories

```typescript
import { easings } from "./easings";
import type { EasingToken } from "./easings";

// --------------------------------------------------
// fadeUp — Default entrance for general elements
// --------------------------------------------------
export const fadeUp = {
  from: { y: 30, autoAlpha: 0 },
  to: { y: 0, autoAlpha: 1, duration: 0.8, ease: easings.smooth },
  scrollTrigger: { start: "top 85%", end: "top 20%", toggleActions: "play none none none" },
};

// --------------------------------------------------
// textRevealLines — Masked line-by-line reveal via SplitText
// Used for: all headings and display text
// --------------------------------------------------
// Implementation: SplitText with mask: "lines", autoSplit: true
// Each line container clips overflow, lines translate from yPercent: 100 → 0
export const textRevealLines = {
  splitConfig: { type: "lines", mask: "lines", autoSplit: true },
  from: { yPercent: 100 },
  to: { yPercent: 0, duration: 1.0, ease: easings.smooth, stagger: 0.12 },
  scrollTrigger: { start: "top 85%", toggleActions: "play none none none" },
};

// --------------------------------------------------
// textRevealWords — Word-by-word opacity scrub (for scroll-driven narrative)
// Used for: album page narrative text sections
// --------------------------------------------------
export const textRevealWords = {
  splitConfig: { type: "words" },
  from: { opacity: 0.15 },
  to: { opacity: 1, stagger: 0.05 },
  scrollTrigger: { start: "top 80%", end: "bottom 20%", scrub: 1 },
};

// --------------------------------------------------
// clipRevealUp — Clip-path wipe from bottom
// --------------------------------------------------
export const clipRevealUp = {
  from: { clipPath: "inset(100% 0% 0% 0%)" },
  to: { clipPath: "inset(0% 0% 0% 0%)", duration: 1.0, ease: easings.smooth },
  scrollTrigger: { start: "top 85%", toggleActions: "play none none none" },
};

// --------------------------------------------------
// clipRevealLeft — Clip-path wipe from left
// --------------------------------------------------
export const clipRevealLeft = {
  from: { clipPath: "inset(0% 100% 0% 0%)" },
  to: { clipPath: "inset(0% 0% 0% 0%)", duration: 1.0, ease: easings.smooth },
  scrollTrigger: { start: "top 85%", toggleActions: "play none none none" },
};

// --------------------------------------------------
// imageReveal — Two-part: colored overlay slides away, image scales down
// The overlay uses brand accent #7B93B0
// --------------------------------------------------
export const imageReveal = {
  overlay: {
    from: { scaleX: 1, transformOrigin: "left center" },
    to: { scaleX: 0, transformOrigin: "right center", duration: 0.8, ease: easings.smoothInOut },
  },
  image: {
    from: { scale: 1.3 },
    to: { scale: 1, duration: 1.2, ease: easings.smooth },
  },
  overlayColor: "#7B93B0",
  scrollTrigger: { start: "top 80%", toggleActions: "play none none none" },
};

// --------------------------------------------------
// imageLoadReveal — Blur-up lazy load entrance
// Used for: all lazy-loaded images site-wide
// --------------------------------------------------
export const imageLoadReveal = {
  // Implemented via next/image blurDataURL + CSS transition
  // on load: transition from blur(20px) scale(1.1) → blur(0) scale(1)
  cssTransition: "filter 0.6s var(--ease-out), transform 0.6s var(--ease-out)",
  loadedStyle: { filter: "blur(0)", transform: "scale(1)" },
  placeholderStyle: { filter: "blur(20px)", transform: "scale(1.1)" },
};

// --------------------------------------------------
// parallaxLayer — Scroll-linked Y translation
// --------------------------------------------------
export const parallaxLayer = (speed: number = 0.15) => ({
  to: { yPercent: speed * 100, ease: "none" },
  scrollTrigger: { start: "top bottom", end: "bottom top", scrub: true },
});

// --------------------------------------------------
// magneticPull — Cursor proximity detection for buttons
// --------------------------------------------------
export const magneticPull = {
  proximityRadius: 100, // px — activation zone
  strength: 0.3,        // 0–1 — how far the element moves toward cursor
  ease: "power3.out",
  returnDuration: 0.5,
  returnEase: "elastic.out(1, 0.3)",
};

// --------------------------------------------------
// staggerGrid — Staggered entrance for card grids
// --------------------------------------------------
export const staggerGrid = {
  from: { y: 40, autoAlpha: 0 },
  to: { y: 0, autoAlpha: 1, duration: 0.8, ease: easings.smooth, stagger: 0.1 },
  scrollTrigger: { start: "top 85%", toggleActions: "play none none none" },
};

// --------------------------------------------------
// marqueeLoop — Infinite horizontal scroll
// --------------------------------------------------
export const marqueeLoop = {
  duration: 20,    // seconds for one full loop
  ease: "none",
  repeat: -1,
};

// --------------------------------------------------
// navOverlayOpen — Full-screen navigation entrance
// --------------------------------------------------
export const navOverlayOpen = {
  backdrop: { from: { autoAlpha: 0 }, to: { autoAlpha: 1, duration: 0.4, ease: easings.smooth } },
  menuItems: {
    splitConfig: { type: "lines", mask: "lines" },
    from: { yPercent: 100 },
    to: { yPercent: 0, duration: 0.8, ease: easings.smooth, stagger: 0.08 },
    delay: 0.2,
  },
};

// --------------------------------------------------
// navOverlayClose — Reverse of open
// --------------------------------------------------
export const navOverlayClose = {
  menuItems: {
    to: { yPercent: -100, duration: 0.5, ease: easings.smoothInOut, stagger: 0.04 },
  },
  backdrop: { to: { autoAlpha: 0, duration: 0.3, ease: easings.smoothInOut, delay: 0.2 } },
};

// --------------------------------------------------
// scrollIndicatorPulse — Vertical "Scroll" text with animated line
// --------------------------------------------------
export const scrollIndicatorPulse = {
  line: {
    from: { scaleY: 0, transformOrigin: "top center" },
    to: { scaleY: 1, duration: 0.8, ease: easings.smoothInOut, repeat: -1, yoyo: true, repeatDelay: 0.5 },
  },
  text: {
    from: { autoAlpha: 0 },
    to: { autoAlpha: 1, duration: 0.6, ease: easings.smooth, delay: 0.5 },
  },
};

// --------------------------------------------------
// pageTransitionLeave — Overlay wipe exit (default for all routes)
// --------------------------------------------------
export const pageTransitionLeave = {
  overlay: {
    from: { scaleX: 0, transformOrigin: "left center" },
    to: { scaleX: 1, duration: 0.5, ease: easings.smoothInOut },
  },
  content: {
    to: { autoAlpha: 0, y: -20, duration: 0.3, ease: easings.smooth },
  },
  totalDuration: 0.5,
};

// --------------------------------------------------
// pageTransitionEnter — Overlay wipe reveal + content entrance
// --------------------------------------------------
export const pageTransitionEnter = {
  overlay: {
    from: { scaleX: 1, transformOrigin: "right center" },
    to: { scaleX: 0, duration: 0.5, ease: easings.smoothInOut, delay: 0.1 },
  },
  totalDuration: 0.6,
};

// --------------------------------------------------
// sharedElementMorph — GSAP Flip for Work → Album transition
// --------------------------------------------------
export const sharedElementMorph = {
  flipDuration: 0.9,
  flipEase: easings.smoothInOut,
  // Steps:
  // 1. Capture Flip state of album card thumbnail
  // 2. Store in Zustand morphSource
  // 3. On album page mount, Flip.from() the hero image to morph from card position
  // Fallback: if morphSource is null (direct URL visit), skip Flip and use standard enter
};

// --------------------------------------------------
// heroSequence — Homepage hero load-in choreography
// Total duration: ~3.5s
// --------------------------------------------------
export const heroSequence = {
  steps: [
    // Step 1: Film grain fades in (0s–0.3s)
    { target: ".grain-overlay", from: { autoAlpha: 0 }, to: { autoAlpha: 1, duration: 0.3 } },
    // Step 2: Horizontal rule draws from center (0.2s–0.8s)
    { target: ".hero-rule", from: { scaleX: 0 }, to: { scaleX: 1, duration: 0.6, ease: "power2.inOut" }, position: 0.2 },
    // Step 3: "BAMYAN" SplitText line reveal (0.5s–1.7s)
    { target: ".hero-title", animation: "textRevealLines", stagger: 0.12, position: 0.5 },
    // Step 4: "STORYWORKS" fade up with letter-spacing animation (1.2s–2.0s)
    { target: ".hero-subtitle-brand", from: { autoAlpha: 0, letterSpacing: "0.3em" }, to: { autoAlpha: 1, letterSpacing: "0.08em", duration: 0.8, ease: "power3.out" }, position: 1.2 },
    // Step 5: Background photograph breathes in (1.4s–2.6s)
    { target: ".hero-image", from: { autoAlpha: 0, scale: 1.15 }, to: { autoAlpha: 1, scale: 1, duration: 1.2, ease: "power2.out" }, position: 1.4 },
    // Step 6: Tagline fades up (2.2s–2.7s)
    { target: ".hero-tagline", from: { autoAlpha: 0, y: 20 }, to: { autoAlpha: 1, y: 0, duration: 0.5, ease: "power3.out" }, position: 2.2 },
    // Step 7: Scroll indicator appears (2.8s–3.5s)
    { target: ".scroll-indicator", animation: "scrollIndicatorPulse", position: 2.8 },
  ],
  totalDuration: 3.5,
};

// --------------------------------------------------
// reducedMotionFallbacks — prefers-reduced-motion: reduce behavior
// --------------------------------------------------
export const reducedMotionFallbacks = {
  fadeUp: "instant autoAlpha: 1, no Y translation",
  textRevealLines: "instant autoAlpha: 1, no SplitText, no mask animation",
  textRevealWords: "all words at full opacity, no scrub",
  clipRevealUp: "instant clip-path: inset(0%), no animation",
  clipRevealLeft: "instant clip-path: inset(0%), no animation",
  imageReveal: "no overlay animation, image visible immediately at scale 1",
  imageLoadReveal: "no blur transition, image appears immediately",
  parallaxLayer: "disabled — no Y translation",
  magneticPull: "disabled — no cursor following",
  staggerGrid: "instant autoAlpha: 1, no stagger delay",
  marqueeLoop: "static, no scroll — content visible in place",
  navOverlayOpen: "instant visibility toggle, no stagger",
  navOverlayClose: "instant visibility toggle",
  scrollIndicatorPulse: "visible but static, no pulse animation",
  pageTransitionLeave: "instant opacity 0, no overlay wipe",
  pageTransitionEnter: "instant opacity 1, no overlay reveal",
  sharedElementMorph: "disabled — standard instant transition",
  heroSequence: "all elements visible immediately, no choreography",
  horizontalScrollGallery: "converted to vertical flowing gallery, no pin",
};
```

---

## 4. Component Specification

### Layout Components

#### `Header`
- **Type:** Client Component (`'use client'`)
- **Reasoning:** Requires scroll detection for sticky blur, manages menu state via Zustand
- **Props Interface:**
  ```typescript
  interface HeaderProps {
    className?: string;
  }
  ```
- **Behavior:** Fixed position. Wordmark "BAMYAN" in Instrument Serif (left) + text links "Work", "About", "Contact" (right) in Satoshi. Background: transparent at top → `backdrop-filter: blur(12px)` + `background: rgba(12, 15, 20, 0.6)` after scrolling past 100vh. Nav links have underline draw-on animation from left on hover (2px thickness, `var(--color-primary)`, 4px offset below text).
- **Animation:** ScrollTrigger detects scroll position. Blur backdrop activates via CSS class toggle. Links use `clipPath` or `scaleX` underline animation on hover.
- **Responsive:** On `< md` (768px): wordmark left + hamburger icon right. Text links hidden. Hamburger triggers `menuOpen` in Zustand.
- **Accessibility:** `<header>` landmark, `<nav>` with `aria-label="Main navigation"`, links are `<a>` tags via Next.js `<Link>`. Hamburger has `aria-expanded`, `aria-controls="mobile-menu"`.

#### `MobileMenu`
- **Type:** Client Component
- **Props Interface:**
  ```typescript
  interface MobileMenuProps {
    // No required props — reads menuOpen from Zustand
  }
  ```
- **Behavior:** Full-screen overlay (`position: fixed; inset: 0; z-index: 50`). Dark background `var(--color-overlay)`. "BAMYAN" wordmark centered top. Menu items stacked vertically in Instrument Serif at `--text-4xl`. Social links at bottom in JetBrains Mono at `--text-sm`.
- **Animation:** Uses `navOverlayOpen` / `navOverlayClose` presets. Backdrop fades in, menu items reveal line-by-line via SplitText mask. Close reverses with faster timing.
- **Responsive:** Only renders on `< md`. Body scroll locked via Lenis `.stop()` when open.
- **Accessibility:** `id="mobile-menu"`, `role="dialog"`, `aria-modal="true"`, focus trap (first focusable on open, return focus to hamburger on close). Escape key closes.

#### `Footer`
- **Type:** Server Component (static content) wrapping a Client Component for hover animation
- **Props Interface:**
  ```typescript
  interface FooterProps {
    className?: string;
  }
  ```
- **Behavior:** Large typographic CTA: "Let's work together" in Instrument Serif at `--text-footer-cta` (80px). On hover, the text shifts color to `var(--color-primary)` and a magnetic pull effect activates. Below: email link + social icon links (Instagram, Behance, LinkedIn) in a horizontal row. Copyright line at very bottom in JetBrains Mono `--text-xs`.
- **Animation:** CTA text uses `textRevealLines` on scroll enter. Social links stagger `fadeUp`.
- **Responsive:** CTA scales to `--text-3xl` on mobile. Social icons remain horizontal.
- **Accessibility:** `<footer>` landmark. CTA is an `<a href="mailto:...">`. Social links have `aria-label` (e.g., "Instagram profile").

#### `TransitionOverlay`
- **Type:** Client Component
- **Props Interface:**
  ```typescript
  interface TransitionOverlayProps {
    // No required props — reads from Zustand: isTransitioning, transitionType
  }
  ```
- **Behavior:** Full-screen overlay element (`position: fixed; inset: 0; z-index: 100`). Background: `var(--color-overlay-solid)`. Hidden by default (`scaleX: 0`). Activated by Zustand `isTransitioning` state.
  - When `transitionType === 'wipe'`: overlay scales from left → full → reveals from right.
  - When `transitionType === 'morph'`: overlay is transparent, GSAP Flip handles the visual transition of the card-to-hero morph.
- **Animation:** Uses `pageTransitionLeave` and `pageTransitionEnter` presets.

#### `TransitionLink`
- **Type:** Client Component
- **Props Interface:**
  ```typescript
  interface TransitionLinkProps {
    href: string;
    children: React.ReactNode;
    transitionType?: "wipe" | "morph";
    flipId?: string; // for shared element morph — matches data-flip-id on source element
    className?: string;
    onClick?: () => void;
    prefetch?: boolean;
  }
  ```
- **Behavior:** Wraps Next.js `<Link>`. Intercepts click: sets Zustand `isTransitioning: true` + `transitionType`, runs leave animation via GSAP timeline, calls `router.push()` after animation completes, new page runs enter animation on mount.
  - For morph transitions: captures GSAP `Flip.getState()` of the source element before navigation, stores in Zustand `morphSource`.
- **Hooks:** `useRouter`, `useUIStore`, `useGSAP`
- **Prefetch:** `<Link prefetch>` enabled by default. On hover, additionally triggers `router.prefetch()` for aggressive preloading.

#### `ScrollIndicator`
- **Type:** Client Component
- **Props Interface:**
  ```typescript
  interface ScrollIndicatorProps {
    className?: string;
  }
  ```
- **Behavior:** Positioned absolute bottom-right of hero. Vertical "Scroll" text in JetBrains Mono `--text-xs` rotated 90°, with a thin animated line below extending downward.
- **Animation:** Uses `scrollIndicatorPulse` preset. Fades out when user scrolls past hero (ScrollTrigger toggle at 50vh).
- **Responsive:** Hidden on mobile (`< md`).
- **Accessibility:** `aria-hidden="true"` — decorative element.

#### `BackToTop`
- **Type:** Client Component
- **Behavior:** Small circular button, fixed bottom-right, appears after scrolling past 100vh. Click triggers `lenis.scrollTo(0)`.
- **Animation:** Fade in/out based on scroll position via ScrollTrigger.
- **Accessibility:** `<button aria-label="Back to top">`, keyboard focusable.

### Animation Wrapper Components

#### `FadeIn`
- **Type:** Client Component
- **Props Interface:**
  ```typescript
  interface FadeInProps {
    children: React.ReactNode;
    delay?: number;      // default: 0
    duration?: number;    // default: 0.8
    y?: number;           // default: 30
    className?: string;
    as?: React.ElementType; // default: "div"
  }
  ```
- **Behavior:** Wraps children with scroll-triggered fade + Y translate entrance.
- **Animation:** `useGSAP` with ScrollTrigger. Uses `fadeUp` preset, overridable via props.
- **Reduced Motion:** Instant `autoAlpha: 1`, no translation.

#### `TextReveal`
- **Type:** Client Component
- **Props Interface:**
  ```typescript
  interface TextRevealProps {
    children: React.ReactNode;
    variant?: "lines" | "words"; // default: "lines"
    stagger?: number;           // default: 0.12 for lines, 0.05 for words
    scrub?: boolean;            // default: false (true enables scroll-linked)
    className?: string;
    as?: React.ElementType;      // default: "div"
  }
  ```
- **Behavior:** Applies SplitText to children text. `lines` variant uses `textRevealLines` preset (masked). `words` variant uses `textRevealWords` preset (opacity scrub).
- **Animation:** `useGSAP` + SplitText with `autoSplit: true` for responsive reflow.
- **Reduced Motion:** Text immediately visible, no split animation.

#### `ImageReveal`
- **Type:** Client Component
- **Props Interface:**
  ```typescript
  interface ImageRevealProps {
    children: React.ReactNode; // should be a next/image
    overlayColor?: string;     // default: "#7B93B0"
    className?: string;
  }
  ```
- **Behavior:** Wraps an image with a colored overlay div. On scroll enter: overlay slides away (scaleX 1→0), then image scales from 1.3→1.
- **Animation:** Uses `imageReveal` preset. Two-step GSAP timeline triggered by ScrollTrigger.
- **Reduced Motion:** No overlay, image at scale 1 immediately.

#### `ParallaxSection`
- **Type:** Client Component
- **Props Interface:**
  ```typescript
  interface ParallaxSectionProps {
    children: React.ReactNode;
    speed?: number;      // default: 0.15 — multiplier for Y translation
    className?: string;
  }
  ```
- **Behavior:** Applies scroll-linked Y translation to children for depth effect.
- **Animation:** Uses `parallaxLayer(speed)` preset. ScrollTrigger scrub.
- **Responsive:** Disabled on mobile (`< md`) via `gsap.matchMedia()`. Disabled when `prefers-reduced-motion: reduce`.

#### `MagneticButton`
- **Type:** Client Component
- **Props Interface:**
  ```typescript
  interface MagneticButtonProps {
    children: React.ReactNode;
    radius?: number;       // default: 100
    strength?: number;     // default: 0.3
    as?: React.ElementType; // default: "button"
    className?: string;
    [key: string]: any;    // pass-through HTML attributes
  }
  ```
- **Behavior:** Tracks mouse position relative to element center. When cursor is within `radius`, element translates toward cursor by `strength` factor. On mouse leave, springs back with elastic easing.
- **Animation:** Uses `magneticPull` preset values. `gsap.to()` on mousemove, `gsap.to()` with elastic return on mouseleave.
- **Responsive:** Disabled on touch devices (detected via `matchMedia('(hover: hover)')`).
- **Accessibility:** Underlying element remains a `<button>` or `<a>` — magnetic effect is purely visual.

### Section Components

#### `Hero`
- **Type:** Client Component
- **Props Interface:**
  ```typescript
  interface HeroProps {
    image: SanityImageSource;
    blurDataURL: string;
  }
  ```
- **Behavior:** Full-viewport section (`height: 100vh`). Background: moody photograph via `next/image` with `priority`, `fill`, `sizes="100vw"`, `placeholder="blur"`, `blurDataURL`. Overlaid: "BAMYAN" in Instrument Serif at `--text-hero`, "STORYWORKS" below at `--text-2xl` with wide letter-spacing, tagline in Satoshi at `--text-lg`. Thin horizontal rule (`1px height, var(--color-primary)`) between title and subtitle.
- **Animation:** Full `heroSequence` timeline on mount via `useGSAP`. ScrollIndicator at bottom-right.
- **Responsive:** Title scales to `--text-hero-mobile` on mobile. Layout adjusts to center-aligned stack. Image covers viewport.
- **Accessibility:** `<section aria-label="Hero">`. Image has descriptive `alt`. Title hierarchy: `<h1>` for "BAMYAN STORYWORKS".

#### `ProjectGrid`
- **Type:** Server Component shell with Client Component cards
- **Props Interface:**
  ```typescript
  interface ProjectGridProps {
    projects: ProjectMeta[];
    columns?: 2 | 3;       // default: 2
    featured?: boolean;     // default: false — if true, first item spans full width
  }
  ```
- **Behavior:** CSS Grid layout. 2-column on desktop with alternating large/small card sizes for visual rhythm. Each card is a `ProjectCard`.
- **Responsive:** Single column on mobile, 2-column on tablet+.

#### `ProjectCard`
- **Type:** Client Component
- **Props Interface:**
  ```typescript
  interface ProjectCardProps {
    project: ProjectMeta;
    index: number;
    size?: "large" | "default";
  }
  ```
- **Behavior:** Thumbnail image (aspect-ratio 3:4 or 16:9 depending on size) + album title + category tag (in JetBrains Mono `--text-xs`, uppercase). Wrapped in `TransitionLink` with `transitionType="morph"`.
- **Hover:** Image scales to 1.05x + color overlay (`var(--color-primary)` at 30% opacity) slides in from bottom + title text reveals on top of overlay via `clipPath` animation. Overlay must fully reverse on mouse leave before click navigation triggers.
- **Animation:** Scroll entrance via `staggerGrid` preset.
- **Responsive:** Direct tap navigates (no intermediary hover state on touch).
- **Accessibility:** Card is a link. Image has `alt` text. Title is visible text content of the link.
- **Data attribute:** `data-flip-id={project.slug}` on the image container for GSAP Flip morph.

#### `AboutTeaser`
- **Type:** Server Component
- **Props Interface:**
  ```typescript
  interface AboutTeaserProps {
    bio: string;         // one-line philosophy
    portrait: SanityImageSource;
    blurDataURL: string;
  }
  ```
- **Behavior:** Two-column layout: portrait image (left, wrapped in `ImageReveal`) + philosophy text in Instrument Serif at `--text-3xl` (right, wrapped in `TextReveal variant="lines"`). "About me →" link below text.
- **Responsive:** Stacked single column on mobile, image on top.

#### `CategoriesStrip`
- **Type:** Client Component
- **Behavior:** Horizontal strip showing four photography types: Personal, Event, Sports, Solo. Each category is a text label in Satoshi `--text-xl` with a thin divider line between them. Optionally each has a small thumbnail that reveals on hover.
- **Animation:** Labels stagger in via `fadeUp`. On hover, labels shift to `var(--color-primary)`.
- **Responsive:** Wraps to 2×2 grid on mobile.

#### `ContactCTA`
- **Type:** Server Component shell with Client animation wrapper
- **Behavior:** Large "Let's work together" in Instrument Serif at `--text-5xl`, centered. Below: email link in Satoshi. Wrapped in `TextReveal variant="lines"`.
- **Responsive:** Scales to `--text-3xl` on mobile.

#### `HorizontalScrollGallery`
- **Type:** Client Component
- **Props Interface:**
  ```typescript
  interface HorizontalScrollGalleryProps {
    images: SanityImage[];
    blurDataURLs: string[];
  }
  ```
- **Behavior:** Pinned section. Images arranged in a horizontal strip. As user scrolls vertically, the strip scrolls horizontally. Each image is full viewport height, auto width maintaining aspect ratio. Spacing between images: `--space-4`.
- **Animation:** ScrollTrigger with `pin: true`, `scrub: 1`. Total scroll distance = (number of images × viewport width). Horizontal `x` translation from 0 → negative total width.
- **Responsive:** On mobile (`< md`), converts to vertical flowing gallery — no pin, no horizontal scroll. Images stack vertically at full width with `--space-8` between them.
- **Accessibility:** `aria-label="Photo gallery"`. Images have individual `alt` text from Sanity.

#### `NarrativeSection`
- **Type:** Client Component
- **Props Interface:**
  ```typescript
  interface NarrativeSectionProps {
    text: string;
    className?: string;
  }
  ```
- **Behavior:** Album narrative text displayed in Satoshi at `--text-xl` to `--text-2xl`. Text is split into words. As user scrolls through the section, words transition from `opacity: 0.15` to `opacity: 1` progressively — creating a reading/highlighting effect scrubbed to scroll position.
- **Animation:** Uses `textRevealWords` preset with `scrub: 1`. ScrollTrigger start/end spans the full section.
- **Reduced Motion:** All words at full opacity immediately. No scroll scrub.

### UI Components

#### `Button`
- **Type:** Client Component
- **Props Interface:**
  ```typescript
  interface ButtonProps {
    children: React.ReactNode;
    variant?: "primary" | "outline" | "ghost"; // default: "primary"
    size?: "sm" | "md" | "lg";                 // default: "md"
    magnetic?: boolean;                         // default: true
    href?: string;                              // renders as <a> if provided
    className?: string;
    [key: string]: any;
  }
  ```
- **Behavior:**
  - `primary`: Background `var(--color-primary)`, text `var(--color-background)`.
  - `outline`: Border `1px solid var(--color-border)`, transparent bg.
  - `ghost`: No border, no background.
- **Hover:** Border animation — dashed border rotates (for outline) or solid border draws around element. Magnetic pull effect when `magnetic: true`.
- **Accessibility:** Renders `<button>` by default, `<a>` if `href` provided. Focusable, `focus-visible` ring uses `var(--color-primary)`.

#### `Input` / `Textarea`
- **Type:** Client Component
- **Props Interface:**
  ```typescript
  interface InputProps extends React.InputHTMLAttributes<HTMLInputElement> {
    label: string;
    error?: string;
  }
  interface TextareaProps extends React.TextareaHTMLAttributes<HTMLTextAreaElement> {
    label: string;
    error?: string;
  }
  ```
- **Behavior:** Bottom-border only inputs (`border-bottom: 1px solid var(--color-border)`). On focus: animated underline draws from left in `var(--color-primary)` via `scaleX(0→1)` CSS transition. Label above in Satoshi `--text-sm`. Error message below in red-ish accent.
- **Accessibility:** `<label>` associated via `htmlFor`/`id`. Error linked via `aria-describedby`. `aria-invalid="true"` when error present.

#### `ContactForm`
- **Type:** Client Component
- **Props Interface:**
  ```typescript
  interface ContactFormProps {
    // No required props — uses Server Action directly
  }
  ```
- **Fields:** name (text), email (email), photographyType (select: Personal / Event / Sports / Solo), preferredDate (date), message (textarea).
- **Validation:** Zod schema validates client-side on blur and on submit. Server Action revalidates with same Zod schema.
- **Submission:** React Server Action → Resend API. Success state: form content fades out, "Thank you" message fades in with `fadeUp`. Error state: inline error messages per field + toast at top.
- **Animation:** Form fields stagger entrance via `fadeUp` with 0.08s stagger. Focus underline draws on.

#### `Cursor`
- **Not implemented.** Decision: native pointer cursor for all interactions. No custom cursor component needed.

---

## 5. Page-by-Page Blueprint

### Home Page (`/`)

**Server Component** — assembles sections, fetches featured projects from Sanity.

**Data Requirements:**
- Sanity GROQ query: `*[_type == "album" && featured == true] | order(order asc)[0...4] { _id, title, slug, category, coverImage, "blurDataURL": coverImage.asset->metadata.lqip }`
- Hero image from Sanity singleton document or config
- About teaser: short bio + portrait from Sanity "about" singleton

**SEO:**
- Title: `"Bamyan Storyworks — Photography for moments that matter"`
- Description: `"Cinematic photography studio specializing in personal, event, sports, and solo photography."`
- OG Image: static branded image or dynamic generation
- JSON-LD: `LocalBusiness` schema with `photographer` type

**Section-by-Section Scroll Timeline:**

1. **Hero** (0vh–100vh)
   - On page load (not scroll-triggered): `heroSequence` timeline plays
   - Film grain fades in (0s–0.3s)
   - Horizontal rule draws from center (0.2s–0.8s)
   - "BAMYAN" SplitText masked line reveal, stagger 0.12s (0.5s–1.7s)
   - "STORYWORKS" fade up + letter-spacing 0.3em→0.08em (1.2s–2.0s)
   - Background photograph fade + scale 1.15→1 (1.4s–2.6s)
   - Tagline fade up in Satoshi (2.2s–2.7s)
   - Scroll indicator appears and begins pulse (2.8s–3.5s)
   - At scroll position 50vh: scroll indicator fades out via `fadeUp` reversed

2. **Selected Albums** (100vh–~260vh)
   - `start: "top 85%"`: Section heading "Selected Work" triggers `textRevealLines`
   - ProjectGrid with 3–4 albums. 2-column asymmetric layout.
   - Each ProjectCard triggers at `start: "top 85%"` with `staggerGrid` (stagger 0.1s)
   - Card images use `imageReveal` (blue overlay + scale)
   - Hover: scale 1.05x + overlay slide-in with title

3. **About Teaser** (~260vh–~360vh)
   - `start: "top 80%"`: Portrait triggers `imageReveal` on left
   - `start: "top 75%"`: Philosophy text triggers `textRevealLines` on right, stagger 0.12s
   - "About me →" link fades up with `fadeUp`, 0.3s delay after text
   - Subtle `parallaxLayer(0.08)` on portrait image for depth

4. **Categories Strip** (~360vh–~420vh)
   - `start: "top 85%"`: Labels stagger in via `fadeUp`, stagger 0.08s
   - Thin horizontal lines between categories draw on via `clipRevealLeft`

5. **Contact CTA** (~420vh–~500vh)
   - `start: "top 80%"`: "Let's work together" triggers `textRevealLines`
   - Email link fades up with `fadeUp`, 0.2s delay
   - `MagneticButton` effect on the CTA text

6. **Footer** (~500vh–end)
   - Social links stagger `fadeUp`, stagger 0.06s
   - Copyright fades in

---

### Work Page (`/work`)

**Server Component** — fetches all albums.

**Data Requirements:**
- Sanity GROQ: `*[_type == "album"] | order(order asc) { _id, title, slug, category, year, location, coverImage, "blurDataURL": coverImage.asset->metadata.lqip }`

**SEO:**
- Title: `"Work — Bamyan Storyworks"`
- Description: `"Browse photography albums spanning personal, event, sports, and solo sessions."`
- JSON-LD: `CollectionPage` with `hasPart` array of album items

**Scroll Timeline:**

1. **Page Header** (0vh–40vh)
   - "Work" heading in Instrument Serif `--text-6xl`, `textRevealLines` on enter
   - Optional filter tabs (All / Personal / Event / Sports / Solo) in Satoshi, fade up with stagger

2. **Album Grid** (40vh–end)
   - Full ProjectGrid with all 5–6 albums
   - 2-column asymmetric layout with alternating large/default card sizes
   - Cards trigger `staggerGrid` at `start: "top 85%"`
   - Each card has `data-flip-id={slug}` for shared element morph transition

---

### Album Page (`/work/[slug]`)

**Server Component** with Client Component sections for scroll animations.

**Data Requirements:**
- Sanity GROQ: `*[_type == "album" && slug.current == $slug][0] { title, slug, category, year, location, description, heroImage, "heroBlur": heroImage.asset->metadata.lqip, images[] { asset->, alt, caption, "blurDataURL": asset->metadata.lqip }, narrative }`
- `generateStaticParams`: `*[_type == "album"] { "slug": slug.current }`
- `generateMetadata`: dynamic title, description, OG image from album data

**SEO:**
- Title: `"[Album Title] — Bamyan Storyworks"`
- OG Image: album hero image
- JSON-LD: `ImageGallery` schema with `associatedMedia` array

**Scroll Timeline — The Cinematic Core:**

1. **Album Hero** (0vh–100vh)
   - Full-bleed hero image at `100vh` height
   - If arriving via morph transition: `Flip.from()` morphs the card thumbnail into this hero image over 0.9s with `power2.inOut`
   - If arriving via wipe transition or direct URL: image uses `imageReveal` (overlay + scale)
   - Album title in Instrument Serif `--text-5xl` triggers `textRevealLines` at 0.3s delay
   - Metadata (category • year • location) in JetBrains Mono `--text-sm`, uppercase, fades up

2. **Narrative Intro** (100vh–200vh)
   - Album description paragraphs in Satoshi `--text-xl`
   - Uses `NarrativeSection` — words highlight from 15% → 100% opacity scrubbed to scroll
   - ScrollTrigger: `start: "top 80%"`, `end: "bottom 20%"`, `scrub: 1`

3. **Horizontal Scroll Gallery** (200vh–pinned for ~Nvw)
   - `HorizontalScrollGallery` component pinned to viewport
   - N images (8–20) arranged horizontally, each at viewport height
   - Scroll distance: N × 100vw
   - Images use `imageLoadReveal` (blur-up) as they enter the horizontal viewport
   - On mobile: converts to vertical gallery, no pin
   - ScrollTrigger: `pin: true`, `scrub: 1`, `end: () => "+=" + (images.length * window.innerWidth)`

4. **Additional Narrative Blocks** (interspersed if album has multiple text sections)
   - Same `NarrativeSection` treatment
   - `ImageReveal` for standalone highlight images between text blocks

5. **Next/Previous Navigation** (end of album)
   - Two-column: "Previous Album" (left) + "Next Album" (right)
   - Each shows the album title + small thumbnail
   - Wrapped in `TransitionLink` with `transitionType="wipe"`
   - Entrance: `fadeUp` with stagger

---

### About Page (`/about`)

**Server Component** with Client animation wrappers.

**Data Requirements:**
- Sanity GROQ: `*[_type == "about"][0] { bio, portrait, "portraitBlur": portrait.asset->metadata.lqip, approach, services[], socialLinks[] }`

**SEO:**
- Title: `"About — Bamyan Storyworks"`
- JSON-LD: `Person` schema with `jobTitle: "Photographer"`

**Scroll Timeline:**

1. **Hero Section** (0vh–80vh)
   - Portrait image (large, ~60% viewport width) with `imageReveal`
   - Name + title in Instrument Serif, `textRevealLines`

2. **Bio** (80vh–180vh)
   - Full bio text in Satoshi `--text-lg` to `--text-xl`
   - `TextReveal variant="lines"` on scroll enter

3. **Approach / Philosophy** (180vh–260vh)
   - Short statement about photographic approach
   - Could use `NarrativeSection` (word highlight scrub) for emphasis

4. **Services / What I Shoot** (260vh–340vh)
   - Four categories (Personal, Event, Sports, Solo) with brief descriptions
   - 2×2 grid, each card fades in via `staggerGrid`
   - Each category has a representative thumbnail image

5. **Social Links** (340vh–380vh)
   - Links to Instagram, Behance, LinkedIn
   - Staggered `fadeUp` entrance

---

### Contact Page (`/contact`)

**Server Component** wrapping Client Component form.

**Data Requirements:**
- Minimal — form is client-side, submission via Server Action

**SEO:**
- Title: `"Contact — Bamyan Storyworks"`
- JSON-LD: `ContactPage`

**Scroll Timeline:**

1. **Heading** (0vh–30vh)
   - "Let's create something" in Instrument Serif `--text-5xl`, `textRevealLines`
   - Short description in Satoshi below

2. **Contact Form** (30vh–end)
   - `ContactForm` component with fields staggering in via `fadeUp` (stagger 0.08s)
   - Bottom-border inputs with animated underline on focus
   - Select dropdown for photography type styled to match design system
   - Submit button with `MagneticButton` + border animation on hover
   - Success: form fades out, "Thank you — I'll be in touch" fades in
   - Alongside form (right column on desktop): email address, social links, optional location text

---

## 6. Routing & Transition Map

| Route A | Route B | Transition | Timing | Special Behavior |
|---------|---------|------------|--------|-----------------|
| `/` | `/work` | Overlay wipe right | 1.0s | Standard |
| `/` | `/about` | Overlay wipe right | 1.0s | Standard |
| `/` | `/contact` | Overlay wipe right | 1.0s | Standard |
| `/work` | `/work/[slug]` | Shared element morph | 0.9s | Card thumbnail morphs to album hero via GSAP Flip. Fallback to wipe if Flip state unavailable. |
| `/work/[slug]` | `/work` | Overlay wipe left | 0.9s | Reverse direction signals "going back" |
| `/work/[slug]` | `/work/[other-slug]` | Overlay wipe right | 1.0s | Via next/prev nav at bottom of album |
| `*` | `/about` | Overlay wipe right | 1.0s | Standard |
| `*` | `/contact` | Overlay wipe right | 1.0s | Standard |
| Any (browser back) | Previous | Overlay wipe left | 0.9s | Detected via `popstate` event, reverse direction |
| Any (browser forward) | Next | Overlay wipe right | 0.9s | Standard direction |

### TransitionLink + Zustand Implementation Architecture

```
Click TransitionLink
  → Set Zustand: isTransitioning: true, transitionType
  → If morph: Flip.getState(sourceElement) → store in morphSource
  → Run pageTransitionLeave GSAP timeline
  → On timeline complete: router.push(href)
  → New page mounts, template.tsx detects isTransitioning
  → If wipe: run pageTransitionEnter (overlay reveals from right)
  → If morph: Flip.from(morphSource) on hero image
  → On enter complete: set isTransitioning: false
```

### `<Link>` Prefetch Strategy

- All `TransitionLink` instances render with `prefetch` enabled (Next.js default)
- Album cards additionally call `router.prefetch(href)` on `mouseenter` (300ms head start)
- Next/previous album links at bottom of album pages: prefetch on viewport enter via ScrollTrigger callback

### Browser Back/Forward

- Listen for `popstate` event in the TransitionOverlay component
- On `popstate`: run `pageTransitionLeave` with leftward direction, let the browser handle the route change, run `pageTransitionEnter` on the arriving page
- Edge case: if `isTransitioning` is already true during popstate (rapid back/forward), skip animation and let navigation proceed instantly

---

## 7. Performance Optimization Plan

### LazyMotion Setup

```typescript
// lib/motion-features.ts
import { domAnimation } from "motion/react";
export default domAnimation;

// providers/Providers.tsx
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

This reduces Motion's bundle from ~30–50KB to ~5KB.

### Dynamic Imports (`next/dynamic`)

| Component | `ssr: false` | Reason |
|-----------|-------------|--------|
| `MobileMenu` | Yes | Only needed on interaction, heavy animation |
| `HorizontalScrollGallery` | Yes | Heavy ScrollTrigger pin logic, only on album pages |
| `ContactForm` | No | SSR for SEO, hydrates on client |
| Sanity Studio (`/studio`) | Yes | Entirely client-side admin interface |

### `next/image` Configuration

```typescript
// next.config.ts (partial)
images: {
  formats: ["image/avif", "image/webp"],
  deviceSizes: [640, 768, 1024, 1280, 1536, 1920],
  imageSizes: [16, 32, 48, 64, 96, 128, 256, 384],
  minimumCacheTTL: 60 * 60 * 24 * 365, // 1 year
  remotePatterns: [
    { protocol: "https", hostname: "cdn.sanity.io" },
  ],
},
```

### Font Loading

- Instrument Serif: `next/font/google`, `display: "swap"`, `preload: true` — hero font, LCP-critical
- Satoshi: `next/font/local` from self-hosted `.woff2`, `display: "swap"`, `preload: true` — body font
- JetBrains Mono: `next/font/local`, `preload: false` — accent font, not critical for FCP

### Bundle Splitting Strategy

| Route | Libraries Loaded |
|-------|-----------------|
| All routes | GSAP core (~10KB), ScrollTrigger (~15KB), Lenis (~5KB), Motion LazyMotion (~5KB), Zustand (~1KB) = ~36KB baseline |
| Home | + SplitText (hero animation) |
| Work | + Flip (shared element morph on card click) |
| Work/[slug] | + SplitText + Flip (narrative text + potential morph arrival) |
| About | + SplitText |
| Contact | + Zod (~5KB) for form validation |
| /studio | Sanity Studio loaded independently (~500KB+, isolated route) |

### `will-change` Plan

- **Apply before animation starts:** add `will-change: transform, opacity` via GSAP's `onStart` callback or CSS class toggle 100ms before animation
- **Remove after animation completes:** remove via `onComplete` callback
- **Never leave permanently:** `will-change: auto` is the resting state
- Exception: `TransitionOverlay` can keep `will-change: transform` since it's persistent and animates frequently

### Critical CSS Path (Above the Fold)

First paint must include:
1. `var(--color-background)` on `html` (prevent white flash)
2. Font declarations for Instrument Serif and Satoshi
3. Header styles (fixed position, layout)
4. Hero container styles (full viewport, image container, text positioning)
5. Film grain overlay CSS

### Server Component vs Client Component Decision Tree

| Use Server Component when... | Use Client Component when... |
|------------------------------|------------------------------|
| Content is static or fetched at build/request time | Component uses `useState`, `useEffect`, `useRef` |
| No browser APIs needed | Requires GSAP, Motion, Lenis, or any animation |
| No event handlers | Has click, hover, scroll, or form handlers |
| Rendering HTML from CMS data | Reads from Zustand store |
| Layout shells and page assemblies | Interactive UI (buttons, forms, menus) |

**In practice for this project:**
- Server: `page.tsx` files, `layout.tsx`, `AboutTeaser` content, `ProjectGrid` shell
- Client: all animation wrappers, `Header`, `MobileMenu`, `ProjectCard`, `Hero`, `ContactForm`, `HorizontalScrollGallery`, `NarrativeSection`, `TransitionOverlay`, `TransitionLink`, `ScrollIndicator`, `BackToTop`

### Core Web Vitals Targets

| Metric | Target | Measurement Tool |
|--------|--------|-----------------|
| LCP | < 2.5s | Vercel Speed Insights, Lighthouse |
| FID/INP | < 200ms | Chrome CrUX, Lighthouse |
| CLS | < 0.1 | Lighthouse, Web Vitals JS library |
| FCP | < 1.8s | Lighthouse |
| TTI | < 3.9s | Lighthouse |
| Lighthouse Performance | ≥ 90 (mobile) | Lighthouse CI in deploy pipeline |
| JS bundle (main) | < 150KB gzipped | Bundle Analyzer |

---

## 8. Implementation Phases (Build Order)

Time estimates assume a solo experienced developer working part-time (~4–6 hours/day).

### Phase A — Foundation (2–3 days)

Scaffold Next.js 16.2 + TypeScript project. Install all dependencies. Configure `next.config.ts` with image remote patterns for Sanity, Turbopack defaults. Set up Tailwind 4 with all design tokens in `globals.css`. Configure fonts in `lib/fonts.ts`. Create `Providers.tsx` with GSAP plugin registration + Lenis (cinematic slow: `lerp: 0.06`, `duration: 1.4`, `autoRaf: false`, synced to GSAP ticker) + `LazyMotion`. Create Zustand `ui-store.ts` with initial shape. Set up `types/` directory with all interfaces. Set up Sanity project, define schemas for album and about singleton.

**Done when:** `npm run dev` shows a blank dark page with smooth scroll active, correct fonts loading, and film grain overlay visible.

### Phase B — Layout Shell (3–4 days)

Build Header (sticky, backdrop blur on scroll), Footer (large CTA, social links), Navigation (inline text links with underline animation), MobileMenu (full-screen overlay with staggered SplitText). Build TransitionOverlay and TransitionLink with Zustand integration. Wire up overlay wipe transition between stub pages. Build `proxy.ts` if needed for redirects.

**Done when:** You can navigate between stub pages (Home, Work, About, Contact) with full overlay wipe transitions, the nav works on desktop and mobile, and the header blur activates on scroll.

### Phase C — Animation Primitives (3–4 days)

Build all `components/animations/` wrappers: `FadeIn`, `TextReveal`, `ImageReveal`, `ParallaxSection`, `MagneticButton`, `ClipReveal`. Create all named presets in `lib/animations.ts` and `lib/easings.ts`. Build `ScrollIndicator` and `BackToTop`. Implement `prefers-reduced-motion` detection hook and wire fallbacks into every animation primitive.

**Done when:** Dropping `<FadeIn>` around any element produces the correct scroll-triggered entrance animation. `<TextReveal>` splits and animates text. `<ImageReveal>` plays the two-part blue overlay + scale reveal. All animations respect `prefers-reduced-motion`.

### Phase D — Home Page (3–4 days)

Build Hero with full `heroSequence` timeline. Build `ProjectGrid` and `ProjectCard` with hover effects (scale + overlay + title reveal). Build `AboutTeaser` with portrait image reveal and text reveal. Build `CategoriesStrip`. Build `ContactCTA`. Wire Sanity data fetching for featured albums and about teaser.

**Done when:** Homepage plays exactly as described in the scroll timeline — hero choreographs on load, albums reveal on scroll, about teaser has parallax depth, footer CTA is magnetic.

### Phase E — Work & Album Pages (4–5 days)

Build Work page with full album grid and filter tabs (if desired). Build album page template with: hero image (morph arrival or reveal), `NarrativeSection` (word-by-word scroll scrub), `HorizontalScrollGallery` (pinned horizontal scroll), next/prev navigation. Implement GSAP Flip shared element morph from `ProjectCard` to album hero. Wire `generateStaticParams` for SSG. Wire `generateMetadata` for dynamic SEO.

**Done when:** You can browse Home → Work → Album → Back with full animations and transitions, including the shared element morph. Horizontal gallery scrolls cinematically on desktop and converts to vertical on mobile.

### Phase F — About & Contact Pages (2–3 days)

Build About page with portrait, bio, approach section, services grid, social links. Build Contact page with `ContactForm` (Zod validation, Server Action, Resend email delivery). Style form inputs with bottom-border animation. Implement success/error states with animation.

**Done when:** All pages complete with content and animations. Contact form validates, submits, delivers email, and shows success state.

### Phase G — Responsive & Mobile (2–3 days)

Adapt all components for mobile: disable parallax, convert horizontal galleries to vertical, simplify staggers, scale typography. Implement mobile menu with full-screen overlay. Disable magnetic effects on touch. Test on real devices: iPhone Safari, Android Chrome, iPad.

**Done when:** Site works flawlessly on mobile Safari, Android Chrome, iPad, and desktop. No horizontal overflow, no broken scroll, no invisible elements.

### Phase H — Polish & Ship (2–3 days)

Performance audit: run Lighthouse, analyze bundles with Next.js Bundle Analyzer, optimize images, subset fonts. Accessibility audit: axe-core scan, keyboard navigation test, screen reader check, focus-visible styles. SEO: verify metadata on every page, generate sitemap.xml, robots.txt, OG images, JSON-LD. Build custom 404 page with on-brand animation. Favicons and web manifest. Cross-browser test (Chrome, Firefox, Safari, Edge). Deploy to Vercel. Configure Sanity webhook for ISR revalidation. Set up Google Analytics 4.

**Done when:** Lighthouse Performance ≥ 90, Accessibility ≥ 95, all checklist items pass, site is live on production domain.

**Total estimated timeline: 21–29 working days (~4–6 weeks part-time)**

---

## 9. Configuration Files

### `next.config.ts`

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

### `tsconfig.json`

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

### `package.json`

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

### `.env.example`

```bash
# Sanity CMS
NEXT_PUBLIC_SANITY_PROJECT_ID=your_project_id     # Sanity project ID (visible in sanity.io/manage)
NEXT_PUBLIC_SANITY_DATASET=production              # Sanity dataset name
NEXT_PUBLIC_SANITY_API_VERSION=2024-07-11          # Sanity API version date
SANITY_API_READ_TOKEN=your_read_token              # For server-side fetching with drafts (optional)

# Email (Resend)
RESEND_API_KEY=re_your_api_key                     # Resend API key for contact form delivery
CONTACT_EMAIL=hello@bamyanstoryworks.com           # Email address to receive contact form submissions

# Analytics
NEXT_PUBLIC_GA_MEASUREMENT_ID=G-XXXXXXXXXX         # Google Analytics 4 measurement ID

# Site
NEXT_PUBLIC_SITE_URL=https://bamyanstoryworks.com  # Production URL for metadata and OG images
```

### ESLint + Prettier

Use `eslint-config-next` as base. Recommended `.prettierrc`:

```json
{
  "semi": true,
  "singleQuote": false,
  "tabWidth": 2,
  "trailingComma": "all",
  "printWidth": 100
}
```

---

## 10. Quality Checklist

### Performance
- [ ] 60fps on all animations — verified in Chrome DevTools Performance panel (no long frames > 16ms)
- [ ] All GSAP contexts properly cleaned up on component unmount (no orphaned ScrollTriggers or tweens)
- [ ] Lighthouse Performance ≥ 90 on mobile
- [ ] `LazyMotion` configured with `domAnimation` feature set (~5KB vs ~50KB)
- [ ] All images served via `next/image` with AVIF/WebP, appropriate `sizes`, and `blurDataURL`
- [ ] Hero image has `priority` attribute for LCP optimization
- [ ] Fonts preloaded for display and body; accent font deferred
- [ ] Turbopack file system caching active for fast dev startup
- [ ] No cumulative layout shift on initial load (CLS < 0.1)
- [ ] JS bundle < 150KB gzipped (excluding animation library baseline)
- [ ] `will-change` applied only during active animations, removed on complete

### Animation & Interaction
- [ ] `prefers-reduced-motion` fully respected — all animations have meaningful fallbacks per `reducedMotionFallbacks` table
- [ ] Horizontal scroll gallery converts to vertical on mobile
- [ ] Parallax disabled on mobile
- [ ] Magnetic effects disabled on touch devices
- [ ] Album card hover overlay fully reverses before morph transition fires
- [ ] Shared element morph (Flip) works for Work → Album navigation
- [ ] Fallback to overlay wipe when morph source is unavailable (direct URL visit)
- [ ] Page transitions work correctly on browser back/forward
- [ ] Lenis smooth scroll disabled/simplified when `prefers-reduced-motion: reduce` is active

### Accessibility
- [ ] Lighthouse Accessibility ≥ 95
- [ ] Keyboard navigation functional for every interactive element
- [ ] Focus-visible styles use `var(--color-primary)` ring (not browser defaults)
- [ ] Skip-to-content link present and functional
- [ ] Mobile menu has proper focus trap and Escape key handling
- [ ] All images have descriptive `alt` text (from Sanity)
- [ ] Form inputs have associated `<label>`, `aria-describedby` for errors, `aria-invalid`
- [ ] Color contrast meets WCAG AA (checked: `#D4D4D8` on `#0C0F14` = ratio ~12.5:1 ✓)
- [ ] Contact form success/error states announced to screen readers

### SEO & Metadata
- [ ] Dynamic `metadata` export on every page
- [ ] OpenGraph images present for every page and album
- [ ] `sitemap.xml` generated via App Router metadata API
- [ ] `robots.txt` present and allows crawling (excludes `/studio`)
- [ ] JSON-LD structured data validates: `LocalBusiness` (home), `ImageGallery` (albums), `Person` (about)
- [ ] All external links have `rel="noopener noreferrer"` and open in new tab

### Cross-Browser & Devices
- [ ] Tested on real mobile devices (not just DevTools responsive mode)
- [ ] iPhone Safari — smooth scroll, transitions, galleries all functional
- [ ] Android Chrome — same
- [ ] iPad — desktop layout with touch interactions
- [ ] Desktop: Chrome, Firefox, Safari (macOS), Edge
- [ ] Tested on slow 3G network throttle — content still loads progressively

### Production Readiness
- [ ] No hydration mismatches in console
- [ ] Console is clean — zero warnings, zero errors
- [ ] Custom 404 page is on-brand with animation
- [ ] Contact form validates, submits, and shows success/error states with animation
- [ ] Favicon set: `.ico`, `apple-touch-icon.png`, `manifest.webmanifest`
- [ ] Sanity webhook configured for ISR revalidation on album publish
- [ ] Google Analytics 4 tracking active
- [ ] SSL configured on production domain
- [ ] Mobile experience is intentionally designed, not just reflowed

---

## Project Structure

```
src/
├── app/
│   ├── layout.tsx              # Root Server Component — metadata, fonts, Providers wrapper
│   ├── template.tsx            # Client Component — page enter animations (re-mounts per route)
│   ├── not-found.tsx           # Custom 404 with on-brand design + animation
│   ├── error.tsx               # Error boundary with branded fallback
│   ├── loading.tsx             # Global loading UI (Suspense fallback)
│   ├── page.tsx                # Home — assembles Hero, ProjectGrid, AboutTeaser, CategoriesStrip, ContactCTA
│   ├── work/
│   │   ├── page.tsx            # Album index — full grid of all albums
│   │   └── [slug]/page.tsx     # Album page — generateStaticParams, scroll-driven narrative
│   ├── about/page.tsx          # About — bio, approach, services, socials
│   ├── contact/
│   │   ├── page.tsx            # Contact — form + info
│   │   └── action.ts           # Server Action: Zod validate → Resend email
│   ├── studio/[[...tool]]/
│   │   └── page.tsx            # Embedded Sanity Studio
│   ├── opengraph-image.tsx     # Dynamic OG image generation
│   ├── sitemap.ts              # Dynamic sitemap from Sanity albums
│   └── robots.ts               # Robots.txt generation
├── components/
│   ├── animations/
│   │   ├── FadeIn.tsx
│   │   ├── TextReveal.tsx
│   │   ├── ImageReveal.tsx
│   │   ├── ParallaxSection.tsx
│   │   ├── MagneticButton.tsx
│   │   └── ClipReveal.tsx
│   ├── layout/
│   │   ├── Header.tsx
│   │   ├── Footer.tsx
│   │   ├── MobileMenu.tsx
│   │   ├── TransitionOverlay.tsx
│   │   ├── TransitionLink.tsx
│   │   ├── ScrollIndicator.tsx
│   │   └── BackToTop.tsx
│   ├── sections/
│   │   ├── Hero.tsx
│   │   ├── ProjectGrid.tsx
│   │   ├── ProjectCard.tsx
│   │   ├── AboutTeaser.tsx
│   │   ├── CategoriesStrip.tsx
│   │   ├── ContactCTA.tsx
│   │   ├── HorizontalScrollGallery.tsx
│   │   └── NarrativeSection.tsx
│   └── ui/
│       ├── Button.tsx
│       ├── Input.tsx
│       ├── Textarea.tsx
│       ├── Select.tsx
│       └── ContactForm.tsx
├── hooks/
│   ├── useReducedMotion.ts     # prefers-reduced-motion detection
│   ├── useMagnetic.ts          # Magnetic proximity effect
│   ├── useMediaQuery.ts        # SSR-safe responsive breakpoint detection
│   └── useLenis.ts             # Access Lenis instance for programmatic scrollTo
├── lib/
│   ├── gsap.ts                 # Centralized plugin registration
│   ├── motion-features.ts      # LazyMotion domAnimation bundle
│   ├── animations.ts           # Named preset factories
│   ├── easings.ts              # Easing token map
│   ├── fonts.ts                # next/font declarations
│   ├── metadata.ts             # Shared metadata helpers, JSON-LD generators
│   └── utils.ts                # cn(), lerp(), clamp()
├── types/
│   ├── project.ts              # Album, AlbumMeta, SanityImage interfaces
│   ├── animation.ts            # AnimationPreset, EasingToken, ScrollTriggerConfig
│   ├── ui.ts                   # NavItem
│   └── index.ts                # Barrel exports
├── stores/
│   └── ui-store.ts             # Zustand: menuOpen, isTransitioning, transitionType, morphSource, scrollProgress, activeSection
├── sanity/
│   ├── client.ts               # Sanity client configuration
│   ├── queries.ts              # GROQ queries with defineQuery
│   ├── schemas/
│   │   ├── album.ts            # Album document schema
│   │   ├── about.ts            # About singleton schema
│   │   └── index.ts            # Schema barrel export
│   └── lib/
│       └── image.ts            # Sanity image URL builder
├── styles/
│   └── globals.css             # Design tokens, Tailwind @theme, grain overlay, Lenis CSS
├── providers/
│   └── Providers.tsx           # Client: GSAP context, Lenis, LazyMotion
├── proxy.ts                    # Replaces middleware.ts in Next.js 16
├── next.config.ts
└── tsconfig.json
```

---

*End of Master Implementation Plan — Bamyan Storyworks*
