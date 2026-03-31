# Pre-Publish Prompts — Bahar Studio

Rewritten March 30, 2026 after full codebase audit.

**Completed and removed:**
- ~~CMS & Content Migration~~ — Sanity project connected (`tm8qd2gj`), data migrated, fallback system intact.
- ~~Contact Form~~ — iCloud SMTP wired, honeypot field, auto-reply, production error handling all in place.
- ~~Legal Pages~~ — Real Privacy Policy and Terms of Service with substantive content. Cookie consent manager scaffolded.

Each prompt below is self-contained. Hand any single prompt to an agent — it has everything it needs. The agent should make its own implementation decisions; these are senior-level directives, not step-by-step instructions.

**Execution order:** 1 → 2 → 3 → 4 → 5 → 6 → 7

---

## Prompt 1 — About Page, Placeholder Media & Social Links Cleanup

```
PROJECT CONTEXT:
This is a Next.js 16 photography portfolio for Bahar Studio (studiobahar.com). It uses Sanity CMS with a placeholder fallback system, GSAP animations, Lenis smooth scroll, and Tailwind CSS 4. The Sanity CMS is connected and serving real content. The site has 11 albums with real imagery.

---

The About page has three real team members but only one (Mostafa Bamyani) has a real portrait photo. The other two (Murtaza Anwari and Enayatullah Anwari) use `placeholder://` URLs that render as gradient rectangles — not acceptable for a live site. All three are real team members.

Additionally, the site's social links and `sameAs` structured data reference Behance and LinkedIn profiles that don't exist. The studio only uses Instagram and email.

Your job:

1. SOURCE REAL TEAM PORTRAITS. Ask the user to provide real portrait photos for Murtaza Anwari and Enayatullah Anwari. If the user provides photos, process them through the existing image pipeline (see `scripts/process-images.mjs`) and wire them in at proper dimensions in both the placeholder data (`src/lib/placeholder-site-content.ts`) and in the Sanity CMS. If the user cannot provide photos before launch, redesign the team section to gracefully handle members without portraits — a stylized initial/monogram treatment, or a consistent placeholder design that looks intentional rather than broken. The current gradient rectangle fallback looks like a bug.

2. CLEAN UP SOCIAL LINKS. The site should only show Instagram and email as social/contact links. Remove Behance and LinkedIn from:
   - Placeholder site settings in `src/lib/placeholder-site-content.ts` (the `socialLinks` array and `sameAs` array)
   - The Sanity CMS `siteSettings` document (if Behance/LinkedIn entries exist there)
   - The social icon enum in `src/lib/cms-validation.ts` (line 41: `z.enum(["instagram", "behance", "linkedin"])`) — keep the enum extensible but remove unused values from placeholder data
   - The `SocialIcon` type in `src/types/content.ts`
   - The icon rendering switch in `src/app/(site)/contact/page.tsx` — keep the `behance` and `linkedin` icon cases so the schema doesn't break if someone adds them back in the CMS later, but they should not appear in the default/placeholder data
   - The Sanity schema options in `src/sanity/schemas/objects.ts` — keep them as allowed options in the CMS but remove from placeholder data

3. AUDIT ALL PLACEHOLDER MEDIA. Search the entire codebase for any remaining `placeholder://` URLs that would render in production outside the fallback system. The `SiteMedia` component (`src/components/ui/SiteMedia.tsx`) handles `placeholder://` URLs gracefully with a gradient fallback, but this is meant as a development aid, not a production feature. Every user-visible image should have a real source.

4. VERIFY THE ABOUT PAGE WORKS. Test the page with reduced motion enabled — the about page has 18+ animation variants that must degrade gracefully. Elements should not be permanently hidden when animations are disabled. Test on mobile breakpoints — team card grids, parallax images, and text reveals all need to work on small screens.

5. VERIFY STRUCTURED DATA. Check the Person JSON-LD on the about page (`src/app/(site)/about/page.tsx`) — make sure the `sameAs` array only contains real, active profile URLs (Instagram only, no Behance/LinkedIn). The data comes from the `getSiteSettings()` call.

SAFETY RULES:
- NEVER remove the placeholder fallback system. The data-fetching functions in `src/lib/albums.ts` and `src/lib/site-content.ts` have a fallback chain that serves placeholder data when the CMS is unavailable. This is a safety net. Do not delete the fallback logic.
- Do NOT change CSS custom properties, color tokens, typography, or layout primitives in `src/styles/globals.css` without showing the user and getting explicit approval.
- The GSAP animation library and Lenis smooth scroll are loaded globally through `src/providers/Providers.tsx`. Do not restructure this.
- After completing your work, run the production build (`next build`). If it fails, fix it before asking the user to verify.

VERIFICATION: Start the dev server and ask the user to review the About page carefully — both on desktop and mobile. If team members were changed or the section was redesigned, the user must approve the new layout. Verify that the footer and contact page only show Instagram (no Behance/LinkedIn). Fix anything they flag before considering this prompt complete.
```

---

## Prompt 2 — Deployment Infrastructure and CI/CD

```
PROJECT CONTEXT:
This is a Next.js 16 photography portfolio for Bahar Studio (studiobahar.com). It uses Sanity CMS (project ID: tm8qd2gj) with a placeholder fallback system, GSAP animations with inline styles, Lenis smooth scroll, Tailwind CSS 4, and Google Fonts loaded via next/font. The Sanity CDN is at cdn.sanity.io. The site is NOT yet deployed — no vercel.json, no CI/CD pipeline, no security headers. The domain studiobahar.com is not yet pointed at Vercel.

---

This project has zero deployment configuration. It needs hosting config, a CI pipeline, and a pre-deploy verification system before it can go live.

Your job:

1. CREATE VERCEL CONFIGURATION (`vercel.json`):
   - Security headers following OWASP recommendations: Content-Security-Policy, Strict-Transport-Security, X-Content-Type-Options, X-Frame-Options, Referrer-Policy, Permissions-Policy.
   - The CSP MUST account for: `cdn.sanity.io` (images), inline styles used by GSAP animations (`style-src 'unsafe-inline'` is likely required — test this), Google Fonts (fonts.googleapis.com, fonts.gstatic.com), and the Next.js inline scripts.
   - Cache-Control headers for static assets (images, fonts — long-lived immutable caching).
   - Canonical domain handling (www.studiobahar.com → studiobahar.com redirect).
   - Region configuration: `iad1` (US East) is appropriate for a Dayton, Ohio business.

2. CREATE A CI PIPELINE (GitHub Actions) that runs on push and PRs:
   - Linting (`npm run lint`), type checking (`npm run type-check`), unit tests (`npm run test:unit`), and production build (`npm run build`).
   - The project already has Playwright e2e tests (`npm run test:e2e` and `npm run test:e2e:consent`). These require a running server. Either set them up properly in CI with a build + serve step, or gate them as a separate workflow that runs on main only. Don't skip them.
   - Proper caching for node_modules and .next build artifacts.
   - Use Node 22.x (the project uses Next.js 16 which requires Node 18+).

3. CREATE A PRE-DEPLOY VERIFICATION SCRIPT:
   - A script (e.g., `scripts/verify-deploy.mjs`) that checks for common production mistakes:
     - Required environment variables present (NEXT_PUBLIC_SANITY_PROJECT_ID, SANITY_READ_TOKEN, ICLOUD_SMTP_USER, ICLOUD_SMTP_PASS, CONTACT_EMAIL, NEXT_PUBLIC_SITE_URL)
     - No `placeholder://` URLs in the built output
     - No oversized assets in public/ (flag anything over 2MB)
     - Production build succeeds
   - Add this as an npm script (`npm run verify:deploy`).

4. SET UP VERCEL ANALYTICS AND SPEED INSIGHTS:
   - Install `@vercel/analytics` and `@vercel/speed-insights`.
   - Wire them into the app layout. These should respect the cookie consent system — check `src/lib/cookie-consent.ts` and `src/components/legal/CookieConsentManager.tsx`. If the user has not consented to analytics, these should not load.
   - Note: `NEXT_PUBLIC_ENABLE_COOKIE_CONSENT` is currently `false` in `.env.example`. When Vercel Analytics is added, this should default to `true` in production. Document this in the `.env.example`.

5. CLEAN UP THE REPOSITORY:
   - Check `.gitignore` — it already excludes many dev artifacts. Verify no untracked development files (marketing HTML, full-res source photos, evaluation docs) exist in the repo. The `.gitignore` already covers `/pictures/`, `/audit/`, `/contact/`, `/process/`, `/newest-landing/`, `/new-landing/`, `/new-contact-form/`, etc. Make sure nothing slipped through.
   - Verify no secrets or API keys are committed anywhere (check git history if possible — the `.env.local` should NOT be committed).

SAFETY RULES:
- NEVER remove the placeholder fallback system in the data-fetching layer.
- Do NOT change CSS custom properties, color tokens, typography, or layout primitives in `src/styles/globals.css` without showing the user and getting explicit approval.
- The GSAP animation library and Lenis smooth scroll are loaded globally through `src/providers/Providers.tsx`. Do not restructure this.
- After completing your work, run the production build (`next build`). If it fails, fix it before asking the user to verify.

VERIFICATION: Run the production build to confirm the new config doesn't break anything. Start the dev server and ask the user to verify the site still looks and behaves correctly — security headers and CSP policies can silently break animations, fonts, or images if configured too strictly. Test thoroughly. Fix any issues before considering this prompt complete.
```

---

## Prompt 3 — Performance Optimization

```
PROJECT CONTEXT:
This is a Next.js 16 photography portfolio for Bahar Studio (studiobahar.com). It uses Sanity CMS with a placeholder fallback system, GSAP animations, Lenis smooth scroll, and 5 custom font families (Cormorant Garamond, Inter, Noto Serif, Space Grotesk, EB Garamond). The site has 110+ high-resolution images across 11 albums. Image optimization is configured (AVIF/WebP, processed at 3200px, Sanity CDN remote patterns). The Providers component at `src/providers/Providers.tsx` loads GSAP and Lenis globally.

---

This is a photography portfolio with 110+ images. Performance is existential — if the site is slow, the work doesn't get seen.

Your job:

1. FIX THE KNOWN HYDRATION BUG. In `src/lib/albums.ts`, the `getFeaturedAlbum()` function (around line 105) uses `Math.random()` at render time to pick a featured album. This produces different results on server vs client, causing a hydration mismatch. Fix this — use a deterministic selection strategy (e.g., based on date, first item, or a seeded random).

2. AUDIT LCP (Largest Contentful Paint):
   - Make sure hero images on the landing page and album pages are prioritized with `priority` prop (not lazy-loaded). Check `src/components/sections/AlbumHero.tsx` and the landing hero component.
   - Check if the CSS grain overlay effect (`.grain-overlay` class in globals.css, applied to `<body>`) is paint-blocking the LCP element.
   - Verify font loading doesn't delay rendering. The site loads 5 custom font families in `src/lib/fonts.ts` — check font-display strategy.

3. AUDIT CLS (Cumulative Layout Shift):
   - Every `<Image>` and `<SiteMedia>` must have explicit dimensions or a properly sized fill container.
   - The header has scroll-based morph animations (height, padding changes) — verify these don't cause layout shift.
   - Font loading across 5 families is a CLS risk — audit font-display strategy and consider reducing to essential families if any are barely used.

4. OPTIMIZE THE JAVASCRIPT BUNDLE:
   - The animation library and smooth-scroll library load on every page via Providers. Evaluate whether they can be code-split or dynamically imported — but do NOT break the global animation contract. If code-splitting risks breaking animations across pages, leave them as-is and document why.
   - The custom cursor component (`src/components/ui/CustomCursor.tsx`) runs globally including on mobile where it's invisible. It's already behind a `NEXT_PUBLIC_ENABLE_CUSTOM_CURSOR` env flag. Verify it's disabled by default and doesn't ship unnecessary JS on touch devices.
   - Verify lucide-react icon imports are tree-shaken (individual named imports, not barrel imports).

5. IMAGE LOADING UX:
   - Add blur placeholders for hero and cover images. Next.js supports `placeholder="blur"` with `blurDataURL`. For Sanity CDN images, generate low-quality image placeholders (LQIP).
   - For gallery pages with 10-16 images, verify progressive loading — images below the fold should lazy-load. The `FolioGallery` component handles gallery rendering.

6. LIGHTHOUSE AUDIT:
   - Run Lighthouse on mobile and desktop for: homepage, a gallery page (e.g., /work/milestone), and the contact page.
   - Report scores. Performance budget targets: LCP < 2.5s, CLS < 0.1, INP < 200ms.

SAFETY RULES:
- NEVER remove the placeholder fallback system in the data-fetching layer.
- Do NOT change CSS custom properties, color tokens, typography, or layout primitives in `src/styles/globals.css` without showing the user and getting explicit approval.
- The GSAP animation library and Lenis smooth scroll are loaded globally through `src/providers/Providers.tsx`. If you're optimizing performance or code-splitting, do not break this contract without testing every page. When in doubt, leave the animation architecture alone.
- After completing your work, run the production build (`next build`). If it fails, fix it before asking the user to verify.

VERIFICATION: Start the dev server and ask the user to check: Does the homepage hero still animate correctly? Do gallery pages load images smoothly without layout jumps? Does the header scroll morph still work? Are there any visual regressions on mobile? Fix anything they flag before considering this prompt complete.
```

---

## Prompt 4 — Error Handling, Loading States & Production Resilience

```
PROJECT CONTEXT:
This is a Next.js 16 photography portfolio for Bahar Studio (studiobahar.com). It uses Sanity CMS with a placeholder fallback system (`src/lib/content-runtime.server.ts` handles fallback logic with `resolveContentAvailabilityFailure`). The error boundary is at `src/app/error.tsx` and the 404 page is at `src/app/not-found.tsx`. There are NO loading.tsx files anywhere in the app. The contact form server action is at `src/app/(site)/contact/action.ts`.

---

The app has basic error handling (error boundary, 404 page) but it's not production-grade. There are no loading states, and the error/404 pages are minimal.

Your job:

1. ADD LOADING STATES. Create `loading.tsx` files for routes that fetch data:
   - `src/app/(site)/loading.tsx` — for the home page
   - `src/app/(site)/work/loading.tsx` — for the work index page
   - `src/app/(site)/work/[slug]/loading.tsx` — for individual album pages
   - `src/app/(site)/about/loading.tsx` — for the about page
   - `src/app/(site)/process/loading.tsx` — for the process page
   These must be skeleton layouts that match the actual page structure — not generic spinners. They should use the site's design language (dark background, grain overlay aesthetic, subtle pulse animations in the primary color).

2. IMPROVE THE ERROR BOUNDARY (`src/app/error.tsx`):
   - Currently it only has "Try again". Add a "Go home" link alongside the reset button.
   - Match the site's cinematic design language (the 404 page style is a good reference).
   - Consider showing different messages for different error types.

3. IMPROVE THE 404 PAGE (`src/app/not-found.tsx`):
   - Currently just says "Page not found" with a "Back to home" link.
   - When someone hits a bad URL, show relevant suggestions — link to the work page, link to contact. 
   - Match the cinematic design language of the rest of the site.

4. IMPROVE ERROR LOGGING in the data layer:
   - The content runtime (`src/lib/content-runtime.server.ts`) already has `warnContentFallback` which logs with structured JSON context. This is good. Verify that in production mode (`getContentRuntimeMode() === "production"`), failures throw `ContentUnavailableError` rather than silently falling back — the current implementation does this correctly, but double-check all data functions in `src/lib/albums.ts` and `src/lib/site-content.ts` follow this pattern consistently.
   - For the contact form server action (`src/app/(site)/contact/action.ts`), verify the catch block at line 138 logs enough context to debug from Vercel logs. Currently it logs `[Contact] Failed to send email:` with the error object — this is adequate but ensure the error includes enough detail (was it a connection timeout? auth failure? DNS resolution?). Add the SMTP host to the log if it's not a credential.

5. HANDLE BROKEN IMAGES. The `SiteMedia` component (`src/components/ui/SiteMedia.tsx`) already handles `placeholder://` URLs with a gradient fallback. But if a real image URL fails to load (CDN down, bad URL), it will show the browser's broken image icon. Add an `onError` handler to the `<Image>` component that swaps to the placeholder gradient fallback when a real image fails to load.

SAFETY RULES:
- NEVER remove the placeholder fallback system. You're improving the experience around it, but the fallback itself must stay intact.
- Do NOT change CSS custom properties, color tokens, typography, or layout primitives in `src/styles/globals.css` without showing the user and getting explicit approval.
- The GSAP animation library and Lenis smooth scroll are loaded globally through `src/providers/Providers.tsx`. Do not restructure this.
- After completing your work, run the production build (`next build`). If it fails, fix it before asking the user to verify.

VERIFICATION: Start the dev server and ask the user to check: Do loading skeletons appear and look natural when navigating between pages? Does the 404 page match the site's design? Does the error page work (you can test by temporarily throwing in a page component)? Fix anything they flag before considering this prompt complete.
```

---

## Prompt 5 — SEO, Structured Data & Social Sharing

```
PROJECT CONTEXT:
This is a Next.js 16 photography portfolio for Bahar Studio (studiobahar.com). It uses Sanity CMS. SEO foundations exist: base metadata in `src/lib/metadata.ts`, a single generic OG image in `src/app/opengraph-image.tsx` (text-only, no photography), robots.ts, dynamic sitemap, and JSON-LD schemas (LocalBusiness on home, ImageGallery on albums, Person on about). The site only uses Instagram for social — no Behance or LinkedIn.

---

The SEO foundation exists but needs hardening for real-world indexing and social sharing. The OG image strategy is the biggest gap — for a photography site, sharing a text-only dark rectangle on social media is a missed opportunity.

Your job:

1. AUDIT EVERY ROUTE'S METADATA. Each page should have a unique, descriptive title and meta description. Check:
   - Home page: `src/app/layout.tsx` (base metadata in `src/lib/metadata.ts`)
   - Work page: `src/app/(site)/work/page.tsx`
   - Album pages: `src/app/(site)/work/[slug]/page.tsx` — these already pull title/description from album data, which is good
   - About: `src/app/(site)/about/page.tsx` — needs metadata export
   - Contact: `src/app/(site)/contact/page.tsx`
   - Process: `src/app/(site)/process/page.tsx`
   - Privacy: `src/app/(site)/privacy/page.tsx`
   - Terms: `src/app/(site)/terms/page.tsx`
   Any page with generic or missing descriptions needs to be fixed.

2. IMPROVE THE OG IMAGE STRATEGY:
   - The current OG image (`src/app/opengraph-image.tsx`) is a plain dark rectangle with "BAHAR STUDIO" text. This is fine as a default fallback.
   - Create route-specific OG images for album pages that incorporate the album's cover image or hero image. Use Next.js `ImageResponse` with the album's photography. Social sharing is how a photography site gets discovered — OG images should showcase the work.
   - Consider OG images for the work page and about page as well.
   - All OG images must be 1200×630.

3. VALIDATE STRUCTURED DATA:
   - LocalBusiness schema on the homepage (`src/app/(site)/page.tsx`) — verify business details are accurate. The `sameAs` array should only contain real, active profile URLs (Instagram only).
   - ImageGallery schema on album pages (`src/app/(site)/work/[slug]/page.tsx`) — already exists, verify valid markup.
   - Person schema on the about page — already exists, verify accuracy.
   - ADD BreadcrumbList schema to interior pages (work, album pages, about, contact, process). This doesn't exist yet — add it to `src/lib/metadata.ts` and wire it into the relevant pages.

4. TECHNICAL SEO:
   - Ensure canonical URLs are set on every page via the base metadata.
   - Verify the sitemap (`src/app/sitemap.ts`) includes lastmod dates and all routes (including privacy, terms).
   - The Sanity Studio route (`/studio`) is disallowed in robots.txt but should also have a `noindex` meta tag. Check if the studio route group (`src/app/(studio)/`) has its own layout with appropriate metadata.
   - Add preconnect/dns-prefetch hints for external origins: `cdn.sanity.io`, `fonts.googleapis.com`, `fonts.gstatic.com`. Add these to the root layout `<head>`.

5. TEST SOCIAL SHARING:
   - After implementing OG images, verify they render correctly at 1200×630 by checking the generated images in the browser (navigate to `/opengraph-image` and album OG image routes).
   - Document how the user can test with Facebook Sharing Debugger and Twitter Card Validator once the site is deployed.

SAFETY RULES:
- NEVER remove the placeholder fallback system in the data-fetching layer.
- Do NOT change CSS custom properties, color tokens, typography, or layout primitives in `src/styles/globals.css` without showing the user and getting explicit approval.
- The GSAP animation library and Lenis smooth scroll are loaded globally through `src/providers/Providers.tsx`. Do not restructure this.
- After completing your work, run the production build (`next build`). If it fails, fix it before asking the user to verify.

VERIFICATION: Start the dev server and navigate to the OG image routes to verify they render correctly. Ask the user to review the generated images. Fix anything they flag before considering this prompt complete.
```

---

## Prompt 6 — Accessibility Audit (WCAG 2.2 AA)

```
PROJECT CONTEXT:
This is a Next.js 16 photography portfolio for Bahar Studio (studiobahar.com). Accessibility foundations exist: skip-to-content link (`src/app/(site)/layout.tsx`), `useReducedMotion` hook (`src/hooks/useReducedMotion.ts`), reduced-motion support in animations (`src/lib/animations.ts`) and the Providers component, `aria-live` on the contact form (`src/components/ui/CinematicContactForm.tsx`), semantic HTML throughout. Known gaps: no `aria-current="page"` on nav items, no breadcrumbs, and color contrast has not been audited. The primary accent color is copper-ish (check `--color-primary` in `globals.css`) on dark backgrounds.

---

The site has good accessibility foundations but hasn't been formally audited. Color contrast and animation behavior are the biggest unknowns.

Your job:

1. COLOR CONTRAST AUDIT. The site uses a warm dark palette with copper/amber accent tones. Test every text/background combination against WCAG AA contrast ratio (4.5:1 for normal text, 3:1 for large text). Key combinations to check:
   - Primary accent color on dark backgrounds (buttons, labels, eyebrow text)
   - "Muted" text colors (the `text-muted` utility) on surface backgrounds
   - Text on the grain overlay
   If any fail, propose adjusted colors that preserve the brand feel — but do NOT apply them without user approval. Show proposed changes side-by-side.

2. KEYBOARD NAVIGATION AUDIT. Test every interactive element:
   - Links, buttons, form controls (the contact form has select dropdowns, text inputs, a textarea)
   - The mobile menu — check for proper focus trap, Escape to close, and screen reader announcements
   - Route transitions — verify tab order is logical after navigation
   - Focus indicators — verify they're visible. The skip-to-content link uses `focus:not-sr-only` which is correct.

3. IMAGE ALT TEXT AUDIT. Check how alt text is generated for gallery images. The Sanity schema supports an `alt` field on images (`src/lib/cms-validation.ts`, `sanityImageSchema`). Verify:
   - All hero/cover images have meaningful, descriptive alt text
   - Gallery images in albums have appropriate alt text (not generic like "Album name 1")
   - Decorative images (grain overlays, background textures) use empty alt or aria-hidden

4. ANIMATION ACCESSIBILITY. Verify that ALL animation components respect `prefers-reduced-motion`:
   - Check every component in `src/components/animations/`
   - Check the about page's 18 animation variants
   - Elements that start at `opacity: 0` for entrance animations must still be accessible to screen readers before the animation fires
   - Check that the grain overlay and any spinning/pulsing elements don't trigger vestibular issues
   - The Providers component already disables Lenis and uses native scroll when `prefers-reduced-motion` is active — verify this works

5. ARIA AND SEMANTICS:
   - Add `aria-current="page"` to the active navigation item. Check the nav component in `src/components/layout/`.
   - Verify `aria-live` region on the contact form success/error messages is working correctly.
   - Verify landmark roles: `<nav>`, `<main>`, `<footer>` are properly used.
   - Check that the header/footer components have proper semantic structure.

6. RUN AUTOMATED AUDIT:
   - Run Lighthouse accessibility audit on every page template (home, work, album, about, contact, process, privacy, terms).
   - Document results and fix any critical or serious issues.

SAFETY RULES:
- NEVER remove the placeholder fallback system in the data-fetching layer.
- Do NOT change CSS custom properties, color tokens, typography, or layout primitives in `src/styles/globals.css` without showing the user and getting explicit approval. This is especially critical for this prompt — color adjustments for contrast compliance MUST be approved by the user before applying. Show proposed changes side-by-side.
- The GSAP animation library and Lenis smooth scroll are loaded globally through `src/providers/Providers.tsx`. Do not restructure this.
- After completing your work, run the production build (`next build`). If it fails, fix it before asking the user to verify.

VERIFICATION: If you changed any colors, focus styles, or ARIA attributes, ask the user to verify the site still looks and feels right. Accessibility improvements should enhance the experience, not change the brand. Show the user any color adjustments side-by-side and get approval. Fix anything they flag before considering this prompt complete.
```

---

## Prompt 7 — Final Verification, Cleanup & Launch Gate

```
PROJECT CONTEXT:
This is a Next.js 16 photography portfolio for Bahar Studio (studiobahar.com). All previous prompts should be complete: CMS connected, contact form working, legal pages real, about page complete, deployment infrastructure configured, performance optimized, SEO hardened, error handling improved, accessibility audited. The site uses Sanity CMS with a placeholder fallback system that should be preserved in production as a resilience layer. The domain studiobahar.com is not yet pointed at Vercel.

---

This is the final gate before going live. Verify everything works together.

Your job:

1. RUN THE FULL VERIFICATION PIPELINE:
   - `npm run lint` — no errors
   - `npm run type-check` — no errors
   - `npm run test:unit` — all pass
   - `npm run test:e2e:all` — all pass
   - `npm run build` — clean production build with no warnings
   Fix anything that fails. Do not skip any step.

2. PRODUCTION SMOKE TEST — Write a script (`scripts/smoke-test.mjs`) that starts the production server and verifies:
   - Every route returns 200: `/`, `/work`, `/about`, `/contact`, `/process`, `/privacy`, `/terms`
   - At least one album route returns 200 (get slugs from the sitemap)
   - No `placeholder://` URLs appear in the rendered HTML of any page
   - OG meta tags are present on every page
   - `/sitemap.xml` is valid and contains expected routes
   - `/robots.txt` serves correctly and disallows `/studio/`
   - The `/studio` route is accessible (200 status — it's the CMS admin)

3. VERIFY ENVIRONMENT VARIABLE COMPLETENESS:
   - Every required variable should be documented in `.env.example` with clear descriptions
   - The app should fail loudly at startup if critical production vars are missing — check `src/lib/public-env.ts` and `src/lib/contact-config.server.ts` for validation
   - Required vars: NEXT_PUBLIC_SANITY_PROJECT_ID, NEXT_PUBLIC_SANITY_DATASET, SANITY_READ_TOKEN, ICLOUD_SMTP_USER, ICLOUD_SMTP_PASS, CONTACT_EMAIL, NEXT_PUBLIC_SITE_URL

4. FINAL REPOSITORY CLEANUP:
   - Verify `.gitignore` is comprehensive
   - Make sure no secrets, API keys, or credentials are committed anywhere (check `git log --all -p -- '*.env*' '*.local'`)
   - Verify `deployment-prompts.md` is either removed or gitignored — it's a development planning document, not production content
   - Remove any unused development dependencies from `package.json` if found

5. CREATE A LAUNCH CHECKLIST (as a markdown file `LAUNCH.md` in the project root):
   - [ ] DNS: studiobahar.com pointed to Vercel (A record / CNAME)
   - [ ] SSL: Automatic via Vercel (verify after DNS propagation)
   - [ ] Environment variables set in Vercel dashboard (list each one)
   - [ ] iCloud SMTP credentials working (app-specific password, not account password)
   - [ ] Contact form tested end-to-end in production (submit → email arrives → auto-reply arrives)
   - [ ] Sanity CMS accessible at studiobahar.com/studio
   - [ ] Sanity CORS configured for studiobahar.com
   - [ ] All team member photos present (no placeholder gradients)
   - [ ] Social links verified (Instagram only, no dead links)
   - [ ] Legal pages reviewed and approved by owner
   - [ ] Cookie consent banner appears when NEXT_PUBLIC_ENABLE_COOKIE_CONSENT=true
   - [ ] Vercel Analytics active (verify data appears in Vercel dashboard after deploy)
   - [ ] Google Search Console configured with sitemap submitted
   - [ ] Social sharing tested (Facebook Sharing Debugger, Twitter Card Validator)
   - [ ] Mobile responsive — tested on real devices or device emulation
   - [ ] Lighthouse scores documented (homepage, a gallery page, contact page)

6. FINAL LIGHTHOUSE RUN. Run Lighthouse on mobile for homepage, a gallery page, and the contact page. Document scores in the `LAUNCH.md` file. If any Core Web Vital fails the "good" threshold, flag it.

SAFETY RULES:
- NEVER remove the placeholder fallback system in the data-fetching layer. It's a resilience layer that ships to production.
- Do NOT change CSS custom properties, color tokens, typography, or layout primitives in `src/styles/globals.css` without showing the user and getting explicit approval.
- The GSAP animation library and Lenis smooth scroll are loaded globally through `src/providers/Providers.tsx`. Do not restructure this.
- The production build must pass cleanly before asking the user for final verification.

FINAL VERIFICATION: Walk the user through the entire site one last time. Every page, every interaction — home, work, each album, about, contact, process, privacy, terms. The user gives the final go/no-go. If they spot anything, fix it. This is the last checkpoint before the site goes live.
```

---

## Execution Order

Run these in order. Each one builds on the last. **Do not skip the verification step at the end of each prompt.** The user must confirm before moving on.

| Step | Prompt | Why This Order | User Verifies |
|------|--------|----------------|---------------|
| 1 | Prompt 1 — About Page & Social Cleanup | Fix all remaining placeholder content before deploying anything | Approve team section, verify social links |
| 2 | Prompt 2 — Deployment Infrastructure | Sets up the pipeline everything else flows through | Site still works with new headers/CSP |
| 3 | Prompt 3 — Performance | Optimize before search engines start indexing | No visual regressions, animations intact |
| 4 | Prompt 4 — Error Handling & Loading States | Production resilience before going live | Loading skeletons, 404, error pages look right |
| 5 | Prompt 5 — SEO & Social Sharing | Prepare for discovery | OG images and metadata look correct |
| 6 | Prompt 6 — Accessibility | Compliance and inclusivity | Approve any color/style adjustments |
| 7 | Prompt 7 — Final Verification & Launch | Last gate before going live | Full site walkthrough, final go/no-go |
