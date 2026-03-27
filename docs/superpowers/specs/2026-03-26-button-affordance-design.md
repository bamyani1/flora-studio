# Button Affordance Fix — Design Spec

## Context

Audit of all buttons and interactive elements across Bahar Studio revealed that 9 elements lack proper visual affordance — they have no border, no background, or extremely faint borders, making them hard for users to recognize as clickable. The site's moody, minimal aesthetic caused several CTAs to drift into "decorative text" territory.

**Goal:** Add subtle borders to all 9 elements so they read as buttons, while preserving the editorial tone. Route 7 of 9 through the existing `Button` component for maximum consistency; handle 2 edge cases with direct edits.

## Approach

Extend `src/components/ui/Button.tsx` to be polymorphic and add new variants, then refactor each problem element to use it.

## Changes to Button.tsx

### 1. Polymorphic `as` prop

Button currently renders `<button>` only. Add an `as` prop so it can render as `TransitionLink`, `<a>`, `<Link>`, or any element, while forwarding all props (including `href`).

```tsx
// Usage examples:
<Button variant="outline-accent" size="xs">Enter the Studio →</Button>
<Button as={TransitionLink} href="/work" variant="outline" size="sm">View Work →</Button>
<Button as="a" href="#section" variant="outline-accent" size="xs">Scroll</Button>
```

TypeScript: Use a generic component pattern so `href` etc. are typed correctly based on the `as` prop.

### 2. New variants

| Variant          | Classes                                                                                    | Use case                                               |
| ---------------- | ------------------------------------------------------------------------------------------ | ------------------------------------------------------ |
| `outline-accent` | `border border-primary bg-transparent text-primary hover:bg-primary/10 hover:text-primary` | Hero CTAs, reset buttons, About CTA                    |
| `outline-subtle` | `border border-white/35 bg-transparent text-white hover:border-white/50`                   | Editorial, exhibition, timeline links on dark sections |

Existing variants (`primary`, `outline`, `ghost`) stay unchanged.

### 3. New size

| Size | Classes                                                        | Use case                              |
| ---- | -------------------------------------------------------------- | ------------------------------------- |
| `xs` | `px-6 py-3 text-[11px] font-label uppercase tracking-[0.15em]` | Hero CTAs, editorial/exhibition links |

Existing sizes (`sm`, `md`, `lg`) stay unchanged.

## Per-Element Changes

### Critical Fixes

**1. Hero CTA Buttons (5 files)**

- Files: `src/components/heroes/HeroMonolith.tsx`, `HeroNegative.tsx`, `HeroSplice.tsx`, `HeroOverprint.tsx`, `HeroDiptych.tsx`
- Before: `<button>` with no border, inline font styles, `hover:opacity-70`
- After: `<Button variant="outline-accent" size="xs">ENTER THE STUDIO →</Button>`
- Note: Each hero uses a different font-family CSS variable (`--font-v3-body`, `--font-v5-body`, etc.). Pass these via `style={{ fontFamily: "var(--font-v3-body)" }}` on the Button — the component forwards unknown props to the rendered element. Keep the GSAP-targeted class names (e.g., `mono-cta`, `neg-cta`) on the parent wrapper div, not the button itself.

**2. "Send another message" Reset Buttons (3 files)**

- Files: `src/components/ui/CinematicContactForm.tsx` (line ~146), `src/components/ui/InlineContactForm.tsx` (line ~98), `src/components/process-reference/ProcessContactForm.tsx` (line ~95)
- Before: `<button>` with no border, just `text-primary`
- After: `<Button variant="outline-accent" size="sm">Send another message</Button>`
- Note: ProcessContactForm uses `var(--process-primary)` — add className override: `className="border-[var(--process-primary)] text-[var(--process-primary)] hover:bg-[var(--process-primary)]/10"`

**3. "View Work" Link in StartAStory**

- File: `src/components/sections/StartAStory.tsx` (line ~21)
- Before: `<TransitionLink>` with no border, `text-muted`
- After: `<Button as={TransitionLink} href="/work" variant="outline" size="sm">View Work →</Button>`
- Note: Use `outline` (gray border) not `outline-accent` so "Inquire" remains the dominant CTA.

**4. About Page "Get in touch" CTA**

- File: `src/app/about/page.tsx` (line ~595)
- Before: `<TransitionLink>` with decorative line + chevron, no border
- After: `<Button as={TransitionLink} href="/contact" variant="outline-accent" size="sm">Get in touch →</Button>`
- Note: Remove the custom line/chevron markup. Replace with simple arrow character.

### Moderate Fixes

**5. Editorial "See the work" CTA**

- File: `src/components/landing/LandingEditorial.tsx` (line ~81)
- Before: `<TransitionLink>` with `border-white/20` (too faint)
- After: `<Button as={TransitionLink} href="/work" variant="outline-subtle" size="xs">See the work</Button>`
- Note: Keep the existing hover fill animation by adding it as a className override with the inner `<div>` for the slide-up effect.

**6. Exhibition "Explore Exhibition" Link**

- File: `src/components/landing/LandingExhibition.tsx` (line ~55)
- Before: `<TransitionLink>` with bottom-border only
- After: `<Button as={TransitionLink} href="/work" variant="outline-subtle" size="xs">Explore Exhibition →</Button>`

**7. Process Timeline Action Links**

- File: `src/components/process-reference/ProcessTimeline.tsx` (line ~193)
- Before: `<Link>` with `border-outline-variant/45` (too faint)
- After: `<Button as={Link} href={step.action.href} variant="outline-subtle" size="xs" className="border-white/30 hover:border-[var(--process-primary)] hover:text-[var(--process-primary)]">{step.action.label}</Button>`
- Note: Wrapped in `ProcessMagnetic` — keep that wrapper.

### Inline Fixes (not through Button)

**8. Process Hero Scroll Chevron**

- File: `src/components/process-reference/ProcessHero.tsx` (line ~132)
- Change: Add `border border-[var(--process-primary)]/40 rounded-full` and set explicit `w-12 h-12` (48px) to the `<a>` element. Add `flex items-center justify-center`.
- Reason: Circular icon-only element doesn't fit Button's text-button API.

**9. "Access" Card CTA**

- File: `src/components/landing/LandingStudioCards.tsx` (line ~79)
- Change: Restyle the inner "Access" text from `font-headline text-4xl italic border-b` to `font-label text-xs uppercase tracking-[0.2em] font-semibold border border-surface-deep/35 px-8 py-3`. Keep "Join Bahar Studio" label above.
- Reason: Card layout with label + title doesn't fit Button's single-child API.

## Files Modified

1. `src/components/ui/Button.tsx` — polymorphic `as` prop, 2 new variants, 1 new size
2. `src/components/heroes/HeroMonolith.tsx` — swap `<button>` for `<Button>`
3. `src/components/heroes/HeroNegative.tsx` — same
4. `src/components/heroes/HeroSplice.tsx` — same
5. `src/components/heroes/HeroOverprint.tsx` — same
6. `src/components/heroes/HeroDiptych.tsx` — same
7. `src/components/ui/CinematicContactForm.tsx` — swap reset `<button>` for `<Button>`
8. `src/components/ui/InlineContactForm.tsx` — same
9. `src/components/process-reference/ProcessContactForm.tsx` — swap reset `<button>` for `<Button>`
10. `src/components/sections/StartAStory.tsx` — swap `<TransitionLink>` for `<Button as={TransitionLink}>`
11. `src/app/about/page.tsx` — swap custom CTA for `<Button as={TransitionLink}>`
12. `src/components/landing/LandingEditorial.tsx` — swap `<TransitionLink>` for `<Button as={TransitionLink}>`
13. `src/components/landing/LandingExhibition.tsx` — same
14. `src/components/process-reference/ProcessTimeline.tsx` — swap `<Link>` for `<Button as={Link}>`
15. `src/components/process-reference/ProcessHero.tsx` — add border classes to `<a>`
16. `src/components/landing/LandingStudioCards.tsx` — restyle Access card inner text

## Verification

1. `npm run type-check` — TypeScript must pass with polymorphic Button types
2. `npm run build` — production build must succeed
3. Visual check each page in browser:
   - Landing page: editorial CTA, exhibition link, studio cards Access
   - Work page: filter buttons (unchanged), View Work link
   - Individual album page: hero CTA
   - Process page: scroll chevron, timeline links
   - About page: Get in touch CTA
   - Contact page: form submit (unchanged), reset button after submit
4. Hover states work on all modified elements
5. Keyboard focus rings appear on all modified elements (inherited from Button base)
