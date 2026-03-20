# Silk Road Studio

Cinematic photography portfolio — moody, minimal, exhibition-grade.

**Sole developer project.** No team, no other contributors.

## Policies

- **Commits:** After every component or meaningful unit of work. Not after phases.
- **Thinking:** Always use extended thinking (ultrathink) throughout all development work.
- **Server Components by default** — only use Client Components when browser APIs, state, or interactivity are required.
- **Named animation presets** — all animations reference presets in `lib/animations.ts`, never inline GSAP configs.
- **Design tokens as source of truth** — all visual values come from CSS custom properties in `globals.css`.

## Key Architecture

- **Stack:** Next.js 16 + React 19 + TypeScript + Tailwind 4 + GSAP + Lenis + Zustand + Sanity
- **Animations:** GSAP with ScrollTrigger, SplitText, Flip. Framer Motion via LazyMotion (reduced bundle).
- **Smooth scroll:** Lenis synced to GSAP ticker (lerp: 0.06, duration: 1.4)
- **State:** Zustand for UI state (menu, transitions, morph source)
- **CMS:** Sanity with embedded studio at `/studio`

## Reference

- Design tokens: `src/styles/globals.css`
- Animation presets: `src/lib/animations.ts`
