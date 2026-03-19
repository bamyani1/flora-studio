# Phase C — Animation Primitives (3–4 days)

## Objective

Build all reusable animation wrapper components and hooks. After this phase, any element can be animated by wrapping it in a component like `<FadeIn>`, `<TextReveal>`, or `<ImageReveal>`. All 18+ animation presets are implemented and every animation respects `prefers-reduced-motion`.

## Done When

- Dropping `<FadeIn>` around any element produces the correct scroll-triggered entrance animation.
- `<TextReveal>` splits and animates text (lines or words variant).
- `<ImageReveal>` plays the two-part blue overlay + scale reveal.
- All animations respect `prefers-reduced-motion`.

## Dependencies from Prior Phases

- **Phase A:** GSAP setup (`lib/gsap.ts`), easing tokens (`lib/easings.ts`), design tokens in `globals.css`, Providers with Lenis.
- **Phase B:** Layout shell exists for testing animations in context.

## Components to Build

### `FadeIn` — `src/components/animations/FadeIn.tsx`

- **Type:** Client Component
- **Props:**
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

### `TextReveal` — `src/components/animations/TextReveal.tsx`

- **Type:** Client Component
- **Props:**
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

### `ImageReveal` — `src/components/animations/ImageReveal.tsx`

- **Type:** Client Component
- **Props:**
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

### `ParallaxSection` — `src/components/animations/ParallaxSection.tsx`

- **Type:** Client Component
- **Props:**
  ```typescript
  interface ParallaxSectionProps {
    children: React.ReactNode;
    speed?: number;      // default: 0.15
    className?: string;
  }
  ```
- **Behavior:** Applies scroll-linked Y translation to children for depth effect.
- **Animation:** Uses `parallaxLayer(speed)` preset. ScrollTrigger scrub.
- **Responsive:** Disabled on mobile (`< md`) via `gsap.matchMedia()`. Disabled when `prefers-reduced-motion: reduce`.

### `MagneticButton` — `src/components/animations/MagneticButton.tsx`

- **Type:** Client Component
- **Props:**
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
- **Accessibility:** Underlying element remains `<button>` or `<a>` — magnetic effect is purely visual.

### `ClipReveal` — `src/components/animations/ClipReveal.tsx`

- **Type:** Client Component
- **Behavior:** Clip-path wipe animation from a specified direction.
- **Animation:** Uses `clipRevealUp` or `clipRevealLeft` preset depending on a `direction` prop.
- **Reduced Motion:** Instant clip-path reveal.

## Hooks to Build

### `useReducedMotion` — `src/hooks/useReducedMotion.ts`

Detects `prefers-reduced-motion: reduce` media query. Returns boolean. SSR-safe (defaults to `false`).

### `useMagnetic` — `src/hooks/useMagnetic.ts`

Encapsulates the magnetic proximity logic. Takes a ref, radius, and strength. Returns nothing — applies GSAP effects directly to the ref.

### `useMediaQuery` — `src/hooks/useMediaQuery.ts`

SSR-safe responsive breakpoint detection. Accepts a media query string, returns boolean match state.

### `useLenis` — `src/hooks/useLenis.ts`

Access the Lenis instance for programmatic `scrollTo`, `stop()`, `start()`.

## All Animation Presets (full reference)

These should all be implemented in `src/lib/animations.ts`:

### `fadeUp`
```typescript
export const fadeUp = {
  from: { y: 30, autoAlpha: 0 },
  to: { y: 0, autoAlpha: 1, duration: 0.8, ease: easings.smooth },
  scrollTrigger: { start: "top 85%", end: "top 20%", toggleActions: "play none none none" },
};
```

### `textRevealLines`
```typescript
export const textRevealLines = {
  splitConfig: { type: "lines", mask: "lines", autoSplit: true },
  from: { yPercent: 100 },
  to: { yPercent: 0, duration: 1.0, ease: easings.smooth, stagger: 0.12 },
  scrollTrigger: { start: "top 85%", toggleActions: "play none none none" },
};
```

### `textRevealWords`
```typescript
export const textRevealWords = {
  splitConfig: { type: "words" },
  from: { opacity: 0.15 },
  to: { opacity: 1, stagger: 0.05 },
  scrollTrigger: { start: "top 80%", end: "bottom 20%", scrub: 1 },
};
```

### `clipRevealUp`
```typescript
export const clipRevealUp = {
  from: { clipPath: "inset(100% 0% 0% 0%)" },
  to: { clipPath: "inset(0% 0% 0% 0%)", duration: 1.0, ease: easings.smooth },
  scrollTrigger: { start: "top 85%", toggleActions: "play none none none" },
};
```

### `clipRevealLeft`
```typescript
export const clipRevealLeft = {
  from: { clipPath: "inset(0% 100% 0% 0%)" },
  to: { clipPath: "inset(0% 0% 0% 0%)", duration: 1.0, ease: easings.smooth },
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

### `imageLoadReveal`
```typescript
export const imageLoadReveal = {
  cssTransition: "filter 0.6s var(--ease-out), transform 0.6s var(--ease-out)",
  loadedStyle: { filter: "blur(0)", transform: "scale(1)" },
  placeholderStyle: { filter: "blur(20px)", transform: "scale(1.1)" },
};
```

### `parallaxLayer`
```typescript
export const parallaxLayer = (speed: number = 0.15) => ({
  to: { yPercent: speed * 100, ease: "none" },
  scrollTrigger: { start: "top bottom", end: "bottom top", scrub: true },
});
```

### `magneticPull`
```typescript
export const magneticPull = {
  proximityRadius: 100,
  strength: 0.3,
  ease: "power3.out",
  returnDuration: 0.5,
  returnEase: "elastic.out(1, 0.3)",
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

### `marqueeLoop`
```typescript
export const marqueeLoop = {
  duration: 20,
  ease: "none",
  repeat: -1,
};
```

### Reduced Motion Fallbacks

Every animation must check `prefers-reduced-motion` and apply these fallbacks:

| Preset | Fallback |
|--------|----------|
| fadeUp | instant autoAlpha: 1, no Y translation |
| textRevealLines | instant autoAlpha: 1, no SplitText, no mask |
| textRevealWords | all words at full opacity, no scrub |
| clipRevealUp | instant clip-path: inset(0%) |
| clipRevealLeft | instant clip-path: inset(0%) |
| imageReveal | no overlay, image at scale 1 immediately |
| imageLoadReveal | no blur transition, image appears immediately |
| parallaxLayer | disabled — no Y translation |
| magneticPull | disabled — no cursor following |
| staggerGrid | instant autoAlpha: 1, no stagger |
| marqueeLoop | static, no scroll |
| horizontalScrollGallery | vertical flowing gallery, no pin |
