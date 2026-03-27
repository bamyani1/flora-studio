# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Bahar Studio — photography portfolio. Moody, minimal, exhibition-grade. Sole developer project.

## Stack

Next.js 16 + React 19 + TypeScript + Tailwind 4 + GSAP + Lenis + Zustand + Sanity

- **Animations:** GSAP with ScrollTrigger, SplitText, Flip. Framer Motion via LazyMotion (reduced bundle).
- **Smooth scroll:** Lenis synced to GSAP ticker (lerp: 0.06, duration: 1.4)
- **State:** Zustand for UI state (menu, transitions, morph source)
- **CMS:** Sanity with embedded studio at `/studio`
- **Path alias:** `@/*` → `./src/*`

## Commands

```bash
npm run dev          # Dev server
npm run build        # Production build
npm run lint         # ESLint
npm run type-check   # TypeScript (no emit)
```

## Code Style

- Prettier: 100 char line width, 2-space indent, trailing commas, double quotes, semicolons
- ESLint: Next.js core-web-vitals + TypeScript presets

## Architecture Policies

- **Server Components by default** — Client Components only when browser APIs, state, or interactivity are required.
- **Named animation presets** — all animations reference presets in `lib/animations.ts`, never inline GSAP configs.
- **Design tokens as source of truth** — all visual values come from CSS custom properties in `globals.css`.
- **Commits:** after every component or meaningful unit of work, not after phases.

## Reference

- Design tokens: `src/styles/globals.css`
- Animation presets: `src/lib/animations.ts`
- Easing functions: `src/lib/easings.ts`
