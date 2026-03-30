# Hero Animation & Image Quality — Design Spec

## Problem

The landing hero image is static — no motion, no life. Once loaded, it just sits there until the user scrolls past. For a photography studio site, the hero image should feel cinematic.

Separately, the image pipeline double-compresses hero images: source at mozjpeg q85 → Next.js re-encodes to AVIF/WebP at default q75. The source is also capped at 3200px when the originals are 6000×4000 from a Sony A7 III. This means Retina/4K screens don't get sharp enough imagery.

## Part 1: Ken Burns + Scroll Parallax

### Ambient Ken Burns (time-based)

- Targets `bgImageRef` (the image wrapper div inside the left column)
- `scale: 1 → 1.06` over 10 seconds, `ease: "none"` (linear, steady creep)
- Plays once on page load after the entrance animation completes (starts at position ~1.8s on the entrance timeline, or as a separate tween with a 1.8s delay)
- No loop, no yoyo — holds at 1.06 after completing
- Gives the image a living, breathing quality while the user reads the headline

### Scroll Parallax Fade-Out (scroll-driven)

- Also targets `bgImageRef`
- `yPercent: 0 → 30` — image drifts up slower than the page
- `autoAlpha: 1 → 0` — fades out as user scrolls past
- `filter: blur(0px) → blur(8px)` — softens as it recedes
- ScrollTrigger: `trigger: sectionRef`, `start: "top top"`, `end: "bottom top"`, `scrub: true`, `ease: "none"`

### How they coexist

Both target the same element. The Ken Burns tween is time-based (GSAP timeline). The scroll parallax is ScrollTrigger-driven. GSAP composites both — the scroll yPercent/autoAlpha/filter layer on top of the ambient scale.

### Animation config location

Update the existing `landingHeroParallax` in `src/lib/animations.ts` to include both the Ken Burns and scroll configs. Currently this config is defined but unused — we'll wire it up.

```
export const landingHeroParallax = {
  kenBurns: {
    from: { scale: 1 },
    to: { scale: 1.06, duration: 10, ease: "none" },
    delay: 1.8,  // after entrance animation settles
  },
  scroll: {
    to: { yPercent: 30, autoAlpha: 0, filter: "blur(8px)", ease: "none" },
    scrollTrigger: { start: "top top", end: "bottom top", scrub: true },
  },
};
```

### willChange management

Add `onEnter`/`onLeave`/`onEnterBack`/`onLeaveBack` callbacks to the ScrollTrigger to toggle `willChange: "transform, opacity, filter"` — follows the pattern used in `GalleryFullBleed` and `GalleryTextureCards`.

### Reduced motion

Both effects skipped. `gsap.set(bgImageRef.current, { autoAlpha: 1, scale: 1, y: 0 })`.

Add to `reducedMotionFallbacks`: `landingHeroParallax: "no ambient zoom, no scroll parallax/fade/blur"`.

## Part 2: Image Quality

### Higher-res hero source

Update `scripts/process-images.mjs`:
- Add a `HERO_MAX_DIM = 4800` constant
- Add a `HERO_QUALITY = 95` constant
- Add optional `maxDim` and `quality` params to `processImage(srcPath, destPath, { maxDim?, quality? })`
- `processSingle` gets the same optional bag and forwards it
- The landing hero call passes `{ maxDim: HERO_MAX_DIM, quality: HERO_QUALITY }`
- The hero goes from 2133×3200 at q85 → ~3200×4800 at q95 (keeping portrait orientation)

This gives the browser a much sharper source to work with, especially at 2x/3x DPR.

### Quality prop in SiteMedia

In `src/components/ui/SiteMedia.tsx`:
- Change the type from `Omit<ImageProps, "src" | "loader" | "quality">` to `Omit<ImageProps, "src" | "loader">`
- The `quality` prop will pass through via `{...rest}` to the Next.js `Image` component — no other changes needed

### Hero quality override

In `src/components/landing/LandingHero.tsx`:
- Add `quality={90}` to the `<SiteMedia>` component

AVIF at q90 is visually near-lossless for photographs and still significantly smaller than JPEG at q75.

### Fix sizes attribute

In `src/components/landing/LandingHero.tsx`:
- Change `sizes` from `"(max-width: 768px) 100vw, 55vw"` to `"(max-width: 768px) 100vw, 44vw"`
- Matches the actual grid column width (`44%`), preventing the browser from downloading an unnecessarily large variant

### Update site-media dimensions

In `src/lib/site-media.ts`:
- Update `LANDING_MEDIA.hero.width` and `height` to match the new processed dimensions after running the updated script

## Files Modified

| File | Change |
|------|--------|
| `src/lib/animations.ts` | Rewrite `landingHeroParallax` config with kenBurns + scroll sections |
| `src/components/landing/LandingHero.tsx` | Wire up Ken Burns + scroll parallax, add `quality={90}`, fix `sizes` |
| `src/components/ui/SiteMedia.tsx` | Allow `quality` prop passthrough |
| `scripts/process-images.mjs` | Add hero-specific max dimension (4800px) and quality (95) overrides |
| `src/lib/site-media.ts` | Update hero dimensions after reprocessing |

## Verification

1. Run `node scripts/process-images.mjs` — confirm hero output is ~3200×4800 at higher file size than before
2. Run dev server (`npm run dev`)
3. Open landing page:
   - Image should fade in (entrance animation), then slowly zoom in over ~10s
   - Scrolling down: image should drift up, fade out, and blur smoothly
   - Check DevTools Network tab: hero should be served as AVIF at ~q90
   - Compare visual quality to the old version (sharper details, better color fidelity)
4. Test reduced motion: enable `prefers-reduced-motion: reduce` in DevTools — image should be static
5. Test mobile: image should be full-width, animations still work
