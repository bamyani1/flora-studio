# Full-Image Display — Design Spec

## Context

Images across the Bahar Studio portfolio are cropped by `object-cover` + fixed aspect ratios / viewport heights. The owner wants every photo shown in its full dimensions without content being cut off.

**Decision:** Use dynamic `aspect-ratio` containers derived from real image dimensions. Heroes (full-viewport cinematic backdrops) keep `object-cover`. All animation code stays untouched.

## Strategy

**Dynamic Aspect-Ratio (Strategy B)** — Set `style={{ aspectRatio: w/h }}` on image containers using real dimensions. Keep `fill` + `object-cover` on `<Image>`. Since the container ratio matches the image, `object-cover` produces zero crop. The `fill` + `absolute inset-0` structure is preserved, so all GSAP parallax/reveal animations continue working unchanged.

**Dimension sources:**

- Sanity images: parse `width x height` from `_ref` string (`image-{hash}-{w}x{h}-{ext}`)
- Static images: read from `image-manifest.json` (already has `width` and `height` per entry)

## Scope

### Files to Change (9 total)

| File                                                      | Change                                                                    |
| --------------------------------------------------------- | ------------------------------------------------------------------------- |
| `src/lib/image-url.ts`                                    | Add `getImageDimensions()` — extract w/h from Sanity `_ref`               |
| `src/lib/image-manifest.ts`                               | Add `getLocalDimensions()` — expose w/h from manifest                     |
| `src/components/sections/gallery/GalleryFullBleed.tsx`    | Plate: `aspect-[16/10]` → dynamic `aspect-ratio` + `maxHeight: 85dvh`     |
| `src/components/sections/gallery/GalleryBentoSplit.tsx`   | Image col: `min-h-screen` → dynamic `aspect-ratio` + `min-h-[40vh]` floor |
| `src/components/sections/gallery/GalleryTextureCards.tsx` | Grid → flex layout, each card: dynamic `aspect-ratio`                     |
| `src/components/landing/CinematicImageReveal.tsx`         | Accept `width`/`height` props, set `aspect-ratio` on container            |
| `src/components/landing/LandingEditorial.tsx`             | Remove `h-[60vh] md:h-[800px]`, pass dims from manifest                   |
| `src/components/landing/LandingExhibition.tsx`            | Remove `h-[70vh] md:h-[900px]`, pass dims from manifest                   |
| `src/components/landing/LandingStudio.tsx`                | Remove `h-[60vh] md:h-[700px]`, pass dims from manifest                   |

### Unchanged

- **Heroes (keep cover):** Hero.tsx, AlbumHero.tsx, GalleryHero.tsx, ExhibitionFeature.tsx, LandingHero.tsx, FolioGallery full-bleed
- **Already correct:** FolioGallery centered-plate, diptych, detail-crop (use `object-contain`)
- **Dead code (not rendered):** FilterableGrid, ProjectCard, CuratedCollections — not imported by any page

## Component Details

### 0. Dimension Utilities

**`getImageDimensions()`** in `src/lib/image-url.ts`:

```ts
export function getImageDimensions(img?: SanityImage | null) {
  const match = img?.asset?._ref?.match(/-(\d+)x(\d+)-/);
  return match ? { width: +match[1], height: +match[2] } : null;
}
```

Works for `ALBUMS_QUERY` and `FEATURED_ALBUMS_QUERY` which fetch `coverImage` without `asset->`, so `_ref` is present.

**`getLocalDimensions()`** in `src/lib/image-manifest.ts`:

```ts
export function getLocalDimensions(path: string) {
  const entry = data[path];
  return entry ? { width: entry.width, height: entry.height } : undefined;
}
```

Used by landing sections which reference local static images.

### 1. GalleryFullBleed

The centered "plate" inside the full-viewport dark section.

**Before:**

```tsx
<TransitionLink className="relative z-10 w-4/5 md:w-2/3 aspect-[16/10] overflow-hidden block">
```

**After:**

```tsx
const dims = getImageDimensions(album.coverImage);
// ...
<TransitionLink
  className="relative z-10 w-4/5 md:w-2/3 overflow-hidden block"
  style={{ aspectRatio: dims ? `${dims.width}/${dims.height}` : '16/10', maxHeight: '85dvh' }}
>
```

- `maxHeight: 85dvh` prevents portrait images from exceeding viewport
- When `maxHeight` caps height on very tall portraits, `object-cover` does minimal edge crop — far less than the current blanket 16:10 crop
- Fallback to `16/10` if dimensions unavailable
- Image element, parallax animation, text overlay, hover scale — all unchanged

### 2. GalleryBentoSplit

8-column image + 4-column text split layout.

**Before (image column):**

```tsx
<div className="md:col-span-8 relative overflow-hidden min-h-[50vh] md:min-h-screen ...">
```

**After:**

```tsx
const dims = getImageDimensions(album.coverImage);
// ...
<div
  className="md:col-span-8 relative overflow-hidden min-h-[40vh] ..."
  style={{ aspectRatio: dims ? `${dims.width}/${dims.height}` : undefined }}
>
```

- `min-h-[40vh]` floor for very wide landscapes on mobile
- Section keeps `min-h-screen` on the outer `<section>` — ensures minimum section height
- Text column (`md:col-span-4`) uses `justify-center` — handles any image-driven height
- Parallax (`bento-img-wrapper` scale + yPercent scrub) unchanged — same absolute structure

### 3. GalleryTextureCards

Two album cards side by side.

**Before (section):**

```tsx
<section className="... grid grid-cols-1 md:grid-cols-2 gap-px ...">
```

**After (section):**

```tsx
<section className="... flex flex-col md:flex-row gap-px ..." style={{ minHeight: 0 }}>
```

**Before (each card):**

```tsx
<TransitionLink className="... min-h-[50vh] md:min-h-screen ...">
```

**After (each card):**

```tsx
const dims = getImageDimensions(album.coverImage);
// ...
<TransitionLink
  className="... min-h-[40vh] w-full md:w-1/2 ..."
  style={{ aspectRatio: dims ? `${dims.width}/${dims.height}` : undefined }}
>
```

- Flex layout allows independent card heights (portrait card taller than landscape)
- Each card height driven by its image's natural ratio
- Text overlay (`absolute inset-x-0 bottom-0`) still anchors to each card's own bottom
- Gradient overlay (`absolute inset-x-0 bottom-0 h-1/3`) adjusts with card height
- Per-card parallax (texture-img scale + yPercent) unchanged

### 4. CinematicImageReveal

Shared component used by all three landing sections.

**Before (props):**

```ts
interface CinematicImageRevealProps {
  src: string;
  alt: string;
  className?: string;
  overlay?: boolean;
  blurDataURL?: string | null;
  priority?: boolean;
  sizes?: string;
}
```

**After (props):**

```ts
interface CinematicImageRevealProps {
  src: string;
  alt: string;
  className?: string;
  overlay?: boolean;
  blurDataURL?: string | null;
  priority?: boolean;
  sizes?: string;
  width?: number; // NEW
  height?: number; // NEW
}
```

**Before (container):**

```tsx
<div ref={containerRef} className={`relative overflow-hidden ${className}`}>
```

**After (container):**

```tsx
<div
  ref={containerRef}
  className={`relative overflow-hidden ${className}`}
  style={{ aspectRatio: width && height ? `${width}/${height}` : undefined }}
>
```

- Container has definite width from `w-full` (passed in className) → `aspect-ratio` resolves height
- `.cinematic-img-wrapper` (`absolute inset-0`) fills the container as before
- Clip-path reveal and parallax animations untouched

### 5. Landing Section Parents

All three follow the same pattern:

**LandingEditorial — Before:**

```tsx
<CinematicImageReveal
  src="/images/editorial-hero.jpg"
  className="w-full h-[60vh] md:h-[800px] mb-24 md:mb-32"
  ...
/>
```

**After:**

```tsx
const dims = getLocalDimensions("/images/editorial-hero.jpg");
// ...
<CinematicImageReveal
  src="/images/editorial-hero.jpg"
  className="w-full mb-24 md:mb-32"
  width={dims?.width}
  height={dims?.height}
  ...
/>
```

Same pattern for LandingExhibition (`h-[70vh] md:h-[900px]` removed) and LandingStudio (`h-[60vh] md:h-[700px]` removed).

**LandingExhibition note:** The skewed background (`absolute inset-0 -skew-y-3`) fills via `inset-0`, so it auto-adjusts to the new section height driven by the image ratio.

## Why This Works

1. **CSS `aspect-ratio` on a block with definite width establishes height** — regardless of whether children are absolutely positioned. The containers all have `w-full` providing definite width.

2. **`object-cover` with matching ratio = zero crop** — When container `aspect-ratio` matches the image, `object-cover` scales the image to exactly fill without any clipping.

3. **Parallax edge note** — Components using parallax (scale 1.1, yPercent ±15) temporarily hide ~10-15% of image edges during scroll motion. At rest, the full image is visible. This is inherent to parallax and is a negligible trade-off vs. the current permanent full-crop.

## Verification

1. `npm run dev` — visually inspect each changed component:
   - `/work` page: scroll through GalleryFullBleed, GalleryBentoSplit, GalleryTextureCards sections
   - Landing page: check LandingEditorial, LandingExhibition, LandingStudio
2. Compare portrait vs landscape album covers — verify no cropping
3. Verify parallax animations still fire correctly on scroll
4. Verify hover effects (scale, overlays) still work
5. Test responsive behavior (mobile + desktop breakpoints)
6. `npm run build` — ensure no TypeScript errors
7. `npm run lint` — ensure no lint issues
