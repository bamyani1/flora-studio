# Work Page — Asymmetric Grid Layout

## Problem
The `/work` page uses full-viewport sections (hero, BentoSplit, FullBleed, TextureCards) for each album. This means one album per screen — too much scrolling to browse all albums. The immersive feel works for the hero but is excessive for every album.

## Solution
Keep the first album as a full-viewport GalleryHero. Replace all remaining sections with a 3-column asymmetric grid using the existing `ProjectCard` component. Every 3 albums group as: 1 large card (2 cols × 2 rows) + 2 small cards, alternating left/right.

## Layout

```
┌─────────────────────────────────┐
│         HERO (100vh)            │  ← GalleryHero (unchanged)
│         Chapter I               │
├────────────────────┬────────────┤
│                    │  Album 3   │
│   Album 2 (large)  │  (small)   │  ← group 1: large-left
│   2 cols × 2 rows  ├────────────┤
│                    │  Album 4   │
│                    │  (small)   │
├────────────┬───────┴────────────┤
│  Album 5   │                    │
│  (small)   │   Album 6 (large)  │  ← group 2: large-right
├────────────┤   2 cols × 2 rows  │
│  Album 7   │                    │
│  (small)   │                    │
├────────────┴───────┬────────────┤
│                    │  Album 8   │
│   Album 9 (large)  │  (small)   │  ← repeats...
│                    ├────────────┤
│                    │  Album 10  │
└────────────────────┴────────────┘
```

## Grid CSS
- Container: `grid grid-cols-1 md:grid-cols-3`
- Row height: `md:grid-auto-rows-[35vh]`
- Gap: `gap-1` (matches existing thin borders)
- Large card: `md:col-span-2 md:row-span-2`
- Small card: `md:col-span-1`
- Mobile: single column, all cards same size

## Files to modify

### `src/lib/gallery-layout.ts`
Simplify to return two section types:
- `hero`: first album (unchanged)
- `grid`: remaining albums as a flat array, each tagged with `large: boolean`

Pattern: albums index 0,3,6,... in the remaining list are `large`. They alternate grid placement (left/right) via CSS `md:order` or explicit grid-column placement.

### `src/app/work/page.tsx`
Replace the section-type switch with:
1. Render `GalleryHero` for the hero album
2. Render a grid container with `ProjectCard` for all remaining albums

### `src/components/sections/ProjectCard.tsx`
Add `md:col-span-2` to the large variant's TransitionLink className. The rest (row-span-2, aspect ratios, hover timeline) already works.

### Removed (no longer rendered on this page)
- `GalleryBentoSplit` — still exists for potential use elsewhere, just not used on `/work`
- `GalleryFullBleed` — same
- `GalleryTextureCards` — same

## Card sizing
- **Small card:** `aspect-video` (16:9) — already exists in ProjectCard
- **Large card:** `aspect-[3/4]` mobile, `md:aspect-auto md:flex-1` — already exists in ProjectCard
- **Row height:** `35vh` per grid row → large cards ≈ 70vh, small cards ≈ 35vh
- **Visible per viewport:** ~3-4 albums

## Hover interaction
Existing ProjectCard GSAP timeline (no changes needed):
- Image: `scale: 1.05` (400ms)
- Overlay: clip-path wipe from bottom (400ms)
- Title: "View Project" clip-reveal (300ms)

## Scroll entrance
Existing `staggerGrid` animation preset (no changes needed):
- Cards fade up with stagger delay based on index

## Mobile
- Single column (`grid-cols-1`)
- All cards render at `aspect-[3/4]` (large) or `aspect-video` (small)
- No col-span/row-span on mobile

## What stays unchanged
- `GalleryHero` component and its animations
- `ProjectCard` component internals (hover, scroll entrance)
- All animation presets in `animations.ts`
- Header, footer, all other pages
