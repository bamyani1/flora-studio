# Bamyan Storyworks

Cinematic photography portfolio — moody, minimal, exhibition-grade.

**Sole developer project.** No team, no other contributors.

## Current Phase

**Phase A — Foundation** → `.plan/phase-a-foundation.md`

## Session Start Protocol

1. Read `.plan/status.md` — see what's built and what's in progress
2. Read the current phase dossier (above) — get the full spec
3. If needed, read `.plan/decisions.md` — understand prior deviations

## Policies

- **Commits:** After every component or meaningful unit of work. Not after phases.
- **Thinking:** Always use extended thinking (ultrathink) throughout all development work.
- **Server Components by default** — only use Client Components when browser APIs, state, or interactivity are required.
- **Named animation presets** — all animations reference presets in `lib/animations.ts`, never inline GSAP configs.
- **Design tokens as source of truth** — all visual values come from CSS custom properties in `globals.css`.

## Session End Protocol

1. Ensure all work is committed
2. Update `.plan/status.md` — check off completed items, update phase status
3. Log deviations in `.plan/decisions.md`
4. If phase complete: mark DONE in `status.md`, update "Current Phase" above

## Phase Transition

Change the phase letter and filename in "Current Phase" above. That's it.

## Key Architecture

- **Stack:** Next.js 16 + React 19 + TypeScript + Tailwind 4 + GSAP + Lenis + Zustand + Sanity
- **Animations:** GSAP with ScrollTrigger, SplitText, Flip. Framer Motion via LazyMotion (reduced bundle).
- **Smooth scroll:** Lenis synced to GSAP ticker (lerp: 0.06, duration: 1.4)
- **State:** Zustand for UI state (menu, transitions, morph source)
- **CMS:** Sanity with embedded studio at `/studio`

## Reference

- Full master plan: `BAMYAN-STORYWORKS-MASTER-PLAN.md`
- Design tokens: `src/styles/globals.css`
- Animation presets: `src/lib/animations.ts`
- Project structure: master plan Section "Project Structure"
