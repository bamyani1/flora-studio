# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Bahar Studio — photography portfolio with cinematic animations.
Next.js 16.2 / React 19 / TypeScript 5.7 (strict) / Tailwind CSS 4.0 / GSAP 3.14 / Lenis 1.3 / Sanity CMS 5.16 / Zustand 5.0 / Zod 3.24 / Resend 4.0

## Commands

- `npm run dev` — dev server
- `npm run build` — production build
- `npm run lint` — ESLint (flat config)
- `npm run type-check` — tsc --noEmit
- `npm run test:unit` — vitest run (tests/unit + tests/component)
- `npm run test:e2e` — playwright test (tests/e2e, port 3101)
- `npm run verify` — full pipeline: lint + type-check + unit + e2e + build

## Code style

- Prettier: semicolons, double quotes, 2-space indent, trailing commas, 100 char printWidth
- ESLint 9 flat config with next/core-web-vitals + next/typescript
- Path alias: `@/*` maps to `./src/*`
- Server components by default; add `"use client"` only when the component uses hooks, event handlers, or browser APIs

## Architecture

### Animation system — CRITICAL RULES

**Never shorten GSAP durations to fix timing issues.** Adjust timeline `position` values instead. Durations are tuned to the visual rhythm — changing them destroys the feel.

All animation presets live in `/src/lib/animations.ts`. Components import named presets and apply them via `useGSAP`. Never define raw GSAP tween values inline — always use or extend a preset from animations.ts.

Easing tokens are in `/src/lib/easings.ts`. Use named easings rather than raw strings.

**Always import `gsap`, `ScrollTrigger`, etc. from `@/lib/gsap`**, never directly from `gsap` or `gsap/ScrollTrigger`. `/src/lib/gsap.ts` is the single plugin registration point.

### Lenis + ScrollTrigger sync

Lenis smooth scroll is synced to GSAP's ticker in `/src/providers/Providers.tsx`. When `prefers-reduced-motion: reduce` is active, Lenis is disabled and native scroll is used. ScrollTrigger.refresh() must be called after route transitions.

### Reduced motion

Every animation component must check `useReducedMotion()` and skip to the final visible state with `gsap.set()` when true. The `reducedMotionFallbacks` export in animations.ts documents expected behavior for each preset.

### Route transitions

State machine in `/src/stores/ui-store.ts`: idle → leaving → entering → idle. Never call `router.push()` directly for internal navigation — always use `<TransitionLink>` or the store's `requestRouteTransition`.

### Tailwind v4

Uses the `@theme` directive in `/src/styles/globals.css` for all design tokens as CSS custom properties. No tailwind.config.js — all configuration is CSS-first via `@theme {}` blocks. To add a new design token, add it inside the `@theme` block.

### Sanity CMS

- Client: lazy singleton proxy in `/src/sanity/client.ts`
- Schemas: `/src/sanity/schemas/` (album, about)
- Queries: GROQ in `/src/sanity/queries.ts` using `defineQuery()`
- All fetch functions fall back to placeholder data when CMS is unavailable — this is intentional for development without Sanity credentials. Do not remove the fallbacks.

### Gallery layout

`/src/lib/gallery-layout.ts` maps albums to a repeating section pattern. Each section type has its own component under `/src/components/sections/gallery/`.

### Contact form

Server action in `/src/app/contact/action.ts` with Zod validation. Sends email via Resend. Gracefully skips if RESEND_API_KEY is not set.

## Testing

- **Unit/component (Vitest):** `tests/unit/` and `tests/component/`. GSAP and next/navigation are globally mocked in `tests/setup/vitest.setup.ts`.
- **E2E (Playwright):** `tests/e2e/`. Dev server on port **3101** (not 3000). Playwright starts its own via `npm run dev -- --hostname 127.0.0.1 --port 3101`.

## Investigation rule

When diagnosing bugs or unexpected behavior, investigate deeply with proof before proposing fixes. Read the actual code paths, trace through the actual data flow. Do not guess-and-patch.
