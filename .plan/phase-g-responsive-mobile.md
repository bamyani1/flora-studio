# Phase G — Responsive & Mobile (2–3 days)

## Objective

Adapt all components for mobile and tablet. Ensure animations are appropriate for touch/low-power devices. Test on real devices. The mobile experience should be intentionally designed, not just reflowed.

## Done When

Site works flawlessly on mobile Safari, Android Chrome, iPad, and desktop. No horizontal overflow, no broken scroll, no invisible elements.

## Dependencies from Prior Phases

- **Phases A–F:** All components built and functional on desktop.

## Responsive Rules by Component

### Layout

| Component | Desktop (≥ md/768px) | Mobile (< md/768px) |
|-----------|---------------------|---------------------|
| Header | Wordmark left + text links right | Wordmark left + hamburger right |
| MobileMenu | Not rendered | Full-screen overlay |
| Footer CTA | `--text-footer-cta` (80px) | `--text-3xl` (32px) |
| Section padding | `--section-padding-y` (128px) | `--section-padding-y-mobile` (64px) |
| Container padding | `--container-padding-x` (32px) | `--container-padding-x-mobile` (16px) |

### Animation Components

| Component | Desktop | Mobile |
|-----------|---------|--------|
| ParallaxSection | Active with `speed` prop | Disabled via `gsap.matchMedia()` |
| HorizontalScrollGallery | Pinned horizontal scroll | Vertical flowing gallery, no pin |
| MagneticButton | Active on `(hover: hover)` | Disabled on touch devices |
| ScrollIndicator | Visible at hero bottom-right | Hidden (`< md`) |

### Section Components

| Component | Desktop | Mobile |
|-----------|---------|--------|
| Hero title | `--text-hero` (128px) | `--text-hero-mobile` (56px) |
| ProjectGrid | 2-column asymmetric | Single column |
| AboutTeaser | Two-column (portrait left, text right) | Stacked (image top, text below) |
| CategoriesStrip | Horizontal row | 2×2 grid |
| ContactCTA | `--text-5xl` (56px) | `--text-3xl` (32px) |
| ContactForm | Two-column (form left, info right) | Stacked single column |
| Album hero title | `--text-5xl` (56px) | `--text-3xl` (32px) |

### UI Components

| Component | Desktop | Mobile |
|-----------|---------|--------|
| ProjectCard hover | Scale + overlay + title reveal | Direct tap navigates (no hover state) |
| Button magnetic | Active | Disabled on touch |

## Performance Strategy

### LazyMotion
- Motion's bundle reduced from ~30–50KB to ~5KB via `domAnimation` feature set.

### Dynamic Imports (`next/dynamic`)

| Component | `ssr: false` | Reason |
|-----------|-------------|--------|
| MobileMenu | Yes | Only needed on interaction, heavy animation |
| HorizontalScrollGallery | Yes | Heavy ScrollTrigger pin logic |
| ContactForm | No | SSR for SEO, hydrates on client |
| Sanity Studio | Yes | Entirely client-side admin |

### `next/image` Configuration
- Formats: AVIF, WebP
- Device sizes: 640, 768, 1024, 1280, 1536, 1920
- Hero image: `priority` attribute for LCP
- All images: `blurDataURL` from Sanity LQIP

### Font Loading
- Instrument Serif: Google Fonts, `preload: true` — LCP-critical
- Satoshi: local `.woff2`, `preload: true` — body font
- JetBrains Mono: local `.woff2`, `preload: false` — accent, not critical

### Bundle Splitting

| Route | Libraries Loaded |
|-------|-----------------|
| All routes | GSAP core (~10KB) + ScrollTrigger (~15KB) + Lenis (~5KB) + LazyMotion (~5KB) + Zustand (~1KB) = ~36KB |
| Home | + SplitText |
| Work | + Flip |
| Work/[slug] | + SplitText + Flip |
| About | + SplitText |
| Contact | + Zod (~5KB) |
| /studio | Sanity Studio (~500KB+, isolated) |

### `will-change` Plan
- Apply `will-change: transform, opacity` via GSAP `onStart` callback
- Remove via `onComplete` callback
- Never leave permanently — `will-change: auto` is resting state
- Exception: `TransitionOverlay` can keep `will-change: transform`

### Critical CSS Path (Above the Fold)
First paint must include:
1. `var(--color-background)` on `html` (prevent white flash)
2. Font declarations for Instrument Serif and Satoshi
3. Header styles (fixed position, layout)
4. Hero container styles (full viewport, image container, text positioning)
5. Film grain overlay CSS

### Server vs Client Component Decision Tree

| Server Component | Client Component |
|-----------------|-----------------|
| Static/fetched content | `useState`, `useEffect`, `useRef` |
| No browser APIs | GSAP, Motion, Lenis, animations |
| No event handlers | Click, hover, scroll, form handlers |
| CMS data rendering | Zustand store reads |
| Layout shells, page assemblies | Interactive UI |

**In practice:**
- Server: `page.tsx` files, `layout.tsx`, `AboutTeaser` content, `ProjectGrid` shell
- Client: all animation wrappers, Header, MobileMenu, ProjectCard, Hero, ContactForm, HorizontalScrollGallery, NarrativeSection, TransitionOverlay, TransitionLink, ScrollIndicator, BackToTop

## Testing Checklist

- [ ] iPhone Safari — smooth scroll, transitions, galleries
- [ ] Android Chrome — same
- [ ] iPad — desktop layout with touch interactions
- [ ] Desktop: Chrome, Firefox, Safari (macOS), Edge
- [ ] Slow 3G network throttle — content loads progressively
- [ ] `prefers-reduced-motion: reduce` — all animations have fallbacks
- [ ] Lenis smooth scroll simplified when reduced motion active
- [ ] No horizontal overflow on any viewport width
- [ ] No invisible elements or broken layouts
- [ ] Mobile menu focus trap works
- [ ] Touch targets meet minimum 44×44px
