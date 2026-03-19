# Decision Log — Bamyan Storyworks

Append-only. Never edit existing entries — only add new rows.

| ID | Phase | Decision | Reason |
|----|-------|----------|--------|
| 1 | A | Upgraded `next-sanity` from ^9.0.0 to ^12.0.0, `sanity` from ^3.80.0 to ^5.16.0 | next-sanity v9 has peer dep on Next.js ^14/^15, v12 supports Next.js 16 |
| 2 | A | Added `forceConsistentCasingInFileNames: false` to tsconfig.json | GSAP ships Flip.js (uppercase) but types/flip.d.ts (lowercase), causing TS error on case-insensitive macOS |
| 3 | A | Sanity project not created on sanity.io — schemas only | No Sanity credentials yet; schemas/client/queries are ready, just needs project ID in .env |
| 4 | B | Header/nav-link styles in globals.css instead of styled-jsx | styled-jsx is unreliable in Next.js App Router client components; globals.css is simpler and more predictable |
| 5 | B | Footer is pure Server Component (no animation wrappers yet) | Plan defers animation wrappers to Phase C; keeps Footer simple for now |
| 6 | C | Skipped `src/hooks/useLenis.ts` | `useLenis()` from `lenis/react` already imported in BackToTop and MobileMenu — custom wrapper adds no value |
| 7 | C | Added `as const` to splitConfig objects in animations.ts | SplitText constructor requires literal types for `type` and `mask` — without const assertion, TS widens to `string` |
| 8 | D | Hardcoded heroSequence timeline in Hero.tsx instead of generic interpreter | 7 GSAP calls using heroSequence timing values as source of truth; generic interpreter adds complexity with no reuse benefit |
| 9 | D | Skipped heroSequence step 0 (grain-overlay) | GSAP cannot target `::after` pseudo-elements; grain overlay at 0.04 opacity is always present via CSS |
| 10 | D | Gradient div placeholders instead of image files | No Sanity project ID yet; avoids binary placeholder files in repo |
| 11 | D | Lazy Sanity client via Proxy | `createClient` throws at import time if `projectId` is undefined; lazy init defers to first fetch call where try/catch handles it |
| 12 | E | Skipped GSAP Flip — all transitions use wipe only | Flip morph adds complexity with no visual payoff for placeholder gradients; wipe is consistent and simpler |
| 13 | E | Skipped NarrativeSection component — used TextReveal directly | TextReveal variant="words" scrub already provides word-by-word scroll-scrub; wrapper adds no value |
| 14 | E | Added delay prop to TextReveal instead of new component | Backward-compatible addition; enables choreographed AlbumHero entrance without a separate animation wrapper |
| 15 | E | FilterableGrid uses CSS opacity fade, not GSAP Flip | 300ms CSS transition is simpler, respects prefers-reduced-motion via global rule, no Flip dependency |
| 16 | E | Direct import for HorizontalScrollGallery instead of dynamic ssr:false | Next.js 16 disallows ssr:false in Server Components; direct import works because useGSAP handles SSR safely |
| 17 | F | Zod schema in shared `src/lib/validations.ts` | Both ContactForm (client) and action.ts (server) import same schema — DRY, single source of truth |
| 18 | F | Button builds all variants (primary/outline/ghost) now | Small component; outline/ghost needed in Phase H, prevents revisiting |
| 19 | F | MagneticButton uses `as="div"` inside Button | Avoids nested `<button>` elements (invalid HTML) |
| 20 | F | Server action graceful degradation without env vars | Logs submission and returns success when RESEND_API_KEY/CONTACT_EMAIL missing; allows dev/build without Resend |
| 21 | F | About services — no thumbnails | Sanity about schema has services as {title, description} only; adding images is scope creep |
| 22 | G | Responsive tokens at CSS source, not per-component md: classes | One change in globals.css fixes 20+ components; aligns with "design tokens as source of truth" |
| 23 | G | ProjectCard hover: no change for touch | Paused timeline has zero cost; overlay invisible on touch; tap navigates via TransitionLink |
| 24 | G | No dynamic imports (kept from Phase E Decision #16) | Next.js 16 disallows ssr:false in Server Components; useGSAP handles SSR safely |
| 25 | G | will-change via helper, not inline styles | Centralized pattern; prevents forgotten cleanup; matches spec's onStart/onComplete strategy |
