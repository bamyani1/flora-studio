# Performance Optimization Design Spec

## Problem

The site has several performance issues:
1. **Hydration bug:** `getFeaturedAlbum()` uses `Math.random()` causing server/client mismatch
2. **Unused fonts:** 9 of 13 font families are dead code (never imported)
3. **Bundle bloat:** CustomCursor ships JS even when disabled via env flag
4. **No blur placeholders:** Hero images flash from blank to loaded with no progressive enhancement
5. **Header animation:** Uses layout-triggering properties (height, padding, width) on every scroll frame
6. **Unoptimized asset:** grain.png is 59KB for a 256x256 repeating texture at 2% opacity

## Decisions

- **Math.random() fix:** Date-seeded deterministic selection (`dayOfYear % arr.length`). Rotates daily, stable per request.
- **Header transform refactor:** Partial — only convert logo `width` to `transform: scale()` and add `will-change` management. Full `scaleY` approach rejected due to child complexity (5 nested regions, existing clipPath animations, inverse-transform fragility). Header is `position: fixed` so height/padding changes already cause zero CLS.
- **LQIP approach:** Sanity CDN URL params (`?w=20&blur=50&q=30`), fetched server-side, converted to base64 data URLs. 2-second timeout with graceful fallback.
- **GSAP/Lenis code-splitting:** Left as-is. Cannot split without breaking global scroll sync, route-change ScrollTrigger refresh, and page transition animations.

## Architecture

### LQIP Pipeline

```
Sanity CDN URL → append ?w=20&blur=50&q=30 → server-side fetch → base64 data URL
                                                                      ↓
                                                        SiteMedia blurDataURL prop
                                                                      ↓
                                                     Next.js Image placeholder="blur"
```

- `src/lib/lqip.ts` — server-only utility with `generateLqipDataUrl()`
- `src/components/ui/SiteMedia.tsx` — accepts optional `blurDataURL`, passes to Image
- Server components (page.tsx files) generate LQIP at render time for hero images only

### Font Cleanup

Before: 13 font families (5 active, 8 hero variations never used, plus ibmPlexMono unused)
After: 5 font families (cormorantGaramond, inter, ebGaramond, notoSerif, spaceGrotesk)

### CustomCursor Bundle Elimination

Before: Static import in Providers.tsx, always in JS bundle (~5KB)
After: `next/dynamic` with `ssr: false`, loaded only when `NEXT_PUBLIC_ENABLE_CUSTOM_CURSOR=true`

## Scope Boundaries

- FolioGallery images: No LQIP (lazy-loaded below fold, minimal benefit)
- Providers.tsx structure: No changes beyond CustomCursor import
- globals.css tokens: No changes
- Placeholder fallback system: Untouched

## Verification

1. `npm run type-check` + `npm run test:unit` + `npm run build`
2. Dev server manual check: hero animation, header morph, gallery loading, mobile
3. Lighthouse mobile+desktop on `/`, `/work/[album]`, `/contact`
4. Targets: LCP < 2.5s, CLS < 0.1, INP < 200ms
