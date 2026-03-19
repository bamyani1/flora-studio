# Phase H — Polish & Ship (2–3 days)

## Objective

Final performance audit, accessibility audit, SEO verification, cross-browser testing, and production deployment. Ship a polished, production-ready site.

## Done When

Lighthouse Performance >= 90, Accessibility >= 95, all checklist items pass, site is live on production domain.

## Dependencies from Prior Phases

- **Phases A–G:** All pages, components, and responsive adaptations complete.

## Core Web Vitals Targets

| Metric | Target | Measurement Tool |
|--------|--------|-----------------|
| LCP | < 2.5s | Vercel Speed Insights, Lighthouse |
| FID/INP | < 200ms | Chrome CrUX, Lighthouse |
| CLS | < 0.1 | Lighthouse, Web Vitals JS library |
| FCP | < 1.8s | Lighthouse |
| TTI | < 3.9s | Lighthouse |
| Lighthouse Performance | >= 90 (mobile) | Lighthouse CI in deploy pipeline |
| JS bundle (main) | < 150KB gzipped | Bundle Analyzer |

## Full Quality Checklist

### Performance
- [ ] 60fps on all animations — verified in Chrome DevTools Performance panel (no long frames > 16ms)
- [ ] All GSAP contexts properly cleaned up on component unmount (no orphaned ScrollTriggers or tweens)
- [ ] Lighthouse Performance >= 90 on mobile
- [ ] `LazyMotion` configured with `domAnimation` feature set (~5KB vs ~50KB)
- [ ] All images served via `next/image` with AVIF/WebP, appropriate `sizes`, and `blurDataURL`
- [ ] Hero image has `priority` attribute for LCP optimization
- [ ] Fonts preloaded for display and body; accent font deferred
- [ ] Turbopack file system caching active for fast dev startup
- [ ] No cumulative layout shift on initial load (CLS < 0.1)
- [ ] JS bundle < 150KB gzipped (excluding animation library baseline)
- [ ] `will-change` applied only during active animations, removed on complete

### Animation & Interaction
- [ ] `prefers-reduced-motion` fully respected — all animations have meaningful fallbacks per `reducedMotionFallbacks` table
- [ ] Horizontal scroll gallery converts to vertical on mobile
- [ ] Parallax disabled on mobile
- [ ] Magnetic effects disabled on touch devices
- [ ] Album card hover overlay fully reverses before morph transition fires
- [ ] Shared element morph (Flip) works for Work → Album navigation
- [ ] Fallback to overlay wipe when morph source unavailable (direct URL visit)
- [ ] Page transitions work correctly on browser back/forward
- [ ] Lenis smooth scroll disabled/simplified when `prefers-reduced-motion: reduce`

### Accessibility
- [ ] Lighthouse Accessibility >= 95
- [ ] Keyboard navigation functional for every interactive element
- [ ] Focus-visible styles use `var(--color-primary)` ring (not browser defaults)
- [ ] Skip-to-content link present and functional
- [ ] Mobile menu has proper focus trap and Escape key handling
- [ ] All images have descriptive `alt` text (from Sanity)
- [ ] Form inputs have associated `<label>`, `aria-describedby` for errors, `aria-invalid`
- [ ] Color contrast meets WCAG AA (checked: `#D4D4D8` on `#0C0F14` = ratio ~12.5:1)
- [ ] Contact form success/error states announced to screen readers

### SEO & Metadata
- [ ] Dynamic `metadata` export on every page
- [ ] OpenGraph images present for every page and album
- [ ] `sitemap.xml` generated via App Router metadata API
- [ ] `robots.txt` present and allows crawling (excludes `/studio`)
- [ ] JSON-LD structured data validates: `LocalBusiness` (home), `ImageGallery` (albums), `Person` (about)
- [ ] All external links have `rel="noopener noreferrer"` and open in new tab

### Cross-Browser & Devices
- [ ] Tested on real mobile devices (not just DevTools responsive mode)
- [ ] iPhone Safari — smooth scroll, transitions, galleries all functional
- [ ] Android Chrome — same
- [ ] iPad — desktop layout with touch interactions
- [ ] Desktop: Chrome, Firefox, Safari (macOS), Edge
- [ ] Tested on slow 3G network throttle — content still loads progressively

### Production Readiness
- [ ] No hydration mismatches in console
- [ ] Console is clean — zero warnings, zero errors
- [ ] Custom 404 page is on-brand with animation
- [ ] Contact form validates, submits, and shows success/error states with animation
- [ ] Favicon set: `.ico`, `apple-touch-icon.png`, `manifest.webmanifest`
- [ ] Sanity webhook configured for ISR revalidation on album publish
- [ ] Google Analytics 4 tracking active
- [ ] SSL configured on production domain
- [ ] Mobile experience is intentionally designed, not just reflowed

## Deployment Steps

1. Run `next build` — verify no errors
2. Run Lighthouse audit on local build
3. Deploy to Vercel
4. Configure custom domain + SSL
5. Set environment variables in Vercel dashboard
6. Configure Sanity webhook for ISR revalidation
7. Set up Google Analytics 4
8. Generate and verify sitemap.xml
9. Verify robots.txt
10. Test all pages on production URL
11. Cross-browser test on production
12. Final Lighthouse audit on production URL

## Files to Create in This Phase

- `src/app/not-found.tsx` — custom 404 with on-brand design + animation
- `src/app/error.tsx` — error boundary with branded fallback
- `src/app/sitemap.ts` — dynamic sitemap from Sanity albums
- `src/app/robots.ts` — robots.txt generation
- `src/app/opengraph-image.tsx` — dynamic OG image generation
- `src/lib/metadata.ts` — shared metadata helpers, JSON-LD generators
- Favicon files in `/public`
- `manifest.webmanifest` in `/public`
