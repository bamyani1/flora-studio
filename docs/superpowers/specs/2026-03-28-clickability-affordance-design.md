# Clickability Affordance Improvements

## Context
Interactive elements across the site lack clear visual feedback on hover, making it unclear to users what's clickable. A full audit identified 8 elements needing fixes across 8 files.

## Changes

### 1. GalleryHero — Album Title Link (CRITICAL)
**File:** `src/components/sections/gallery/GalleryHero.tsx:146-152`
**Problem:** Title wrapped in `<TransitionLink>` has zero hover state.
**Fix:** Add `group` to the TransitionLink, add `transition-colors duration-500 group-hover:text-primary` to the h2 text.

### 2. Header & Mobile Logo — Home Link (CRITICAL)
**File:** `src/components/layout/Header.tsx:144-150`
**Problem:** Logo link has no hover feedback.
**Fix:** Add `transition-opacity duration-500 opacity-90 hover:opacity-100` to the TransitionLink.

### 3. Landing Studio Card — Featured Album (HIGH)
**File:** `src/components/landing/LandingStudioCards.tsx:55-71`
**Problem:** Hover changes bg from `white/[0.02]` to `white/[0.04]` — invisible.
**Fix:** Change to `hover:bg-white/[0.06] hover:border-white/20` and add a `→` arrow span that appears on hover (`opacity-0 group-hover:opacity-100 transition-opacity`).

### 4. AlbumNav — Previous / Next Links (HIGH)
**File:** `src/components/sections/AlbumNav.tsx:19-49`
**Problem:** Only title color changes on hover. No directional indicator.
**Fix:** Add `←` before previous title and `→` after next title, with `translate-x` animation on hover. Add underline on title via `group-hover:underline underline-offset-4 decoration-primary/40`.

### 5. Gallery Full Bleed — Image Card (HIGH)
**File:** `src/components/sections/gallery/GalleryFullBleed.tsx:105-143`
**Problem:** Hover zoom takes 3s — too slow for feedback. No text CTA.
**Fix:** Add `cursor-pointer` to the TransitionLink. Add a "View Series →" label below the category text that fades in on hover (`opacity-0 group-hover/bleed:opacity-100 transition-opacity duration-500`).

### 6. Button — outline-subtle variant (HIGH)
**File:** `src/components/ui/Button.tsx:12`
**Problem:** Border shifts from `white/35` to `white/50` — barely visible.
**Fix:** Change to `hover:border-white/70 hover:bg-white/5`.

### 7. Landing Footer Links (LOW)
**File:** `src/components/landing/LandingFooter.tsx:14-35`
**Problem:** Links at 50% opacity with color-only hover. No underline like main Footer.
**Fix:** Wrap each link in a `group` span and add an animated underline (`scale-x-0 group-hover:scale-x-100 transition-transform`) matching the main Footer pattern.

### 8. Gallery Texture Cards (LOW)
**File:** `src/components/sections/gallery/GalleryTextureCards.tsx:118-125`
**Problem:** Title color change + lift is decent but could be stronger.
**Fix:** Add a small `→` arrow after the title that translates right on hover (`translate-x-0 group-hover/texture:translate-x-2 transition-transform duration-300`).

## Design Principles
- Keep all additions subtle and consistent with the editorial luxury aesthetic
- Use the gold/primary color as the primary hover signal
- Prefer additive changes (arrows, underlines) over dramatic transforms
- Ensure all hover transitions are fast enough for instant feedback (300-700ms)
- No changes to existing GSAP animations or their durations

## Verification
- `npm run build` passes
- Open each page in browser and verify every interactive element has clear hover feedback
- Check that new hover effects don't conflict with existing GSAP animations
