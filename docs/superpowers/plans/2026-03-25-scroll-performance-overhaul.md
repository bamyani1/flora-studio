# Scroll Performance Overhaul — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Eliminate scroll jank across all pages by replacing expensive per-frame CSS operations with compositor-friendly equivalents and cleaning up ScrollTrigger lifecycle management.

**Architecture:** Six independent workstreams targeting: (1) ScrollTrigger orphan cleanup on route change, (2) header per-frame style writes, (3) gallery hero filter→overlay technique, (4) mousemove listener throttling, (5) FolioGallery section virtualization, (6) CSS containment consistency. All changes are engine-only — zero visual difference.

**Tech Stack:** GSAP + ScrollTrigger, Lenis, Next.js Image, IntersectionObserver, CSS `content-visibility`

**Spec:** `docs/superpowers/specs/2026-03-25-scroll-performance-overhaul-design.md`

---

## File Map

| File | Action | Responsibility |
|---|---|---|
| `src/providers/Providers.tsx` | Modify | Debounce ScrollTrigger.refresh(), add cleanup |
| `src/components/layout/TransitionOverlay.tsx` | Modify | Kill all ScrollTriggers before iris transition |
| `src/lib/animations.ts` | Modify | Rewrite headerShrink (remove blur proxy), update cinematicHeroReveal |
| `src/components/layout/Header.tsx` | Modify | Remove per-frame onUpdate, boxShadow→pseudo |
| `src/components/sections/gallery/GalleryHero.tsx` | Modify | Add dark + blur overlays for cinematic reveal |
| `src/components/ui/CustomCursor.tsx` | Modify | rAF-gated mousemove |
| `src/hooks/useMagnetic.ts` | Modify | Cache getBoundingClientRect on mouseenter |
| `src/components/sections/FolioGallery.tsx` | Modify | IntersectionObserver virtualization |
| `src/components/sections/gallery/GalleryBentoSplit.tsx` | Modify | content-visibility + will-change standardization |
| `src/components/sections/gallery/GalleryFullBleed.tsx` | Modify | content-visibility + will-change standardization |
| `src/components/sections/gallery/GalleryTextureCards.tsx` | Modify | content-visibility + will-change standardization |
| `src/styles/globals.css` | Modify | Header pseudo-element styles, gallery containment defaults |

---

### Task 1: ScrollTrigger Lifecycle Cleanup

**Files:**
- Modify: `src/providers/Providers.tsx`
- Modify: `src/components/layout/TransitionOverlay.tsx`

This is foundational — must be done first. Prevents orphaned ScrollTriggers from accumulating across route changes.

- [ ] **Step 1: Add debounced ScrollTrigger.refresh() in Providers**

In `src/providers/Providers.tsx`, replace the immediate `ScrollTrigger.refresh()` call with a debounced version:

```tsx
// BEFORE (lines 40-46):
useEffect(() => {
  if (useNativeScroll) {
    clearLenisRootState();
  }

  requestAnimationFrame(() => ScrollTrigger.refresh());
}, [pathname, useNativeScroll]);

// AFTER:
useEffect(() => {
  if (useNativeScroll) {
    clearLenisRootState();
  }

  const timer = setTimeout(() => {
    requestAnimationFrame(() => ScrollTrigger.refresh());
  }, 100);

  return () => clearTimeout(timer);
}, [pathname, useNativeScroll]);
```

- [ ] **Step 2: Kill all ScrollTriggers before route transition**

In `src/components/layout/TransitionOverlay.tsx`, add `ScrollTrigger` import and kill all triggers before iris transition starts. Find the transition effect (around line 89) and add cleanup:

Add to imports:
```tsx
import { ScrollTrigger } from "@/lib/gsap";
```

In the `useEffect` that handles `transitionPhase`, add cleanup before the iris close call and before the reduced motion fade:

```tsx
// Inside the useEffect, before the reduced-motion branch and before the iris branch:

if (transitionPhase === "leaving") {
  // Kill all ScrollTriggers to prevent orphans on the next page
  ScrollTrigger.getAll().forEach((st) => st.kill());
}
```

Place this **before** the `if (reduced)` check (around line 96), so it runs for both reduced and full motion paths when leaving.

- [ ] **Step 3: Verify cleanup works**

Run: `npm run dev`

Open browser console and navigate between pages. After each navigation, run:
```js
ScrollTrigger.getAll().length
```

Expected: Count should reflect only the current page's triggers (typically 2-8), NOT accumulate across navigations.

- [ ] **Step 4: Verify build passes**

Run: `npm run build`
Expected: Build succeeds with no type errors.

- [ ] **Step 5: Commit**

```bash
git add src/providers/Providers.tsx src/components/layout/TransitionOverlay.tsx
git commit -m "perf: clean up ScrollTriggers on route transition, debounce refresh"
```

---

### Task 2: Header Performance Optimization

**Files:**
- Modify: `src/lib/animations.ts`
- Modify: `src/components/layout/Header.tsx`

Remove the per-frame `onUpdate` callback (dead code since blur is 0→0), move `boxShadow` to an opacity-animated overlay element to eliminate per-frame paint.

- [ ] **Step 1: Update headerShrink preset in animations.ts**

In `src/lib/animations.ts`, update the `headerShrink` preset. Remove `boxShadow` from `from`/`to` (now handled by pseudo), and remove the `blur` proxy (it's a no-op 0→0):

```tsx
export const headerShrink = {
  scrollTrigger: {
    start: 0,
    end: 150,
    scrub: true,
  },
  from: {
    height: "5rem",
    paddingTop: "1.25rem",
    paddingBottom: "1.25rem",
    backgroundColor: "#161a12",
    borderColor: "rgba(255,255,255,0.15)",
    borderRadius: "0px",
  },
  to: {
    height: "3.5rem",
    paddingTop: "0.625rem",
    paddingBottom: "0.625rem",
    backgroundColor: "rgba(17,18,16,0.35)",
    borderColor: "rgba(255,255,255,0.2)",
    borderRadius: "2px",
  },
  /** Shadow is now on ::after pseudo — animate its opacity instead */
  shadow: { from: { opacity: 0 }, to: { opacity: 1 } },
  logo: {
    from: { fontSize: "1.5rem" },
    to: { fontSize: "1.25rem" },
  },
  /** Crossfade: wordmark text fades out, aperture icon fades in */
  logoMorph: {
    text: { from: { autoAlpha: 1 }, to: { autoAlpha: 0 } },
    icon: { from: { autoAlpha: 0, scale: 0.8 }, to: { autoAlpha: 1, scale: 1 } },
  },
};
```

Key changes: removed `boxShadow` from `from`/`to`, removed `blur` property, added `shadow` for pseudo opacity.

- [ ] **Step 2: Rewrite Header scroll animation**

In `src/components/layout/Header.tsx`, rewrite the scroll-scrub `useGSAP` hook (lines 36-101). Remove the `onUpdate` callback, blur proxy, and per-frame backdrop-filter writes. Add shadow pseudo animation:

```tsx
// Scroll-scrub morphing — continuous interpolation over 0-150px
useGSAP(() => {
  const header = headerRef.current;
  if (!header) return;

  // Get the ::after pseudo for shadow animation
  const shadowPseudo = header.querySelector<HTMLElement>(".header-shadow-target");

  if (reducedMotion || isContactPage) {
    gsap.set(header, headerShrink.to);
    if (shadowPseudo) gsap.set(shadowPseudo, headerShrink.shadow.to);
    return;
  }

  const tl = gsap.timeline({
    scrollTrigger: {
      ...headerShrink.scrollTrigger,
    },
  });

  // Header: height, padding, background, border, borderRadius
  tl.fromTo(header, headerShrink.from, { ...headerShrink.to, ease: "none" }, 0);

  // Shadow pseudo: opacity 0 → 1
  if (shadowPseudo) {
    tl.fromTo(shadowPseudo, headerShrink.shadow.from, { ...headerShrink.shadow.to, ease: "none" }, 0);
  }

  // Desktop-only: logo, width, opacity, logo morph
  const logo = logoRef.current;
  const icon = iconRef.current?.root;
  ScrollTrigger.matchMedia({
    "(min-width: 768px)": () => {
      if (logo) {
        tl.fromTo(logo, headerShrink.logo.from, { ...headerShrink.logo.to, ease: "none" }, 0);
        tl.fromTo(
          logo,
          headerShrink.logoMorph.text.from,
          { ...headerShrink.logoMorph.text.to, ease: "none" },
          0,
        );
      }
      if (icon) {
        tl.fromTo(
          icon,
          headerShrink.logoMorph.icon.from,
          { ...headerShrink.logoMorph.icon.to, ease: "none" },
          0,
        );
      }
    },
  });
}, [reducedMotion, isContactPage]);
```

Also, in the Header JSX, add the shadow target element inside the header (as the first child, before any other content). GSAP can't animate CSS pseudo-elements directly, so we use an actual element:

```tsx
{/* Shadow layer — opacity animated instead of per-frame boxShadow */}
<div
  className="header-shadow-target absolute inset-0 rounded-[inherit] pointer-events-none"
  style={{ boxShadow: "0 4px 30px rgba(0,0,0,0.3)", opacity: 0 }}
  aria-hidden="true"
/>
```

- [ ] **Step 3: Verify the header still shrinks correctly**

Run: `npm run dev`

1. Navigate to the home page
2. Scroll past 150px — header should shrink from 5rem to 3.5rem
3. Shadow should fade in during scroll
4. Logo should morph from text to icon on desktop
5. Verify there is NO `backdrop-filter` being set (open DevTools → Elements → check computed styles on the header during scroll)

- [ ] **Step 4: Verify type-check passes**

Run: `npm run type-check`
Expected: No errors.

- [ ] **Step 5: Commit**

```bash
git add src/lib/animations.ts src/components/layout/Header.tsx
git commit -m "perf: remove per-frame backdrop-filter writes, move boxShadow to opacity-animated overlay"
```

---

### Task 3: CinematicHeroReveal — Overlay Technique

**Files:**
- Modify: `src/lib/animations.ts`
- Modify: `src/components/sections/gallery/GalleryHero.tsx`

Replace `filter: blur(20px) brightness(0.1)` animation with two overlay layers whose opacity fades — visually identical, but only `opacity` and `transform` animate per frame.

- [ ] **Step 1: Update cinematicHeroReveal preset in animations.ts**

In `src/lib/animations.ts`, update the `image` sub-preset to remove the `filter` animation. The `scale` stays (compositor-friendly). Add new overlay presets:

```tsx
export const cinematicHeroReveal = {
  /** Image only uses scale — filter work moves to overlay layers */
  image: {
    from: { scale: 1.15 },
    to: { scale: 1, duration: 3.5, ease: "power3.inOut" },
  },
  /** Dark overlay: simulates brightness(0.1) → brightness(1) */
  darkOverlay: {
    from: { opacity: 0.9 },
    to: { opacity: 0, duration: 3.5, ease: "power3.inOut" },
  },
  /** Blur overlay: static backdrop-filter, opacity fades out */
  blurOverlay: {
    from: { opacity: 1 },
    to: { opacity: 0, duration: 3.5, ease: "power3.inOut" },
  },
  nav: {
    from: { y: -20, autoAlpha: 0 },
    to: { y: 0, autoAlpha: 1, duration: 1, stagger: 0.1, ease: easings.smooth },
  },
  chapterLabel: {
    from: { y: 20, autoAlpha: 0 },
    to: { autoAlpha: 1, y: 0, duration: 1, ease: easings.smooth },
  },
  titleLine: {
    from: { y: 100, autoAlpha: 0, rotationX: -20 },
    to: { y: 0, autoAlpha: 1, rotationX: 0, duration: 1.5, ease: easings.smooth, stagger: 0.2 },
  },
  description: {
    from: { y: 20, autoAlpha: 0 },
    to: { y: 0, autoAlpha: 1, duration: 1, ease: easings.smooth },
  },
  scrollCue: {
    from: { autoAlpha: 0 },
    to: { autoAlpha: 1, duration: 1, ease: easings.smooth },
  },
  parallax: {
    to: { yPercent: 20, ease: "none" },
    scrollTrigger: { start: "top top", end: "bottom top", scrub: true },
  },
};
```

- [ ] **Step 2: Add overlay elements to GalleryHero JSX**

In `src/components/sections/gallery/GalleryHero.tsx`, add two overlay divs inside the image container (after the `<Image>` element, before the closing `</div>` of the image shell). These sit on top of the image:

```tsx
{/* Hero image */}
<div className="absolute inset-0 w-full h-full overflow-hidden" style={imageShellStyle}>
  {coverUrl ? (
    <Image
      alt={album.title}
      className="gallery-hero-img object-cover w-full h-full"
      src={coverUrl}
      fill
      priority={priority}
      sizes="100vw"
      placeholder={album.blurDataURL ? "blur" : undefined}
      blurDataURL={album.blurDataURL}
    />
  ) : (
    <div className="gallery-hero-img h-full w-full bg-gradient-to-br from-surface to-surface-elevated" />
  )}
  {/* Cinematic overlays — replace per-frame filter animation */}
  <div
    className="gallery-hero-blur-overlay absolute inset-0"
    style={{ backdropFilter: "blur(20px)", WebkitBackdropFilter: "blur(20px)" }}
    aria-hidden="true"
  />
  <div
    className="gallery-hero-dark-overlay absolute inset-0 bg-black"
    aria-hidden="true"
  />
</div>
```

- [ ] **Step 3: Update GalleryHero animation to use overlays**

In the `useGSAP` hook, update the timeline to animate overlay opacity instead of image filter:

```tsx
useGSAP(
  () => {
    const el = sectionRef.current;
    if (!el) return;

    if (reduced) {
      gsap.set(
        el.querySelectorAll(
          ".gallery-hero-img, .gallery-hero-label, .gallery-hero-title-line, .gallery-hero-desc, .gallery-hero-scroll",
        ),
        { autoAlpha: 1, y: 0, scale: 1, rotationX: 0 },
      );
      // Hide overlays immediately in reduced motion
      gsap.set(el.querySelector(".gallery-hero-dark-overlay"), { opacity: 0 });
      gsap.set(el.querySelector(".gallery-hero-blur-overlay"), { opacity: 0 });
      return;
    }

    const tl = gsap.timeline({ ...withWillChange() });

    // Image scale only (no filter)
    tl.fromTo(el.querySelector(".gallery-hero-img"), cinematicHeroReveal.image.from, {
      ...cinematicHeroReveal.image.to,
    });

    // Dark overlay fades out (simulates brightness reveal)
    tl.fromTo(
      el.querySelector(".gallery-hero-dark-overlay"),
      cinematicHeroReveal.darkOverlay.from,
      { ...cinematicHeroReveal.darkOverlay.to },
      0,
    );

    // Blur overlay fades out (simulates blur dissolve)
    tl.fromTo(
      el.querySelector(".gallery-hero-blur-overlay"),
      cinematicHeroReveal.blurOverlay.from,
      { ...cinematicHeroReveal.blurOverlay.to },
      0,
    );

    // Rest of the timeline unchanged
    tl.fromTo(
      el.querySelector(".gallery-hero-label"),
      cinematicHeroReveal.chapterLabel.from,
      { ...cinematicHeroReveal.chapterLabel.to },
      "-=1.5",
    )
      .fromTo(
        el.querySelectorAll(".gallery-hero-title-line"),
        cinematicHeroReveal.titleLine.from,
        { ...cinematicHeroReveal.titleLine.to },
        "-=1.2",
      )
      .fromTo(
        el.querySelector(".gallery-hero-desc"),
        cinematicHeroReveal.description.from,
        { ...cinematicHeroReveal.description.to },
        "-=1",
      )
      .fromTo(
        el.querySelector(".gallery-hero-scroll"),
        cinematicHeroReveal.scrollCue.from,
        { ...cinematicHeroReveal.scrollCue.to },
        "-=0.5",
      );

    // Parallax on scroll (unchanged — already uses transform only)
    const img = el.querySelector<HTMLElement>(".gallery-hero-img");
    if (!smoothMode && img) {
      gsap.to(img, {
        ...cinematicHeroReveal.parallax.to,
        scrollTrigger: {
          trigger: el,
          ...cinematicHeroReveal.parallax.scrollTrigger,
          onEnter: () => {
            img.style.willChange = "transform";
          },
          onLeave: () => {
            img.style.willChange = "auto";
          },
          onEnterBack: () => {
            img.style.willChange = "transform";
          },
          onLeaveBack: () => {
            img.style.willChange = "auto";
          },
        },
      });
    }
  },
  { scope: sectionRef, dependencies: [reduced, smoothMode] },
);
```

- [ ] **Step 4: Verify the cinematic reveal looks correct**

Run: `npm run dev`

Navigate to `/work` — the first gallery hero section should:
1. Start dark (black overlay) and blurry (blur overlay)
2. Progressively reveal the sharp, bright image over 3.5s
3. Scale from 1.15 → 1 simultaneously
4. Text elements should still stagger in on top

Open DevTools Performance tab and record the reveal. Verify no `filter` style writes in the flame chart.

- [ ] **Step 5: Verify type-check passes**

Run: `npm run type-check`
Expected: No errors.

- [ ] **Step 6: Commit**

```bash
git add src/lib/animations.ts src/components/sections/gallery/GalleryHero.tsx
git commit -m "perf: replace filter animation with opacity overlays in cinematic hero reveal"
```

---

### Task 4: Input Listener Optimization

**Files:**
- Modify: `src/components/ui/CustomCursor.tsx`
- Modify: `src/hooks/useMagnetic.ts`

Throttle high-frequency mousemove handlers that currently fire on every pixel of movement.

- [ ] **Step 1: rAF-gate the CustomCursor mousemove handler**

In `src/components/ui/CustomCursor.tsx`, wrap the mousemove handler with requestAnimationFrame gating. The `gsap.quickTo()` calls already interpolate, so one update per frame is visually identical.

Replace the current handler and listener setup. The key change is storing the latest event data and processing it once per animation frame:

```tsx
// Replace the handleMouseMove function and its listener setup:

const latestMouse = useRef({ x: 0, y: 0 });
const frameId = useRef<number | null>(null);

// Inside useGSAP, replace handleMouseMove:
const processMouseMove = () => {
  const { x, y, rot } = quickToRefs.current;
  const mx = latestMouse.current.x;
  const my = latestMouse.current.y;

  x?.(mx - 20);
  y?.(my - 20);

  const dx = mx - prevMouse.current.x;
  const dy = my - prevMouse.current.y;
  const dist = Math.hypot(dx, dy);
  angle.current += dist * 0.5;
  rot?.(angle.current);

  prevMouse.current.x = mx;
  prevMouse.current.y = my;
  frameId.current = null;
};

const handleMouseMove = (event: MouseEvent) => {
  latestMouse.current.x = event.clientX;
  latestMouse.current.y = event.clientY;
  if (frameId.current === null) {
    frameId.current = requestAnimationFrame(processMouseMove);
  }
};
```

In the cleanup, also cancel the pending frame:

```tsx
return () => {
  if (frameId.current !== null) cancelAnimationFrame(frameId.current);
  style.remove();
  window.removeEventListener("mousemove", handleMouseMove);
  window.removeEventListener("mouseover", handleMouseOver);
  gsap.killTweensOf(cursor);
  gsap.killTweensOf(blades);
};
```

- [ ] **Step 2: Cache getBoundingClientRect in useMagnetic**

In `src/hooks/useMagnetic.ts`, cache the element rect on `mouseenter` instead of reading it on every `mousemove`. Add a `resize` listener to invalidate the cache:

```tsx
// Replace the current implementation inside the useGSAP callback:

let cachedRect: DOMRect | null = null;

const handleMouseEnter = () => {
  cachedRect = el.getBoundingClientRect();
};

const handleMouseMove = (e: MouseEvent) => {
  if (!cachedRect) cachedRect = el.getBoundingClientRect();
  const centerX = cachedRect.left + cachedRect.width / 2;
  const centerY = cachedRect.top + cachedRect.height / 2;
  const dx = e.clientX - centerX;
  const dy = e.clientY - centerY;
  const distance = Math.sqrt(dx * dx + dy * dy);

  if (distance < radius) {
    gsap.to(el, {
      x: dx * strength,
      y: dy * strength,
      duration: 0.25,
      ease: magneticPull.ease,
      overwrite: true,
    });
  }
};

const handleMouseLeave = () => {
  cachedRect = null;
  gsap.to(el, {
    x: 0,
    y: 0,
    duration: 0.4,
    ease: magneticPull.returnEase,
    overwrite: true,
  });
};

const handleResize = () => {
  cachedRect = null;
};

el.addEventListener("mouseenter", handleMouseEnter);
el.addEventListener("mousemove", handleMouseMove);
el.addEventListener("mouseleave", handleMouseLeave);
window.addEventListener("resize", handleResize);

return () => {
  el.removeEventListener("mouseenter", handleMouseEnter);
  el.removeEventListener("mousemove", handleMouseMove);
  el.removeEventListener("mouseleave", handleMouseLeave);
  window.removeEventListener("resize", handleResize);
  gsap.killTweensOf(el);
};
```

- [ ] **Step 3: Verify cursor and magnetic buttons work correctly**

Run: `npm run dev`

1. Move cursor around — should follow smoothly (no visible difference from before)
2. Hover over nav links / buttons — magnetic pull should work as before
3. Resize window, then hover — should still calculate correctly

- [ ] **Step 4: Commit**

```bash
git add src/components/ui/CustomCursor.tsx src/hooks/useMagnetic.ts
git commit -m "perf: rAF-gate cursor mousemove, cache magnetic button rect"
```

---

### Task 5: FolioGallery Virtualization

**Files:**
- Modify: `src/components/sections/FolioGallery.tsx`

Only mount gallery page sections when they're within 1 viewport of visibility. This dramatically reduces the number of active ScrollTriggers on image-heavy project pages.

- [ ] **Step 1: Add useVisiblePages hook inside FolioGallery**

At the top of the file (inside the module, before the component), add a hook that tracks which pages are near the viewport using IntersectionObserver:

```tsx
import { useState, useEffect, useCallback } from "react";

/**
 * Tracks which page indices are near the viewport.
 * Pages within rootMargin are "visible" and should mount their content.
 */
function useVisiblePages(
  containerRef: React.RefObject<HTMLElement | null>,
  pageCount: number,
  eagerCount = 2,
) {
  const [visibleSet, setVisibleSet] = useState<Set<number>>(() => {
    // Mount first N pages eagerly (above-fold content)
    const initial = new Set<number>();
    for (let i = 0; i < Math.min(eagerCount, pageCount); i++) initial.add(i);
    return initial;
  });

  useEffect(() => {
    const container = containerRef.current;
    if (!container) return;

    const pages = container.querySelectorAll<HTMLElement>(".folio-page");
    if (!pages.length) return;

    const observer = new IntersectionObserver(
      (entries) => {
        setVisibleSet((prev) => {
          const next = new Set(prev);
          entries.forEach((entry) => {
            const idx = Number(entry.target.getAttribute("data-page-index"));
            if (isNaN(idx)) return;
            if (entry.isIntersecting) {
              next.add(idx);
            } else {
              // Keep eager pages always mounted
              if (idx >= eagerCount) next.delete(idx);
            }
          });
          return next;
        });
      },
      { rootMargin: "100% 0px" }, // 1 viewport ahead/behind
    );

    pages.forEach((page) => observer.observe(page));

    return () => observer.disconnect();
  }, [containerRef, pageCount, eagerCount]);

  return visibleSet;
}
```

- [ ] **Step 2: Integrate virtualization into the page rendering**

In the FolioGallery component, use the hook and conditionally render page content:

Add refs and hook call near the top of the component:

```tsx
const visiblePages = useVisiblePages(sectionRef, pages.length, 2);
```

Then modify the `.map()` rendering (around line 696) to conditionally render content:

```tsx
{pages.map((page, i) => (
  <div
    key={i}
    data-page-index={i}
    className="folio-page relative overflow-hidden"
    style={{
      minHeight: "100vh",
      borderTop:
        i > 0
          ? "1px solid color-mix(in srgb, var(--color-primary) 15%, transparent)"
          : undefined,
      // Offscreen pages: maintain height but skip rendering
      ...(!visiblePages.has(i) ? { contentVisibility: "auto", containIntrinsicSize: "auto 100vh" } : {}),
    }}
  >
    {visiblePages.has(i) && (
      <>
        {page.layout === "title" && <TitleContent title={title} count={images.length} />}
        {page.layout === "full-bleed" && (
          <FullBleedContent
            image={page.images[0]}
            index={page.imageIndex}
            pageNumber={page.pageNumber}
          />
        )}
        {page.layout === "centered-plate" && (
          <CenteredPlateContent
            image={page.images[0]}
            index={page.imageIndex}
            pageNumber={page.pageNumber}
          />
        )}
        {page.layout === "diptych" && (
          <DiptychContent
            images={page.images}
            startIndex={page.imageIndex}
            pageNumber={page.pageNumber}
          />
        )}
        {page.layout === "detail-crop" && (
          <DetailCropContent
            image={page.images[0]}
            index={page.imageIndex}
            pageNumber={page.pageNumber}
          />
        )}
        {page.layout === "video" && page.videoUrl && (
          <VideoContent videoUrl={page.videoUrl} pageNumber={page.pageNumber} />
        )}
        {page.layout === "colophon" && <ColophonContent />}
      </>
    )}
  </div>
))}
```

- [ ] **Step 3: Verify virtualization works**

Run: `npm run dev`

Navigate to a project with many images (e.g., `/work/miami-vs-smu` which has 16 images).

1. Open DevTools Elements panel
2. Scroll through the gallery
3. Check that `.folio-page` divs far from viewport are empty (no children rendered)
4. Pages near the viewport should have full content
5. Scrolling back should re-mount previously unmounted pages
6. All animations should still fire correctly when pages mount

Also check in console:
```js
document.querySelectorAll('.folio-page').length  // Should be total page count (all placeholders exist)
document.querySelectorAll('.folio-reveal').length // Should be ~3-6 (only mounted pages' images)
```

- [ ] **Step 4: Verify type-check passes**

Run: `npm run type-check`
Expected: No errors.

- [ ] **Step 5: Commit**

```bash
git add src/components/sections/FolioGallery.tsx
git commit -m "perf: virtualize FolioGallery pages with IntersectionObserver"
```

---

### Task 6: CSS Containment & will-change Consistency

**Files:**
- Modify: `src/components/sections/gallery/GalleryBentoSplit.tsx`
- Modify: `src/components/sections/gallery/GalleryFullBleed.tsx`
- Modify: `src/components/sections/gallery/GalleryTextureCards.tsx`
- Modify: `src/styles/globals.css`

Apply `content-visibility: auto` on all gallery sections by default (not just "smooth" mode), and standardize `will-change` lifecycle management.

- [ ] **Step 1: Apply content-visibility to all gallery sections**

In each gallery component (`GalleryBentoSplit.tsx`, `GalleryFullBleed.tsx`, `GalleryTextureCards.tsx`), update the `sectionStyle` to ALWAYS apply `content-visibility: auto` when `deferOffscreen` is true, regardless of `performanceMode`:

The current pattern in each file is:
```tsx
const sectionStyle: CSSProperties | undefined =
  smoothMode && deferOffscreen
    ? { contentVisibility: "auto", containIntrinsicSize: "100vh", contain: "layout paint" }
    : undefined;
```

Change to:
```tsx
const sectionStyle: CSSProperties | undefined = deferOffscreen
  ? { contentVisibility: "auto", containIntrinsicSize: "auto 100vh", contain: "layout style paint" }
  : undefined;
```

Note two changes:
1. Removed `smoothMode &&` condition — always apply when deferring offscreen
2. Changed `containIntrinsicSize` to `"auto 100vh"` — the `auto` keyword lets the browser remember the rendered size after first paint, preventing scroll jumps
3. Added `style` to the `contain` property for additional isolation

Apply this same change to all three files.

- [ ] **Step 2: Add gallery containment defaults in globals.css**

In `src/styles/globals.css`, add a default containment rule for gallery sections:

```css
/* Gallery section containment — isolates repaints between sections */
.gallery-section-contained {
  contain: layout style paint;
}
```

This class can be applied to gallery section wrappers that don't use the `deferOffscreen` prop (e.g., the hero section which is always visible).

- [ ] **Step 3: Verify no visual regressions**

Run: `npm run dev`

1. Navigate to `/work` — all gallery sections should render correctly
2. Scroll through — animations should fire at the right scroll positions
3. Check that `content-visibility: auto` sections don't cause scroll bar jumps (the `auto` keyword in `contain-intrinsic-size` prevents this)

- [ ] **Step 4: Verify build passes**

Run: `npm run build`
Expected: Build succeeds with no errors.

- [ ] **Step 5: Commit**

```bash
git add src/components/sections/gallery/GalleryBentoSplit.tsx src/components/sections/gallery/GalleryFullBleed.tsx src/components/sections/gallery/GalleryTextureCards.tsx src/styles/globals.css
git commit -m "perf: apply content-visibility and containment to all gallery sections"
```

---

## Final Verification

After all tasks are complete:

- [ ] **Full build check:** `npm run build` passes
- [ ] **Type check:** `npm run type-check` passes
- [ ] **Lint:** `npm run lint` passes
- [ ] **Chrome DevTools Performance recording:** Record scroll on `/work` and `/work/[slug]` — compare main thread busy time to pre-optimization baseline
- [ ] **ScrollTrigger count after navigation:** Console check `ScrollTrigger.getAll().length` after navigating 5+ pages — should stay constant (not accumulate)
- [ ] **FolioGallery DOM check:** On a 16-image project, only ~3 page sections should have content mounted at any time
- [ ] **Visual spot check:** Header shrink, hero reveal, gallery animations all look identical to before
