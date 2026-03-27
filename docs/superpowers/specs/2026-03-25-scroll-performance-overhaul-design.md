# Scroll Performance Overhaul — Design Spec

## Problem

Scroll jank across all pages, worsening with image count. Root cause: expensive per-frame CSS properties (backdrop-filter, height, padding, filter, boxShadow) animated via `scrub: true` ScrollTriggers, combined with ScrollTrigger accumulation across routes and no virtualization of off-screen content.

## Constraint

Zero visual changes. Every effect must look identical — only the rendering engine changes underneath.

---

## Section 1: Header — Compositor-Only Scroll Animation

**Files:** `src/components/layout/Header.tsx`, `src/lib/animations.ts` (headerShrink preset), `src/styles/globals.css`

### Current (expensive)

The header uses a scrubbed ScrollTrigger (0–150px range) animating these properties per frame:

- `backdrop-filter: blur(Npx)` — GPU filter kernel recalculated every frame
- `height: 5rem → 3.5rem` — triggers layout reflow
- `padding: 1.25rem → 0.625rem` — triggers layout reflow
- `boxShadow` — triggers paint
- `backgroundColor`, `borderColor`, `borderRadius` — trigger paint

### Replacement

| Property                         | Technique                                                                                                                                                                      |
| -------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `backdrop-filter: blur()`        | Static `backdrop-filter: blur(12px)` on `::before` pseudo-element. Animate pseudo `opacity: 0→1`. Blur computation happens once in compositor; only opacity changes per frame. |
| `height`                         | Header DOM stays at compact size (3.5rem). Initial expanded appearance via `transform: translateY()` offset on inner elements. Scroll animates transform back to 0.            |
| `padding`                        | Content positioned via transforms, not padding. `translateY/translateX` adjustments create the same spacing visual.                                                            |
| `boxShadow`                      | Static shadow on `::after` pseudo-element. Animate `opacity: 0→1`.                                                                                                             |
| `backgroundColor`, `borderColor` | Overlay pseudo-element with final-state colors. `opacity` crossfade.                                                                                                           |
| `borderRadius`                   | Can use `clip-path: inset(0 round Xpx)` if needed, or keep as-is (borderRadius alone is cheap).                                                                                |

### Result

Every scrubbed animation becomes `transform` or `opacity` — handled entirely by compositor thread.

---

## Section 2: ScrollTrigger Lifecycle Management

**Files:** `src/providers/Providers.tsx`, `src/components/layout/TransitionOverlay.tsx`, all gallery components

### Current (broken)

- ScrollTrigger instances created by gallery components persist after route change
- `ScrollTrigger.refresh()` fires on every `pathname` change, recalculating ALL triggers (active + orphaned)
- No explicit kill in gallery component unmount — relies on `useGSAP` scope which may not catch dynamically created triggers

### Fix

1. **Global cleanup before route transition:**
   In the transition orchestration (before iris shutter closes), call:

   ```typescript
   ScrollTrigger.getAll().forEach((st) => st.kill());
   ```

   Guarantees clean slate for next page.

2. **Explicit cleanup per gallery component:**
   Each gallery component (`GalleryHero`, `GalleryBentoSplit`, `GalleryFullBleed`, `GalleryTextureCards`, `FolioGallery`) stores its ScrollTrigger instances in a ref and kills them explicitly in the `useGSAP` cleanup callback.

3. **Debounced refresh:**
   Replace immediate `ScrollTrigger.refresh()` on pathname change with a 100ms debounce. Only fires once after navigation settles.

### Result

At any point, only ScrollTriggers for the current page exist. No orphan accumulation.

---

## Section 3: FolioGallery Virtualization

**File:** `src/components/sections/FolioGallery.tsx`

### Current (expensive)

A 16-image project renders ~11 full-screen page divs simultaneously. All ScrollTrigger animations register at load. Every scroll frame evaluates 11+ triggers even though only 1-2 pages are visible.

### Fix

- **IntersectionObserver-based mounting:** Only mount page sections within 1 viewport of visibility (`rootMargin: "100% 0px"` on the observer). Max 3 mounted at a time (1 ahead, current, 1 behind).
- **Placeholder divs:** Unmounted pages render as empty `<div>` elements with `height` matching `contain-intrinsic-size: 100vh` to preserve scroll position and total document height
- **Mount/unmount lifecycle:** When a page enters observation zone → mount component → ScrollTrigger registers. When page exits (2+ viewports away) → unmount → ScrollTrigger killed automatically.
- **First 2 pages eager:** Always mount pages 0-1 (above-fold content) without waiting for intersection

### Result

Max 3 page sections with active animations at any time. 16-image projects feel identical to 4-image projects during scroll.

---

## Section 4: Filter Animations — Overlay Technique

**Files:** `src/lib/animations.ts` (cinematicHeroReveal, blurReveal presets), `src/components/sections/gallery/GalleryHero.tsx`

### Current (expensive)

`cinematicHeroReveal` animates `filter: blur(20px) brightness(0.1)` → `filter: blur(0px) brightness(1)` scrubbed on scroll. CSS `filter` recalculates the entire filter kernel per frame.

### Fix — Overlay Layer Technique

Instead of animating `filter` on the image element:

1. **Dark overlay:** `<div>` on top of image with `background: black`, animate `opacity: 0.9→0` (replaces `brightness(0.1→1)`)
2. **Blur overlay:** `<div>` with static `backdrop-filter: blur(20px)`, animate `opacity: 1→0` (replaces blur animation). Static `backdrop-filter` is far cheaper than animating the filter value.
3. **Image element:** Only animates `transform: scale(1.15→1)` (already compositor-friendly)

Same technique for `blurReveal` preset: static blur on overlay, fade overlay opacity.

### Result

Image starts dark and blurry, progressively reveals sharp and bright — visually identical. Only `opacity` and `transform` animated per frame.

---

## Section 5: Input Listener Optimization

### CustomCursor (`src/components/ui/CustomCursor.tsx`)

**Current:** Raw `mousemove` fires on every pixel. Runs `gsap.quickTo()` + angle math per event.

**Fix:** Gate with `requestAnimationFrame`:

```typescript
let frameId: number | null = null;
const latestEvent = { x: 0, y: 0 };

const handleMouseMove = (e: MouseEvent) => {
  latestEvent.x = e.clientX;
  latestEvent.y = e.clientY;
  if (!frameId) {
    frameId = requestAnimationFrame(() => {
      // process latestEvent here
      frameId = null;
    });
  }
};
```

`gsap.quickTo()` already interpolates, so one update per frame is visually identical to per-pixel updates.

### MagneticButton (`src/hooks/useMagnetic.ts`)

**Current:** `getBoundingClientRect()` on every `mousemove` — forces synchronous reflow.

**Fix:** Cache the rect on `mouseenter`. Update cache on `resize` via `ResizeObserver`. Use cached values in `mousemove` handler. The rect doesn't change during a hover interaction.

---

## Section 6: CSS Containment & will-change Consistency

**Files:** Gallery components, `src/styles/globals.css`

### content-visibility

Apply `content-visibility: auto` with `contain-intrinsic-size: auto 100vh` on ALL gallery sections by default (not just "smooth" mode). This tells the browser to skip rendering off-screen sections entirely.

### will-change lifecycle

Standardize across all scrubbed ScrollTrigger animations:

- `onEnter` / `onEnterBack`: set `will-change: transform` (or `transform, opacity`)
- `onLeave` / `onLeaveBack`: reset `will-change: auto`

Currently inconsistent — some components do this, others don't. The `withWillChange()` utility in `animations.ts` handles one-shot animations well, but scrubbed animations need the enter/leave pattern.

### contain property

Add `contain: layout style paint` on gallery section wrappers. This isolates repaints — when one gallery section repaints, sibling sections are not affected.

---

## Verification Plan

1. **Chrome DevTools Performance tab:** Record scroll on `/work` and `/work/[slug]` pages before and after. Compare:
   - Main thread busy time during scroll
   - Compositor frame drops
   - Total ScrollTrigger count (via `ScrollTrigger.getAll().length` in console)
2. **Navigate 5+ pages, then check:** `ScrollTrigger.getAll().length` should equal only the current page's triggers (not accumulated)
3. **FolioGallery 16-image project:** Scroll through and verify smooth 60fps. Check DOM inspector — only ~3 page sections should be mounted at any time.
4. **Visual regression:** Screenshot comparison of header shrink, hero reveal, gallery animations before/after to confirm zero visual change
5. **`npm run build` + Lighthouse:** Performance score comparison

---

## Files to Modify

| File                                                      | Changes                                                             |
| --------------------------------------------------------- | ------------------------------------------------------------------- |
| `src/components/layout/Header.tsx`                        | Rewrite scroll animation to use pseudo-elements + transforms        |
| `src/lib/animations.ts`                                   | Update `headerShrink`, `cinematicHeroReveal`, `blurReveal` presets  |
| `src/providers/Providers.tsx`                             | Add ScrollTrigger cleanup before route transition, debounce refresh |
| `src/components/layout/TransitionOverlay.tsx`             | Kill ScrollTriggers before iris shutter                             |
| `src/components/sections/FolioGallery.tsx`                | Add IntersectionObserver virtualization                             |
| `src/components/sections/gallery/GalleryHero.tsx`         | Use overlay technique for hero reveal                               |
| `src/components/sections/gallery/GalleryBentoSplit.tsx`   | Explicit ScrollTrigger cleanup, will-change consistency             |
| `src/components/sections/gallery/GalleryFullBleed.tsx`    | Explicit ScrollTrigger cleanup, will-change consistency             |
| `src/components/sections/gallery/GalleryTextureCards.tsx` | Explicit ScrollTrigger cleanup, will-change consistency             |
| `src/components/ui/CustomCursor.tsx`                      | rAF-gated mousemove                                                 |
| `src/hooks/useMagnetic.ts`                                | Cached getBoundingClientRect                                        |
| `src/styles/globals.css`                                  | content-visibility defaults, contain property on gallery wrappers   |
