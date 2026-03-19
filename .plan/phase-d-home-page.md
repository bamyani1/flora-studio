# Phase D — Home Page (3–4 days)

## Objective

Build the complete home page with all sections: Hero (cinematic load-in), ProjectGrid with ProjectCards (hover effects + morph prep), AboutTeaser (parallax depth), CategoriesStrip, ContactCTA, and Footer integration. Wire Sanity data fetching for featured albums and about teaser content.

## Done When

Homepage plays exactly as described in the scroll timeline — hero choreographs on load, albums reveal on scroll, about teaser has parallax depth, footer CTA is magnetic.

## Dependencies from Prior Phases

- **Phase A:** Design tokens, fonts, GSAP, Zustand, Sanity client + schemas.
- **Phase B:** Header, Footer, TransitionLink, TransitionOverlay.
- **Phase C:** All animation primitives (FadeIn, TextReveal, ImageReveal, ParallaxSection, MagneticButton).

## Components to Build

### `Hero` — `src/components/sections/Hero.tsx`

- **Type:** Client Component
- **Props:**
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

### `ProjectGrid` — `src/components/sections/ProjectGrid.tsx`

- **Type:** Server Component shell with Client Component cards
- **Props:**
  ```typescript
  interface ProjectGridProps {
    projects: ProjectMeta[];
    columns?: 2 | 3;       // default: 2
    featured?: boolean;     // default: false — first item spans full width
  }
  ```
- **Behavior:** CSS Grid layout. 2-column on desktop with alternating large/small card sizes for visual rhythm. Each card is a `ProjectCard`.
- **Responsive:** Single column on mobile, 2-column on tablet+.

### `ProjectCard` — `src/components/sections/ProjectCard.tsx`

- **Type:** Client Component
- **Props:**
  ```typescript
  interface ProjectCardProps {
    project: ProjectMeta;
    index: number;
    size?: "large" | "default";
  }
  ```
- **Behavior:** Thumbnail image (aspect-ratio 3:4 or 16:9 depending on size) + album title + category tag (JetBrains Mono `--text-xs`, uppercase). Wrapped in `TransitionLink` with `transitionType="morph"`.
- **Hover:** Image scales to 1.05x + color overlay (`var(--color-primary)` at 30% opacity) slides in from bottom + title text reveals on top via `clipPath` animation. Overlay must fully reverse on mouse leave before click navigation triggers.
- **Animation:** Scroll entrance via `staggerGrid` preset.
- **Responsive:** Direct tap navigates (no intermediary hover state on touch).
- **Accessibility:** Card is a link. Image has `alt` text.
- **Data attribute:** `data-flip-id={project.slug}` on the image container for GSAP Flip morph.

### `AboutTeaser` — `src/components/sections/AboutTeaser.tsx`

- **Type:** Server Component
- **Props:**
  ```typescript
  interface AboutTeaserProps {
    bio: string;
    portrait: SanityImageSource;
    blurDataURL: string;
  }
  ```
- **Behavior:** Two-column layout: portrait image (left, wrapped in `ImageReveal`) + philosophy text in Instrument Serif at `--text-3xl` (right, wrapped in `TextReveal variant="lines"`). "About me →" link below text.
- **Responsive:** Stacked single column on mobile, image on top.

### `CategoriesStrip` — `src/components/sections/CategoriesStrip.tsx`

- **Type:** Client Component
- **Behavior:** Horizontal strip showing four photography types: Personal, Event, Sports, Solo. Each category is a text label in Satoshi `--text-xl` with a thin divider line between them. Optionally each has a small thumbnail that reveals on hover.
- **Animation:** Labels stagger in via `fadeUp`. On hover, labels shift to `var(--color-primary)`.
- **Responsive:** Wraps to 2×2 grid on mobile.

### `ContactCTA` — `src/components/sections/ContactCTA.tsx`

- **Type:** Server Component shell with Client animation wrapper
- **Behavior:** Large "Let's work together" in Instrument Serif at `--text-5xl`, centered. Below: email link in Satoshi. Wrapped in `TextReveal variant="lines"`.
- **Responsive:** Scales to `--text-3xl` on mobile.

## Relevant Animation Presets

### `heroSequence` — Homepage hero load-in choreography (total: ~3.5s)

```typescript
export const heroSequence = {
  steps: [
    // Step 1: Film grain fades in (0s–0.3s)
    { target: ".grain-overlay", from: { autoAlpha: 0 }, to: { autoAlpha: 1, duration: 0.3 } },
    // Step 2: Horizontal rule draws from center (0.2s–0.8s)
    { target: ".hero-rule", from: { scaleX: 0 }, to: { scaleX: 1, duration: 0.6, ease: "power2.inOut" }, position: 0.2 },
    // Step 3: "BAMYAN" SplitText line reveal (0.5s–1.7s)
    { target: ".hero-title", animation: "textRevealLines", stagger: 0.12, position: 0.5 },
    // Step 4: "STORYWORKS" fade up with letter-spacing (1.2s–2.0s)
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
```

### `staggerGrid`
```typescript
export const staggerGrid = {
  from: { y: 40, autoAlpha: 0 },
  to: { y: 0, autoAlpha: 1, duration: 0.8, ease: easings.smooth, stagger: 0.1 },
  scrollTrigger: { start: "top 85%", toggleActions: "play none none none" },
};
```

### `imageReveal`
```typescript
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
```

## Page Blueprint — Home Page (`/`)

**Server Component** — assembles sections, fetches featured projects from Sanity.

### Data Requirements
- Featured albums: `*[_type == "album" && featured == true] | order(order asc)[0...4] { _id, title, slug, category, coverImage, "blurDataURL": coverImage.asset->metadata.lqip }`
- Hero image: from Sanity singleton or config
- About teaser: short bio + portrait from Sanity "about" singleton

### SEO
- Title: `"Bamyan Storyworks — Photography for moments that matter"`
- Description: `"Cinematic photography studio specializing in personal, event, sports, and solo photography."`
- OG Image: static branded image or dynamic generation
- JSON-LD: `LocalBusiness` schema with `photographer` type

### Section-by-Section Scroll Timeline

1. **Hero** (0vh–100vh)
   - On page load (not scroll-triggered): `heroSequence` timeline plays
   - Film grain → horizontal rule → "BAMYAN" → "STORYWORKS" → photograph → tagline → scroll indicator
   - At scroll 50vh: scroll indicator fades out

2. **Selected Albums** (100vh–~260vh)
   - `start: "top 85%"`: "Selected Work" heading triggers `textRevealLines`
   - ProjectGrid with 3–4 albums, 2-column asymmetric
   - Cards trigger `staggerGrid` at `start: "top 85%"` (stagger 0.1s)
   - Card images use `imageReveal` (blue overlay + scale)
   - Hover: scale 1.05x + overlay slide-in with title

3. **About Teaser** (~260vh–~360vh)
   - `start: "top 80%"`: Portrait triggers `imageReveal` on left
   - `start: "top 75%"`: Philosophy text triggers `textRevealLines` on right
   - "About me →" link fades up with `fadeUp`, 0.3s delay
   - `parallaxLayer(0.08)` on portrait for depth

4. **Categories Strip** (~360vh–~420vh)
   - `start: "top 85%"`: Labels stagger `fadeUp`, stagger 0.08s
   - Divider lines draw on via `clipRevealLeft`

5. **Contact CTA** (~420vh–~500vh)
   - `start: "top 80%"`: "Let's work together" triggers `textRevealLines`
   - Email link fades up with `fadeUp`, 0.2s delay
   - `MagneticButton` effect on CTA text

6. **Footer** (~500vh–end)
   - Social links stagger `fadeUp`, stagger 0.06s
   - Copyright fades in
