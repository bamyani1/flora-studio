# AGENTS.md

This file provides guidance to Codex (Codex.ai/code) when working with code in this repository.

## Commands

- `npm run verify` — master verification gate (lint → type-check → unit tests → e2e → build). Run before marking work done.
- `npm run dev` — start dev server
- `npm run build` — production build
- `npm run test:unit` — Vitest unit/component tests
- `npm run test:e2e` — Playwright e2e (port 3101)
- `npm run test:e2e:consent` — Playwright cookie consent suite (port 3102)
- `npm run test:e2e:all` — both e2e suites
- `npm run lint` — ESLint
- `npm run type-check` — TypeScript check (`tsc --noEmit`)
- `npm run images:process` — process images from `pictures/` into `public/images/`; `--validate` flag for validation only
- `npm run migrate:sanity` — bulk import placeholder content into Sanity (`--force` to upsert)
- `npm run verify:deploy` — post-deploy verification
- `npm run smoke` — smoke tests
- `npm run lighthouse` — Lighthouse audit

### Running a single test

- **Unit**: `npm run test:unit -- tests/unit/albums.test.ts`
- **E2E**: `npm run test:e2e -- tests/e2e/contact.spec.ts`
- **E2E by name**: `npm run test:e2e -- --grep "contact form validates"`

## Code Style

Prettier enforced: double quotes, semicolons, 2-space indent, 100 char width, trailing commas.

## Architecture

- **Tailwind CSS 4.0** with `@theme` directives in `src/styles/globals.css`. No `tailwind.config.js` exists — do not create one.
- **Design tokens** are CSS custom properties inside the `@theme` block. Use Tailwind utilities (`bg-background`, `text-primary`, `font-display`), never hardcode hex values or raw rem.
- **GSAP animations**: use named presets from `src/lib/animations.ts` and easings from `src/lib/easings.ts`. Import GSAP/plugins from `@/lib/gsap`, not from `gsap` directly.
- **Server Components** by default. Only add `"use client"` when the component needs browser APIs, state, effects, or event handlers.
- **Content runtime** (`CONTENT_RUNTIME_MODE` env var): `local` = soft-fallback to placeholders, `preview` = draft content from Sanity, `production` = fails loudly via `ContentUnavailableError`, `e2e` = checked-in fixtures.
- **Sanity CMS**: embedded studio at `/studio`, schemas in `src/sanity/schemas/`, queries in `src/sanity/queries.ts`.
- **Smooth scroll**: Lenis synced to GSAP ticker in `src/providers/Providers.tsx`. Respects `prefers-reduced-motion`.
- **State**: Zustand store at `src/stores/ui-store.ts` for UI state (menu, transitions).
- **Route groups**: `(site)` for public pages, `(studio)` for the Sanity studio at `/studio`.
- **Animations library**: `src/lib/animations.ts` has ~40 named presets (e.g., `fadeUp`, `textRevealLines`, `clipRevealUp`, `imageReveal`, `parallaxLayer`). Reuse presets; don't create inline GSAP configs.
- **Content resolution**: `src/lib/albums.ts` and `src/lib/site-content.ts` fetch from Sanity with automatic fallback to placeholders via `resolveContentAvailabilityFailure()`. E2E mode uses fixtures from `src/lib/e2e-content.ts` (single album: `march-madness`).
- **Gallery architecture**: `src/components/sections/gallery/` has variant components (`GalleryHero`, `GalleryBentoSplit`, `GalleryFullBleed`, `GalleryTextureCards`) sharing a `GallerySectionProps` interface. `FolioGallery` is an editorial layout engine supporting 11 layout types (panoramic, editorial-left/right, staggered-pair, trio-mosaic, full-bleed, diptych, etc.) with orientation-aware image sequencing.
- **Validation**: Zod schemas in `src/lib/validations.ts` (contact form) and `src/lib/cms-validation.ts` (Sanity content).
- **SEO/metadata**: `src/lib/metadata.ts` exports `baseMetadata` and JSON-LD builders (`localBusinessJsonLd`, `imageGalleryJsonLd`, `personJsonLd`, `breadcrumbJsonLd`).
- **Image pipeline**: `scripts/process-images.mjs` processes source photos from `pictures/` into `public/images/` via Sharp. Standard images: max 3200px, quality 85. Hero images: max 4800px, quality 95. EXIF auto-rotation applied.
- **Next.js image config**: AVIF + WebP formats, remote patterns for `cdn.sanity.io` and `lh3.googleusercontent.com`, 1-year cache TTL.
- **Path alias**: `@/*` → `./src/*`

## Environment

Copy `.env.example` to `.env.local` and fill in values. Key vars: `NEXT_PUBLIC_SANITY_PROJECT_ID`, `SANITY_READ_TOKEN`, SMTP creds (`ICLOUD_SMTP_USER`/`ICLOUD_SMTP_PASS`), `CONTACT_EMAIL`. Set `CONTACT_DELIVERY_MODE=stub` for local dev.

## Testing

- Unit/component: Vitest with jsdom (`tests/unit/`, `tests/component/`)
- E2E: Playwright on `127.0.0.1:3101` with `CONTENT_RUNTIME_MODE=e2e` and `CONTACT_DELIVERY_MODE=stub`
- Consent E2E: separate config on port 3102 with `NEXT_PUBLIC_ENABLE_COOKIE_CONSENT=true`

## Gotchas

- **GSAP plugins are pre-registered** in `@/lib/gsap` (ScrollTrigger, SplitText, Flip). Import from `@/lib/gsap`, never call `gsap.registerPlugin()` yourself.
- **Every GSAP animation preset must have a `reducedMotionFallbacks` entry** in `src/lib/animations.ts`. Missing entries break `prefers-reduced-motion` support.
- **No API routes** — all mutations use Server Actions (e.g., `src/app/(site)/contact/action.ts`). Don't create `src/app/api/` routes.
- **Contact form has a honeypot** (`website` field in `src/lib/validations.ts`). It's intentional — don't remove it or make it visible.
- **`server-only` imports**: `src/sanity/client.ts`, `src/lib/lqip.ts`, `src/lib/content-runtime.server.ts` — cannot be imported in client components or Edge runtime.
- **OG image routes use Edge runtime** — they fetch from Sanity directly, not through `server-only` modules.
- **Fonts are configured in `src/lib/fonts.ts`** with CSS variables (`--font-display`, `--font-body`, etc.) applied in the root layout. Don't add fonts elsewhere.
- **Content runtime auto-detection**: defaults to `local` in dev, `preview` on Vercel preview deploys, `production` on Vercel production. Override with `CONTENT_RUNTIME_MODE` env var.
- **CSS modules**: only `src/components/process-reference/process-reference.module.css` uses CSS modules. Everything else is Tailwind utilities. The module uses CSS custom properties from the `@theme` block for theme-awareness.
