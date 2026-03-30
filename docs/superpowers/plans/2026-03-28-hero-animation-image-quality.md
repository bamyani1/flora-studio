# Hero Animation & Image Quality Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add ambient Ken Burns zoom + scroll parallax fade-out to the landing hero image, and improve hero image quality by raising source resolution and serving quality.

**Architecture:** Two independent concerns wired into the same component. Animation: rewrite the unused `landingHeroParallax` config in animations.ts with kenBurns + scroll sections, wire it up in LandingHero's `useGSAP` hook. Image quality: allow quality passthrough in SiteMedia, update the processing script for higher-res hero sources, fix the sizes attribute.

**Tech Stack:** GSAP (ScrollTrigger), Next.js Image, sharp (mozjpeg)

---

### Task 1: Update animation config

**Files:**
- Modify: `src/lib/animations.ts:452-461` (rewrite `landingHeroParallax`)

- [ ] **Step 1: Rewrite `landingHeroParallax` config**

Replace the existing block at lines 452-461:

```typescript
// --------------------------------------------------
// landingHeroParallax — Ambient Ken Burns zoom + scroll-driven parallax fade
// --------------------------------------------------
export const landingHeroParallax = {
  kenBurns: {
    from: { scale: 1 },
    to: { scale: 1.06, duration: 10, ease: "none" },
    delay: 1.8,
  },
  scroll: {
    to: { yPercent: 30, autoAlpha: 0, filter: "blur(8px)", ease: "none" },
    scrollTrigger: { start: "top top", end: "bottom top", scrub: true },
  },
};
```

- [ ] **Step 2: Update `reducedMotionFallbacks`**

Find the `landingHeroParallax` entry in `reducedMotionFallbacks` (~line 603) and replace:

```typescript
  landingHeroParallax: "no ambient zoom, no scroll parallax/fade/blur",
```

- [ ] **Step 3: Verify build**

Run: `npx next build 2>&1 | head -20`
Expected: No TypeScript errors related to animations.ts

- [ ] **Step 4: Commit**

```bash
git add src/lib/animations.ts
git commit -m "update landingHeroParallax with Ken Burns + scroll config"
```

---

### Task 2: Wire up animations in LandingHero

**Files:**
- Modify: `src/components/landing/LandingHero.tsx`

- [ ] **Step 1: Add import**

Add `landingHeroParallax` to the existing import from `@/lib/animations`:

```typescript
import { landingHeroGridSequence, landingHeroParallax } from "@/lib/animations";
```

- [ ] **Step 2: Add Ken Burns + scroll parallax after the entrance timeline**

Inside the `useGSAP` callback, after the existing headline parallax block (after line 58), add:

```typescript
      // Ken Burns — ambient slow zoom
      if (bgImageRef.current) {
        gsap.fromTo(
          bgImageRef.current,
          landingHeroParallax.kenBurns.from,
          {
            ...landingHeroParallax.kenBurns.to,
            delay: landingHeroParallax.kenBurns.delay,
          },
        );

        // Scroll parallax — drift up, fade, blur
        const img = bgImageRef.current;
        gsap.to(img, {
          ...landingHeroParallax.scroll.to,
          scrollTrigger: {
            trigger: sectionRef.current,
            ...landingHeroParallax.scroll.scrollTrigger,
            onEnter: () => { img.style.willChange = "transform, opacity, filter"; },
            onLeave: () => { img.style.willChange = "auto"; },
            onEnterBack: () => { img.style.willChange = "transform, opacity, filter"; },
            onLeaveBack: () => { img.style.willChange = "auto"; },
          },
        });
      }
```

- [ ] **Step 3: Update reduced motion fallback**

In the `if (reducedMotion)` block (lines 31-36), the existing loop already sets `autoAlpha: 1, y: 0` on `bgImageRef`. Add `scale: 1` to the set call to ensure the Ken Burns scale is also reset:

```typescript
      if (reducedMotion) {
        for (const ref of Object.values(refs)) {
          if (ref.current) gsap.set(ref.current, { autoAlpha: 1, y: 0, scale: 1 });
        }
        return;
      }
```

- [ ] **Step 4: Verify in dev server**

Run: `npm run dev`

Open http://localhost:3000 and verify:
- Image fades in (entrance), then slowly zooms in over ~10s
- Scrolling down: image drifts up, fades out, blurs
- No console errors

- [ ] **Step 5: Commit**

```bash
git add src/components/landing/LandingHero.tsx
git commit -m "wire up Ken Burns zoom and scroll parallax on hero image"
```

---

### Task 3: Allow quality prop in SiteMedia

**Files:**
- Modify: `src/components/ui/SiteMedia.tsx:8`

- [ ] **Step 1: Remove quality from Omit**

Change line 8 from:

```typescript
type SiteMediaProps = Omit<ImageProps, "src" | "loader" | "quality"> & {
```

to:

```typescript
type SiteMediaProps = Omit<ImageProps, "src" | "loader"> & {
```

No other changes — `quality` already passes through via `{...rest}`.

- [ ] **Step 2: Verify build**

Run: `npx next build 2>&1 | head -20`
Expected: No TypeScript errors

- [ ] **Step 3: Commit**

```bash
git add src/components/ui/SiteMedia.tsx
git commit -m "allow quality prop passthrough in SiteMedia"
```

---

### Task 4: Set hero quality and fix sizes

**Files:**
- Modify: `src/components/landing/LandingHero.tsx:70-76`

- [ ] **Step 1: Add quality prop and fix sizes**

Update the `<SiteMedia>` call (lines 70-77) from:

```tsx
            <SiteMedia
              src={LANDING_MEDIA.hero.src}
              alt={LANDING_MEDIA.hero.alt}
              fill
              className="object-cover"
              priority
              sizes="(max-width: 768px) 100vw, 55vw"
            />
```

to:

```tsx
            <SiteMedia
              src={LANDING_MEDIA.hero.src}
              alt={LANDING_MEDIA.hero.alt}
              fill
              className="object-cover"
              priority
              quality={90}
              sizes="(max-width: 768px) 100vw, 44vw"
            />
```

- [ ] **Step 2: Commit**

```bash
git add src/components/landing/LandingHero.tsx
git commit -m "set hero image quality to 90 and fix sizes to 44vw"
```

---

### Task 5: Update image processing script for higher-res hero

**Files:**
- Modify: `scripts/process-images.mjs`

- [ ] **Step 1: Add hero constants and update processImage signature**

After the existing constants (line 15-16), add:

```javascript
const HERO_MAX_DIM = 4800;
const HERO_QUALITY = 95;
```

Update `processImage` to accept an options bag:

```javascript
async function processImage(srcPath, destPath, opts = {}) {
  const maxDim = opts.maxDim ?? MAX_DIM;
  const quality = opts.quality ?? QUALITY;

  const dir = join(destPath, "..");
  if (!existsSync(dir)) mkdirSync(dir, { recursive: true });

  const meta = await sharp(srcPath).metadata();
  const { width, height } = meta;

  const longest = Math.max(width, height);
  let resizeOpts = undefined;
  if (longest > maxDim) {
    resizeOpts = width >= height ? { width: maxDim } : { height: maxDim };
  }

  const result = await sharp(srcPath)
    .rotate()
    .resize(resizeOpts)
    .jpeg({ quality, mozjpeg: true })
    .toFile(destPath);

  return { width: result.width, height: result.height };
}
```

- [ ] **Step 2: Update processSingle to forward opts**

```javascript
async function processSingle(srcFolder, srcFile, destFolder, destFile, opts = {}) {
  const srcPath = join(SRC, srcFolder, srcFile);
  const destPath = join(DEST, destFolder, destFile);
  if (!existsSync(srcPath)) {
    console.error(`  ⚠ Missing: ${srcPath}`);
    return;
  }
  try {
    const dims = await processImage(srcPath, destPath, opts);
    console.log(`  ✓ ${destFolder}/${destFile} (${dims.width}×${dims.height})`);
  } catch (err) {
    console.error(`  ✗ ${destFile}: ${err.message}`);
  }
}
```

- [ ] **Step 3: Update landing hero call with hero overrides**

Change the landing hero `processSingle` call (~line 87-92) from:

```javascript
  await processSingle(
    "good for heros and albums other important images",
    "DSC01886_fullres.jpg",
    "",
    "landing-hero.jpg",
  );
```

to:

```javascript
  await processSingle(
    "good for heros and albums other important images",
    "DSC01886_fullres.jpg",
    "",
    "landing-hero.jpg",
    { maxDim: HERO_MAX_DIM, quality: HERO_QUALITY },
  );
```

- [ ] **Step 4: Run the script**

Run: `node scripts/process-images.mjs`

Expected: Landing hero output should be ~3200×4800 (or proportional) and larger file size than before (was 430KB, should now be ~800KB-1.2MB at q95 and higher resolution).

- [ ] **Step 5: Update site-media dimensions**

After the script runs, check the actual output dimensions:

```bash
sips -g pixelWidth -g pixelHeight public/images/landing-hero.jpg
```

Update `src/lib/site-media.ts` lines 48-53 with the new width and height values from the output.

- [ ] **Step 6: Commit**

```bash
git add scripts/process-images.mjs src/lib/site-media.ts public/images/landing-hero.jpg
git commit -m "increase hero source to 4800px q95 for sharper Retina display"
```

---

### Task 6: Final verification

- [ ] **Step 1: Dev server visual check**

Run: `npm run dev`

Open http://localhost:3000 and verify:
1. Hero image loads with visibly sharper detail than before
2. Ken Burns zoom starts after entrance animation (~1.8s), slowly zooms in
3. Scrolling down: image drifts up, fades, and blurs smoothly
4. The border separator between image and text column is still visible
5. No layout shifts, no console errors

- [ ] **Step 2: Network tab check**

Open DevTools → Network → filter by Img. The hero image request should show:
- Format: AVIF (or WebP for unsupported browsers)
- Transferred size smaller than the source JPEG despite higher quality

- [ ] **Step 3: Reduced motion check**

In DevTools → Rendering → check "Emulate CSS media feature prefers-reduced-motion: reduce"
- Image should be static — no zoom, no parallax, no fade

- [ ] **Step 4: Mobile check**

Toggle responsive mode (375px width):
- Image fills full width, animations still work
- No horizontal overflow from the scale animation (parent has `overflow-hidden`)
