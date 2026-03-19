# Project Status — Bamyan Storyworks

## Phase Progress

| Phase | Status | Started | Completed | Summary |
|-------|--------|---------|-----------|---------|
| A — Foundation | NOT STARTED | — | — | — |
| B — Layout Shell | NOT STARTED | — | — | — |
| C — Animation Primitives | NOT STARTED | — | — | — |
| D — Home Page | NOT STARTED | — | — | — |
| E — Work & Album Pages | NOT STARTED | — | — | — |
| F — About & Contact | NOT STARTED | — | — | — |
| G — Responsive & Mobile | NOT STARTED | — | — | — |
| H — Polish & Ship | NOT STARTED | — | — | — |

## Phase A — Foundation

- [ ] Next.js 16 project scaffolded with TypeScript
- [ ] `next.config.ts` configured (images, Sanity remote patterns)
- [ ] `tsconfig.json` configured (paths: `@/*` → `./src/*`)
- [ ] `package.json` with all dependencies installed
- [ ] `.env.example` created
- [ ] ESLint + Prettier configured
- [ ] `src/styles/globals.css` — full design token system + grain overlay + Lenis CSS
- [ ] `src/lib/fonts.ts` — Instrument Serif + Satoshi + JetBrains Mono
- [ ] Font files downloaded to `public/fonts/` (Satoshi, JetBrains Mono)
- [ ] `src/lib/gsap.ts` — centralized GSAP plugin registration
- [ ] `src/lib/easings.ts` — easing token map
- [ ] `src/lib/animations.ts` — named preset factories (all 18+ presets)
- [ ] `src/lib/motion-features.ts` — LazyMotion domAnimation bundle
- [ ] `src/lib/utils.ts` — cn(), lerp(), clamp()
- [ ] `src/providers/Providers.tsx` — GSAP + Lenis + LazyMotion
- [ ] `src/stores/ui-store.ts` — Zustand store (menuOpen, isTransitioning, etc.)
- [ ] `src/types/` — project.ts, animation.ts, ui.ts, index.ts
- [ ] `src/app/layout.tsx` — root layout with fonts + Providers
- [ ] `src/app/page.tsx` — placeholder home page
- [ ] Sanity project created + schemas (album, about)
- [ ] `src/sanity/client.ts` + `src/sanity/queries.ts` + schemas
- [ ] Dark page renders with smooth scroll, correct fonts, film grain visible

## Phase B — Layout Shell

- [ ] `src/components/layout/Header.tsx` — sticky, backdrop blur on scroll
- [ ] `src/components/layout/Footer.tsx` — large CTA, social links
- [ ] `src/components/layout/MobileMenu.tsx` — full-screen overlay, SplitText stagger
- [ ] `src/components/layout/TransitionOverlay.tsx` — wipe/morph overlay
- [ ] `src/components/layout/TransitionLink.tsx` — animated navigation
- [ ] `src/components/layout/ScrollIndicator.tsx` — hero scroll indicator
- [ ] `src/components/layout/BackToTop.tsx` — scroll-to-top button
- [ ] Zustand wired for menu + transition state
- [ ] Stub pages (Home, Work, About, Contact) with overlay wipe transitions
- [ ] Header blur activates on scroll
- [ ] Mobile nav works with full-screen overlay

## Phase C — Animation Primitives

- [ ] `src/components/animations/FadeIn.tsx`
- [ ] `src/components/animations/TextReveal.tsx`
- [ ] `src/components/animations/ImageReveal.tsx`
- [ ] `src/components/animations/ParallaxSection.tsx`
- [ ] `src/components/animations/MagneticButton.tsx`
- [ ] `src/components/animations/ClipReveal.tsx`
- [ ] `src/hooks/useReducedMotion.ts`
- [ ] `src/hooks/useMagnetic.ts`
- [ ] `src/hooks/useMediaQuery.ts`
- [ ] `src/hooks/useLenis.ts`
- [ ] All animations respect `prefers-reduced-motion`

## Phase D — Home Page

- [ ] `src/components/sections/Hero.tsx` — full heroSequence timeline
- [ ] `src/components/sections/ProjectGrid.tsx` — 2-col asymmetric layout
- [ ] `src/components/sections/ProjectCard.tsx` — hover effects + morph data-flip-id
- [ ] `src/components/sections/AboutTeaser.tsx` — portrait + philosophy text
- [ ] `src/components/sections/CategoriesStrip.tsx` — category labels
- [ ] `src/components/sections/ContactCTA.tsx` — large CTA text
- [ ] `src/app/page.tsx` — full home page assembly
- [ ] Sanity data fetching for featured albums + about teaser
- [ ] Home page scroll timeline matches spec

## Phase E — Work & Album Pages

- [ ] `src/app/work/page.tsx` — album index with full grid
- [ ] `src/app/work/[slug]/page.tsx` — album page template
- [ ] `src/components/sections/HorizontalScrollGallery.tsx` — pinned horizontal scroll
- [ ] `src/components/sections/NarrativeSection.tsx` — word-by-word scroll scrub
- [ ] GSAP Flip shared element morph (ProjectCard → album hero)
- [ ] `generateStaticParams` for SSG
- [ ] `generateMetadata` for dynamic SEO
- [ ] Next/Previous album navigation
- [ ] Horizontal gallery converts to vertical on mobile

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
