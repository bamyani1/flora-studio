# Phase E — Work & Album Pages (4–5 days)

## Objective

Build the Work index page (album grid with optional filters) and the Album detail page (the cinematic core of the site). The album page features: hero image with GSAP Flip morph arrival, word-by-word narrative scroll scrub, pinned horizontal scroll gallery, and next/previous album navigation. This is the most complex phase.

## Done When

You can browse Home → Work → Album → Back with full animations and transitions, including the shared element morph. Horizontal gallery scrolls cinematically on desktop and converts to vertical on mobile.

## Dependencies from Prior Phases

- **Phase A:** Sanity schemas (album), GSAP with Flip plugin, design tokens.
- **Phase B:** TransitionLink + TransitionOverlay with morph support, Zustand morphSource.
- **Phase C:** All animation primitives (FadeIn, TextReveal, ImageReveal, ParallaxSection).
- **Phase D:** ProjectGrid + ProjectCard (cards set `data-flip-id` and capture Flip state).

## Components to Build

### `HorizontalScrollGallery` — `src/components/sections/HorizontalScrollGallery.tsx`

- **Type:** Client Component
- **Props:**
  ```typescript
  interface HorizontalScrollGalleryProps {
    images: SanityImage[];
    blurDataURLs: string[];
  }
  ```
- **Behavior:** Pinned section. Images arranged in a horizontal strip. As user scrolls vertically, the strip scrolls horizontally. Each image is full viewport height, auto width maintaining aspect ratio. Spacing between images: `--space-4`.
- **Animation:** ScrollTrigger with `pin: true`, `scrub: 1`. Total scroll distance = (number of images × viewport width). Horizontal `x` translation from 0 → negative total width.
  ```typescript
  ScrollTrigger: {
    pin: true,
    scrub: 1,
    end: () => "+=" + (images.length * window.innerWidth)
  }
  ```
- **Responsive:** On mobile (`< md`), converts to vertical flowing gallery — no pin, no horizontal scroll. Images stack vertically at full width with `--space-8` between them.
- **Accessibility:** `aria-label="Photo gallery"`. Images have individual `alt` text from Sanity.
- **Dynamic import:** `next/dynamic` with `ssr: false` — heavy ScrollTrigger pin logic.
- **Image loading:** Uses `imageLoadReveal` (blur-up) as images enter the horizontal viewport.

### `NarrativeSection` — `src/components/sections/NarrativeSection.tsx`

- **Type:** Client Component
- **Props:**
  ```typescript
  interface NarrativeSectionProps {
    text: string;
    className?: string;
  }
  ```
- **Behavior:** Album narrative text displayed in Satoshi at `--text-xl` to `--text-2xl`. Text is split into words. As user scrolls through the section, words transition from `opacity: 0.15` to `opacity: 1` progressively — creating a reading/highlighting effect scrubbed to scroll position.
- **Animation:** Uses `textRevealWords` preset with `scrub: 1`. ScrollTrigger start/end spans the full section.
- **Reduced Motion:** All words at full opacity immediately. No scroll scrub.

## Relevant Animation Presets

### `textRevealWords`
```typescript
export const textRevealWords = {
  splitConfig: { type: "words" },
  from: { opacity: 0.15 },
  to: { opacity: 1, stagger: 0.05 },
  scrollTrigger: { start: "top 80%", end: "bottom 20%", scrub: 1 },
};
```

### `sharedElementMorph`
```typescript
export const sharedElementMorph = {
  flipDuration: 0.9,
  flipEase: easings.smoothInOut,
  // Steps:
  // 1. Capture Flip state of album card thumbnail
  // 2. Store in Zustand morphSource
  // 3. On album page mount, Flip.from() the hero image to morph from card position
  // Fallback: if morphSource is null (direct URL visit), skip Flip and use standard enter
};
```

### `parallaxLayer`
```typescript
export const parallaxLayer = (speed: number = 0.15) => ({
  to: { yPercent: speed * 100, ease: "none" },
  scrollTrigger: { start: "top bottom", end: "bottom top", scrub: true },
});
```

### `imageLoadReveal`
```typescript
export const imageLoadReveal = {
  cssTransition: "filter 0.6s var(--ease-out), transform 0.6s var(--ease-out)",
  loadedStyle: { filter: "blur(0)", transform: "scale(1)" },
  placeholderStyle: { filter: "blur(20px)", transform: "scale(1.1)" },
};
```

## Page Blueprints

### Work Page (`/work`) — `src/app/work/page.tsx`

**Server Component** — fetches all albums.

**Data:**
```groq
*[_type == "album"] | order(order asc) {
  _id, title, slug, category, year, location, coverImage,
  "blurDataURL": coverImage.asset->metadata.lqip
}
```

**SEO:**
- Title: `"Work — Bamyan Storyworks"`
- Description: `"Browse photography albums spanning personal, event, sports, and solo sessions."`
- JSON-LD: `CollectionPage` with `hasPart` array

**Scroll Timeline:**

1. **Page Header** (0vh–40vh)
   - "Work" heading in Instrument Serif `--text-6xl`, `textRevealLines` on enter
   - Optional filter tabs (All / Personal / Event / Sports / Solo) in Satoshi, fade up with stagger

2. **Album Grid** (40vh–end)
   - Full ProjectGrid with all 5–6 albums
   - 2-column asymmetric layout with alternating large/default card sizes
   - Cards trigger `staggerGrid` at `start: "top 85%"`
   - Each card has `data-flip-id={slug}` for shared element morph

### Album Page (`/work/[slug]`) — `src/app/work/[slug]/page.tsx`

**Server Component** with Client Component sections.

**Data:**
```groq
*[_type == "album" && slug.current == $slug][0] {
  title, slug, category, year, location, description,
  heroImage, "heroBlur": heroImage.asset->metadata.lqip,
  images[] {
    asset->, alt, caption,
    "blurDataURL": asset->metadata.lqip
  },
  narrative
}
```

**Static generation:**
```typescript
// generateStaticParams
*[_type == "album"] { "slug": slug.current }
```

**Dynamic metadata:**
```typescript
// generateMetadata
// Title: "[Album Title] — Bamyan Storyworks"
// OG Image: album hero image
// JSON-LD: ImageGallery schema with associatedMedia array
```

**Scroll Timeline — The Cinematic Core:**

1. **Album Hero** (0vh–100vh)
   - Full-bleed hero image at `100vh` height
   - If arriving via morph transition: `Flip.from()` morphs card thumbnail into hero image over 0.9s with `power2.inOut`
   - If arriving via wipe or direct URL: image uses `imageReveal` (overlay + scale)
   - Album title in Instrument Serif `--text-5xl`, `textRevealLines` at 0.3s delay
   - Metadata (category • year • location) in JetBrains Mono `--text-sm`, uppercase, fades up

2. **Narrative Intro** (100vh–200vh)
   - Album description in Satoshi `--text-xl`
   - Uses `NarrativeSection` — words highlight 15% → 100% opacity scrubbed to scroll
   - ScrollTrigger: `start: "top 80%"`, `end: "bottom 20%"`, `scrub: 1`

3. **Horizontal Scroll Gallery** (200vh–pinned for ~Nvw)
   - `HorizontalScrollGallery` pinned to viewport
   - N images (8–20) at viewport height, auto width
   - Scroll distance: N × 100vw
   - Images use `imageLoadReveal` (blur-up) as they enter horizontal viewport
   - On mobile: vertical gallery, no pin
   - ScrollTrigger: `pin: true`, `scrub: 1`, `end: () => "+=" + (images.length * window.innerWidth)`

4. **Additional Narrative Blocks** (if album has multiple text sections)
   - Same `NarrativeSection` treatment
   - `ImageReveal` for standalone highlight images between text blocks

5. **Next/Previous Navigation** (end of album)
   - Two-column: "Previous Album" (left) + "Next Album" (right)
   - Each shows album title + small thumbnail
   - Wrapped in `TransitionLink` with `transitionType="wipe"`
   - Entrance: `fadeUp` with stagger

## GSAP Flip Morph Architecture

```
Click TransitionLink (on ProjectCard, transitionType="morph")
  → Flip.getState(sourceElement) via data-flip-id
  → Store in Zustand morphSource
  → Run pageTransitionLeave
  → router.push("/work/[slug]")
  → Album page mounts
  → template.tsx detects isTransitioning + transitionType="morph"
  → If morphSource exists:
      → Flip.from(morphSource) on hero image element (0.9s, power2.inOut)
  → If morphSource is null (direct URL visit):
      → Skip Flip, use standard imageReveal enter
  → On complete: set isTransitioning: false
```

### Prefetch Strategy
- Album cards: `router.prefetch(href)` on `mouseenter` (300ms head start)
- Next/previous links: prefetch on viewport enter via ScrollTrigger callback
