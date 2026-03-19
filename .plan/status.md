# Project Status — Bamyan Storyworks

## Phase Progress

| Phase | Status | Started | Completed | Summary |
|-------|--------|---------|-----------|---------|
| A — Foundation | DONE | 2026-03-19 | 2026-03-19 | All infrastructure scaffolded, builds clean |
| B — Layout Shell | DONE | 2026-03-19 | 2026-03-19 | Header, Footer, MobileMenu, transitions, stub pages, BackToTop |
| C — Animation Primitives | DONE | 2026-03-19 | 2026-03-19 | All animation wrappers + hooks, reduced motion support |
| D — Home Page | DONE | 2026-03-19 | 2026-03-19 | Hero, ProjectGrid/Card, AboutTeaser, CategoriesStrip, ContactCTA, page assembly |
| E — Work & Album Pages | DONE | 2026-03-19 | 2026-03-19 | Work index with filter tabs, album detail with hero/narrative/gallery/nav |
| F — About & Contact | NOT STARTED | — | — | — |
| G — Responsive & Mobile | NOT STARTED | — | — | — |
| H — Polish & Ship | NOT STARTED | — | — | — |

## Phase A — Foundation

- [x] Next.js 16 project scaffolded with TypeScript
- [x] `next.config.ts` configured (images, Sanity remote patterns)
- [x] `tsconfig.json` configured (paths: `@/*` → `./src/*`)
- [x] `package.json` with all dependencies installed
- [x] `.env.example` created
- [x] ESLint + Prettier configured
- [x] `src/styles/globals.css` — full design token system + grain overlay + Lenis CSS
- [x] `src/lib/fonts.ts` — Instrument Serif + Satoshi + JetBrains Mono
- [x] Font files downloaded to `public/fonts/` (Satoshi, JetBrains Mono)
- [x] `src/lib/gsap.ts` — centralized GSAP plugin registration
- [x] `src/lib/easings.ts` — easing token map
- [x] `src/lib/animations.ts` — named preset factories (all 18+ presets)
- [x] `src/lib/motion-features.ts` — LazyMotion domAnimation bundle
- [x] `src/lib/utils.ts` — cn(), lerp(), clamp()
- [x] `src/providers/Providers.tsx` — GSAP + Lenis + LazyMotion
- [x] `src/stores/ui-store.ts` — Zustand store (menuOpen, isTransitioning, etc.)
- [x] `src/types/` — project.ts, animation.ts, ui.ts, index.ts
- [x] `src/app/layout.tsx` — root layout with fonts + Providers
- [x] `src/app/page.tsx` — placeholder home page
- [x] Sanity schemas created (album, about) — project not yet created on sanity.io
- [x] `src/sanity/client.ts` + `src/sanity/queries.ts` + schemas
- [x] Dark page renders with smooth scroll, correct fonts, film grain visible

## Phase B — Layout Shell

- [x] `src/components/layout/Header.tsx` — sticky, backdrop blur on scroll
- [x] `src/components/layout/Footer.tsx` — large CTA, social links
- [x] `src/components/layout/MobileMenu.tsx` — full-screen overlay, SplitText stagger
- [x] `src/components/layout/TransitionOverlay.tsx` — wipe/morph overlay
- [x] `src/components/layout/TransitionLink.tsx` — animated navigation
- [x] `src/components/layout/ScrollIndicator.tsx` — hero scroll indicator
- [x] `src/components/layout/BackToTop.tsx` — scroll-to-top button
- [x] Zustand wired for menu + transition state
- [x] Stub pages (Home, Work, About, Contact) with overlay wipe transitions
- [x] Header blur activates on scroll
- [x] Mobile nav works with full-screen overlay
- [x] `src/lib/navigation.ts` — shared NAV_ITEMS + SOCIAL_LINKS
- [x] `src/app/template.tsx` — page enter animation on route change
- [x] `src/hooks/useFocusTrap.ts` — focus trapping for MobileMenu
- [x] `src/providers/Providers.tsx` — refactored to `lenis/react` for shared instance
- [x] `npm run build` passes clean

## Phase C — Animation Primitives

- [x] `src/components/animations/FadeIn.tsx` — scroll-triggered fade-up entrance
- [x] `src/components/animations/TextReveal.tsx` — SplitText line/word reveal
- [x] `src/components/animations/ImageReveal.tsx` — overlay + scale image reveal
- [x] `src/components/animations/ParallaxSection.tsx` — scroll-linked Y parallax
- [x] `src/components/animations/MagneticButton.tsx` — cursor-following magnetic effect
- [x] `src/components/animations/ClipReveal.tsx` — clip-path wipe reveal
- [x] `src/hooks/useReducedMotion.ts` — SSR-safe prefers-reduced-motion detection
- [x] `src/hooks/useMagnetic.ts` — cursor proximity GSAP translation
- [x] `src/hooks/useMediaQuery.ts` — SSR-safe media query matching
- [x] ~~`src/hooks/useLenis.ts`~~ — SKIPPED: `useLenis()` from `lenis/react` already in use
- [x] All animations respect `prefers-reduced-motion`
- [x] `npm run build` passes clean

## Phase D — Home Page

- [x] `src/components/sections/Hero.tsx` — full heroSequence timeline
- [x] `src/components/sections/ProjectGrid.tsx` — 2-col CSS grid layout
- [x] `src/components/sections/ProjectCard.tsx` — hover effects + morph data-flip-id
- [x] `src/components/sections/AboutTeaser.tsx` — portrait + philosophy text
- [x] `src/components/sections/CategoriesStrip.tsx` — category labels
- [x] `src/components/sections/ContactCTA.tsx` — large CTA text with MagneticButton
- [x] `src/app/page.tsx` — full home page assembly
- [x] Sanity data fetching with try/catch + placeholder fallback
- [x] `src/lib/placeholder-data.ts` — hardcoded fallback data
- [x] `src/sanity/client.ts` — lazy initialization to prevent build-time crash
- [x] `npm run build` passes clean

## Phase E — Work & Album Pages

- [x] `src/app/work/page.tsx` — album index with full grid
- [x] `src/app/work/[slug]/page.tsx` — album page template
- [x] `src/components/sections/HorizontalScrollGallery.tsx` — pinned horizontal scroll
- [x] NarrativeSection skipped — TextReveal variant="words" scrub used directly
- [x] GSAP Flip skipped — all transitions use wipe only
- [x] `generateStaticParams` for SSG
- [x] `generateMetadata` for dynamic SEO
- [x] Next/Previous album navigation with wrap-around
- [x] Horizontal gallery converts to vertical on mobile
- [x] `src/components/sections/AlbumHero.tsx` — full-screen hero with ImageReveal
- [x] `src/components/sections/AlbumNav.tsx` — prev/next navigation
- [x] `src/components/sections/FilterableGrid.tsx` — category filter with CSS fade
- [x] TextReveal delay prop for choreographed entrances
- [x] ProjectCard switched from morph to wipe transition
- [x] `npm run build` passes clean

## Phase F — About & Contact

- [ ] `src/app/about/page.tsx` — bio, approach, services, socials
- [ ] `src/app/contact/page.tsx` — form + info
- [ ] `src/app/contact/action.ts` — Server Action (Zod + Resend)
- [ ] `src/components/ui/Button.tsx`
- [ ] `src/components/ui/Input.tsx`
- [ ] `src/components/ui/Textarea.tsx`
- [ ] `src/components/ui/Select.tsx`
- [ ] `src/components/ui/ContactForm.tsx`
- [ ] Form validates, submits, delivers email, shows success state

## Phase G — Responsive & Mobile

- [ ] All components adapted for mobile breakpoints
- [ ] Parallax disabled on mobile
- [ ] Horizontal galleries → vertical on mobile
- [ ] Magnetic effects disabled on touch
- [ ] Mobile menu fully functional
- [ ] Tested: iPhone Safari, Android Chrome, iPad

## Phase H — Polish & Ship

- [ ] Lighthouse Performance >= 90
- [ ] Lighthouse Accessibility >= 95
- [ ] All SEO metadata + JSON-LD validated
- [ ] sitemap.xml + robots.txt
- [ ] Custom 404 page
- [ ] Favicons + web manifest
- [ ] Cross-browser tested (Chrome, Firefox, Safari, Edge)
- [ ] Deployed to Vercel
- [ ] Sanity webhook for ISR revalidation
- [ ] Google Analytics 4 configured
- [ ] Console clean (zero warnings/errors)

## Key Technical Facts

_Populated as they become known during implementation._

- **Sanity Project ID:** —
- **Production URL:** —
- **Vercel Project:** —
- **Domain:** —
