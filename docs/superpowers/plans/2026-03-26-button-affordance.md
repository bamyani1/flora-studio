# Button Affordance Fix — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Fix 9 interactive elements that lack visual affordance (no border/shape) by extending the Button component to be polymorphic and routing 7 elements through it, with 2 direct inline fixes.

**Architecture:** Extend `src/components/ui/Button.tsx` with a generic `as` prop for polymorphism, add `outline-accent` and `outline-subtle` variants plus an `xs` size, then refactor each problem element. Two edge cases (circular scroll chevron, Access card) get direct class edits.

**Tech Stack:** React 19, TypeScript, Tailwind 4, Next.js 16

---

### Task 1: Extend Button.tsx — polymorphic `as` prop + new variants + new size

**Files:**

- Modify: `src/components/ui/Button.tsx`

- [ ] **Step 1: Rewrite Button.tsx with polymorphic typing and new variants**

Replace the entire file with:

```tsx
"use client";

import { type ComponentPropsWithoutRef, type ElementType, type ReactNode } from "react";
import { cn } from "@/lib/utils";
import { MagneticButton } from "@/components/animations/MagneticButton";

const variantClasses = {
  primary: "bg-primary text-background hover:bg-primary-muted",
  outline: "border border-border bg-transparent text-text hover:border-border-hover",
  "outline-accent":
    "border border-primary bg-transparent text-primary hover:bg-primary/10 hover:text-primary",
  "outline-subtle": "border border-white/35 bg-transparent text-white hover:border-white/50",
  ghost: "bg-transparent text-text hover:text-text-heading",
} as const;

const sizeClasses = {
  xs: "px-6 py-3 text-[11px] font-label uppercase tracking-[0.15em]",
  sm: "px-4 py-2 text-sm min-h-[44px]",
  md: "px-6 py-3 text-base",
  lg: "px-8 py-4 text-lg",
} as const;

type ButtonOwnProps<T extends ElementType = "button"> = {
  as?: T;
  variant?: keyof typeof variantClasses;
  size?: keyof typeof sizeClasses;
  magnetic?: boolean;
  children?: ReactNode;
  className?: string;
};

type ButtonProps<T extends ElementType = "button"> = ButtonOwnProps<T> &
  Omit<ComponentPropsWithoutRef<T>, keyof ButtonOwnProps<T>>;

export function Button<T extends ElementType = "button">({
  as,
  children,
  variant = "primary",
  size = "md",
  magnetic = false,
  className,
  ...props
}: ButtonProps<T>) {
  const Component = as || "button";

  const element = (
    <Component
      className={cn(
        "inline-flex items-center justify-center font-body font-medium tracking-wide transition-colors duration-normal",
        "focus-visible:outline-2 focus-visible:outline-primary focus-visible:outline-offset-4",
        "disabled:pointer-events-none disabled:opacity-50",
        variantClasses[variant],
        sizeClasses[size],
        className,
      )}
      {...props}
    >
      {children}
    </Component>
  );

  if (magnetic) {
    return (
      <MagneticButton as="div" className="inline-block">
        {element}
      </MagneticButton>
    );
  }

  return element;
}
```

- [ ] **Step 2: Run type-check to verify polymorphic types compile**

Run: `npm run type-check`
Expected: PASS — no errors in Button.tsx

- [ ] **Step 3: Commit**

```bash
git add src/components/ui/Button.tsx
git commit -m "feat(Button): add polymorphic as prop, outline-accent/outline-subtle variants, xs size"
```

---

### Task 2: Refactor Hero CTA buttons (5 files)

**Files:**

- Modify: `src/components/heroes/HeroMonolith.tsx:225-241`
- Modify: `src/components/heroes/HeroNegative.tsx:321-340`
- Modify: `src/components/heroes/HeroSplice.tsx:560-581`
- Modify: `src/components/heroes/HeroOverprint.tsx:182-202`
- Modify: `src/components/heroes/HeroDiptych.tsx:317-342`

- [ ] **Step 1: Refactor HeroMonolith CTA**

In `src/components/heroes/HeroMonolith.tsx`, add the import at the top with the other imports:

```tsx
import { Button } from "@/components/ui/Button";
```

Replace lines 225-241 (the CTA block):

```tsx
{
  /* CTA — anchored to bottom */
}
<div className="mono-cta absolute bottom-10 flex justify-center">
  <button
    type="button"
    className="group inline-flex items-center gap-2 bg-transparent border-none cursor-pointer transition-opacity duration-300 hover:opacity-70"
    style={{
      fontFamily: "var(--font-v3-body)",
      fontWeight: 400,
      fontSize: 11,
      letterSpacing: "0.15em",
      textTransform: "uppercase",
      color: "var(--color-primary)",
    }}
  >
    <span>ENTER THE STUDIO</span>
    <span aria-hidden="true">&rarr;</span>
  </button>
</div>;
```

With:

```tsx
{
  /* CTA — anchored to bottom */
}
<div className="mono-cta absolute bottom-10 flex justify-center">
  <Button
    type="button"
    variant="outline-accent"
    size="xs"
    className="gap-2"
    style={{ fontFamily: "var(--font-v3-body)" }}
  >
    ENTER THE STUDIO <span aria-hidden="true">&rarr;</span>
  </Button>
</div>;
```

- [ ] **Step 2: Refactor HeroNegative CTA**

In `src/components/heroes/HeroNegative.tsx`, add the import:

```tsx
import { Button } from "@/components/ui/Button";
```

Replace lines 321-340 (the CTA button):

```tsx
<button
  type="button"
  className="neg-cta absolute bottom-6 right-12 group inline-flex items-center gap-2 bg-transparent border-none cursor-pointer"
  style={{
    fontFamily: "var(--font-v5-body)",
    fontWeight: 500,
    fontSize: 10,
    letterSpacing: "0.15em",
    textTransform: "uppercase",
    color: "var(--color-primary)",
  }}
>
  <span className="transition-opacity duration-300 group-hover:opacity-70">VIEW REEL</span>
  <span
    className="inline-block transition-all duration-300 group-hover:opacity-70 group-hover:translate-x-0.5"
    aria-hidden="true"
  >
    &rarr;
  </span>
</button>
```

With:

```tsx
<div className="neg-cta absolute bottom-6 right-12">
  <Button
    type="button"
    variant="outline-accent"
    size="xs"
    className="gap-2"
    style={{ fontFamily: "var(--font-v5-body)" }}
  >
    VIEW REEL <span aria-hidden="true">&rarr;</span>
  </Button>
</div>
```

Note: The `neg-cta` class moves to a wrapper div so GSAP can still target it without conflicting with Button's classes.

- [ ] **Step 3: Refactor HeroSplice CTA**

In `src/components/heroes/HeroSplice.tsx`, add the import:

```tsx
import { Button } from "@/components/ui/Button";
```

Replace lines 560-581 (the CTA button):

```tsx
<button
  type="button"
  className="spl-meta-right group inline-flex items-center gap-2 bg-transparent border-none cursor-pointer"
  style={{
    fontFamily: "var(--font-v5-body)",
    fontWeight: 500,
    fontSize: 10,
    letterSpacing: "0.15em",
    textTransform: "uppercase",
    color: "var(--color-primary)",
  }}
>
  <span className="transition-opacity duration-300 group-hover:opacity-70">WATCH THE CUT</span>
  <span
    className="inline-block transition-all duration-300 group-hover:opacity-70 group-hover:translate-x-0.5"
    aria-hidden="true"
  >
    &rarr;
  </span>
</button>
```

With:

```tsx
<div className="spl-meta-right">
  <Button
    type="button"
    variant="outline-accent"
    size="xs"
    className="gap-2"
    style={{ fontFamily: "var(--font-v5-body)" }}
  >
    WATCH THE CUT <span aria-hidden="true">&rarr;</span>
  </Button>
</div>
```

- [ ] **Step 4: Refactor HeroOverprint CTA**

In `src/components/heroes/HeroOverprint.tsx`, add the import:

```tsx
import { Button } from "@/components/ui/Button";
```

Replace lines 183-202 (the CTA button):

```tsx
<button
  type="button"
  className="group inline-flex items-center gap-3 bg-transparent border-none cursor-pointer"
  style={{
    fontFamily: "var(--font-v1-body)",
    fontWeight: 500,
    fontSize: 13,
    letterSpacing: "0.15em",
    textTransform: "uppercase",
    color: "var(--color-primary)",
  }}
>
  <span>EXPLORE THE STUDIO</span>
  <span
    className="inline-block transition-transform duration-300 group-hover:translate-x-1"
    aria-hidden="true"
  >
    &rarr;
  </span>
</button>
```

With:

```tsx
<Button
  type="button"
  variant="outline-accent"
  size="xs"
  className="gap-3"
  style={{ fontFamily: "var(--font-v1-body)" }}
>
  EXPLORE THE STUDIO <span aria-hidden="true">&rarr;</span>
</Button>
```

- [ ] **Step 5: Refactor HeroDiptych CTA**

In `src/components/heroes/HeroDiptych.tsx`, add the import:

```tsx
import { Button } from "@/components/ui/Button";
```

Replace lines 317-342 (the CTA block):

```tsx
<div className="dipt-cta" style={{ marginTop: "2.5rem" }}>
  <a
    href="#portfolio"
    className="group inline-flex items-center gap-2"
    style={{
      fontFamily: "var(--font-body)",
      fontWeight: 500,
      fontSize: 11,
      letterSpacing: "0.15em",
      textTransform: "uppercase",
      color: "var(--color-primary)",
      textDecoration: "none",
      transition: "opacity 0.3s ease",
    }}
  >
    <span className="underline-offset-4 transition-all duration-300 group-hover:underline">
      VIEW PORTFOLIO
    </span>
    <span
      className="inline-block transition-transform duration-300 group-hover:translate-x-1"
      aria-hidden="true"
    >
      &rarr;
    </span>
  </a>
</div>
```

With:

```tsx
<div className="dipt-cta" style={{ marginTop: "2.5rem" }}>
  <Button as="a" href="#portfolio" variant="outline-accent" size="xs" className="gap-2">
    VIEW PORTFOLIO <span aria-hidden="true">&rarr;</span>
  </Button>
</div>
```

- [ ] **Step 6: Run type-check**

Run: `npm run type-check`
Expected: PASS

- [ ] **Step 7: Commit**

```bash
git add src/components/heroes/HeroMonolith.tsx src/components/heroes/HeroNegative.tsx src/components/heroes/HeroSplice.tsx src/components/heroes/HeroOverprint.tsx src/components/heroes/HeroDiptych.tsx
git commit -m "refactor(heroes): use Button outline-accent for all hero CTAs"
```

---

### Task 3: Refactor "Send another message" reset buttons (3 files)

**Files:**

- Modify: `src/components/ui/CinematicContactForm.tsx:146-152`
- Modify: `src/components/ui/InlineContactForm.tsx:98-104`
- Modify: `src/components/process-reference/ProcessContactForm.tsx:95-102`

- [ ] **Step 1: Refactor CinematicContactForm reset button**

In `src/components/ui/CinematicContactForm.tsx`, add the import alongside existing imports (after line 8):

```tsx
import { Button } from "@/components/ui/Button";
```

Replace lines 146-152:

```tsx
<button
  type="button"
  onClick={() => setSubmitted(false)}
  className="mt-8 font-label text-xs uppercase tracking-wider text-primary transition-colors hover:text-text"
>
  Send another message
</button>
```

With:

```tsx
<Button
  type="button"
  variant="outline-accent"
  size="sm"
  className="mt-8"
  onClick={() => setSubmitted(false)}
>
  Send another message
</Button>
```

- [ ] **Step 2: Refactor InlineContactForm reset button**

In `src/components/ui/InlineContactForm.tsx`, add the import alongside existing imports (after line 6):

```tsx
import { Button } from "@/components/ui/Button";
```

Replace lines 98-104:

```tsx
<button
  type="button"
  onClick={() => setSubmitted(false)}
  className="mt-8 font-label text-sm uppercase tracking-wider text-primary transition-colors hover:text-text"
>
  Send another message
</button>
```

With:

```tsx
<Button
  type="button"
  variant="outline-accent"
  size="sm"
  className="mt-8"
  onClick={() => setSubmitted(false)}
>
  Send another message
</Button>
```

- [ ] **Step 3: Refactor ProcessContactForm reset button**

In `src/components/process-reference/ProcessContactForm.tsx`, add the import alongside existing imports (after line 5):

```tsx
import { Button } from "@/components/ui/Button";
```

Replace lines 95-102:

```tsx
<button
  ref={resetRef}
  type="button"
  onClick={() => setSubmitted(false)}
  className="mt-8 font-label text-xs uppercase tracking-[0.2em] text-[var(--process-primary)] transition-colors hover:text-[var(--process-on-surface)]"
>
  Send another message
</button>
```

With:

```tsx
<Button
  ref={resetRef}
  type="button"
  variant="outline-accent"
  size="sm"
  className="mt-8 border-[var(--process-primary)] text-[var(--process-primary)] hover:bg-[var(--process-primary)]/10"
  onClick={() => setSubmitted(false)}
>
  Send another message
</Button>
```

- [ ] **Step 4: Run type-check**

Run: `npm run type-check`
Expected: PASS

- [ ] **Step 5: Commit**

```bash
git add src/components/ui/CinematicContactForm.tsx src/components/ui/InlineContactForm.tsx src/components/process-reference/ProcessContactForm.tsx
git commit -m "refactor(forms): use Button outline-accent for reset buttons"
```

---

### Task 4: Refactor "View Work" link in StartAStory

**Files:**

- Modify: `src/components/sections/StartAStory.tsx:1-31`

- [ ] **Step 1: Refactor the View Work link**

In `src/components/sections/StartAStory.tsx`, add the Button import:

```tsx
import { Button } from "@/components/ui/Button";
```

Replace lines 21-26 (the View Work TransitionLink):

```tsx
<TransitionLink
  href="/work"
  className="inline-flex items-center font-label text-sm uppercase tracking-wider text-muted transition-colors duration-300 hover:text-text"
>
  View Work &rarr;
</TransitionLink>
```

With:

```tsx
<Button as={TransitionLink} href="/work" variant="outline" size="sm" className="gap-2">
  View Work <span aria-hidden="true">&rarr;</span>
</Button>
```

- [ ] **Step 2: Run type-check**

Run: `npm run type-check`
Expected: PASS

- [ ] **Step 3: Commit**

```bash
git add src/components/sections/StartAStory.tsx
git commit -m "refactor(StartAStory): use Button outline for View Work link"
```

---

### Task 5: Refactor About page "Get in touch" CTA

**Files:**

- Modify: `src/app/about/page.tsx:594-605`

- [ ] **Step 1: Add Button import and refactor CTA**

In `src/app/about/page.tsx`, add the Button import alongside existing imports:

```tsx
import { Button } from "@/components/ui/Button";
```

Replace lines 594-605 (the `<div data-about-animate>` wrapping the CTA):

```tsx
<div data-about-animate="fade-up" data-delay="1.5" className="pt-12">
  <TransitionLink
    className="inline-flex items-center gap-6 group text-primary font-label uppercase tracking-[0.5em] text-sm font-semibold"
    href="/contact"
  >
    <span>Get in touch</span>
    <div className="relative flex items-center">
      <span className="w-16 h-px bg-primary group-hover:w-32 transition-all duration-700"></span>
      <ChevronRight className="w-4 h-4 -ml-2 group-hover:ml-0 transition-all duration-700" />
    </div>
  </TransitionLink>
</div>
```

With:

```tsx
<div data-about-animate="fade-up" data-delay="1.5" className="pt-12">
  <Button
    as={TransitionLink}
    href="/contact"
    variant="outline-accent"
    size="sm"
    className="gap-2 font-semibold"
  >
    Get in touch <span aria-hidden="true">&rarr;</span>
  </Button>
</div>
```

Note: The `ChevronRight` import from lucide-react can be removed if it's no longer used elsewhere in the file. Check before removing — it may be used in other sections of the about page.

- [ ] **Step 2: Run type-check**

Run: `npm run type-check`
Expected: PASS

- [ ] **Step 3: Commit**

```bash
git add src/app/about/page.tsx
git commit -m "refactor(about): use Button outline-accent for Get in touch CTA"
```

---

### Task 6: Refactor landing page editorial & exhibition links

**Files:**

- Modify: `src/components/landing/LandingEditorial.tsx:85-93`
- Modify: `src/components/landing/LandingExhibition.tsx:57-62`

- [ ] **Step 1: Refactor LandingEditorial CTA**

In `src/components/landing/LandingEditorial.tsx`, add the Button import:

```tsx
import { Button } from "@/components/ui/Button";
```

Replace lines 85-93 (the `<div ref={ctaRef}>` block):

```tsx
<div ref={ctaRef}>
  <TransitionLink
    href="/work"
    className="group relative inline-block overflow-hidden border border-white/20 px-8 py-4 font-label text-[10px] uppercase tracking-[0.2em] text-white hover:border-white/60 transition-colors duration-500"
  >
    <span className="relative z-10">See the work</span>
    <div className="absolute inset-0 bg-white/5 translate-y-[100%] group-hover:translate-y-0 transition-transform duration-500 ease-[cubic-bezier(0.76,0,0.24,1)]"></div>
  </TransitionLink>
</div>
```

With:

```tsx
<div ref={ctaRef}>
  <Button
    as={TransitionLink}
    href="/work"
    variant="outline-subtle"
    size="xs"
    className="group relative overflow-hidden"
  >
    <span className="relative z-10">See the work</span>
    <div className="absolute inset-0 bg-white/5 translate-y-[100%] group-hover:translate-y-0 transition-transform duration-500 ease-[cubic-bezier(0.76,0,0.24,1)]"></div>
  </Button>
</div>
```

Note: The hover fill animation is preserved as children of the Button.

- [ ] **Step 2: Refactor LandingExhibition link**

In `src/components/landing/LandingExhibition.tsx`, add the Button import:

```tsx
import { Button } from "@/components/ui/Button";
```

Replace lines 57-62 (the Explore Exhibition link):

```tsx
<TransitionLink
  href="/work"
  className="font-label text-[10px] uppercase tracking-[0.2em] text-white border-b border-white/30 pb-1 hover:border-white transition-colors duration-500"
>
  Explore Exhibition
</TransitionLink>
```

With:

```tsx
<Button as={TransitionLink} href="/work" variant="outline-subtle" size="xs" className="gap-2">
  Explore Exhibition <span aria-hidden="true">&rarr;</span>
</Button>
```

- [ ] **Step 3: Run type-check**

Run: `npm run type-check`
Expected: PASS

- [ ] **Step 4: Commit**

```bash
git add src/components/landing/LandingEditorial.tsx src/components/landing/LandingExhibition.tsx
git commit -m "refactor(landing): use Button outline-subtle for editorial and exhibition CTAs"
```

---

### Task 7: Refactor Process timeline action links

**Files:**

- Modify: `src/components/process-reference/ProcessTimeline.tsx:190-199`

- [ ] **Step 1: Refactor timeline action links**

In `src/components/process-reference/ProcessTimeline.tsx`, add the Button import alongside existing imports:

```tsx
import { Button } from "@/components/ui/Button";
```

Replace lines 190-200 (the action link block):

```tsx
{
  step.action && (
    <div data-step-text data-timeline-animate className="mt-8 inline-block">
      <ProcessMagnetic>
        <Link
          href={step.action.href}
          className="inline-flex border border-outline-variant/45 px-8 py-3 font-label text-[10px] uppercase tracking-widest text-[var(--process-on-surface)] transition-all hover:border-[var(--process-primary)] hover:text-[var(--process-primary)]"
        >
          {step.action.label}
        </Link>
      </ProcessMagnetic>
    </div>
  );
}
```

With:

```tsx
{
  step.action && (
    <div data-step-text data-timeline-animate className="mt-8 inline-block">
      <ProcessMagnetic>
        <Button
          as={Link}
          href={step.action.href}
          variant="outline-subtle"
          size="xs"
          className="border-white/30 text-[var(--process-on-surface)] hover:border-[var(--process-primary)] hover:text-[var(--process-primary)]"
        >
          {step.action.label}
        </Button>
      </ProcessMagnetic>
    </div>
  );
}
```

- [ ] **Step 2: Run type-check**

Run: `npm run type-check`
Expected: PASS

- [ ] **Step 3: Commit**

```bash
git add src/components/process-reference/ProcessTimeline.tsx
git commit -m "refactor(process): use Button outline-subtle for timeline action links"
```

---

### Task 8: Inline fix — Process hero scroll chevron

**Files:**

- Modify: `src/components/process-reference/ProcessHero.tsx:132-144`

- [ ] **Step 1: Add border and sizing to scroll chevron**

In `src/components/process-reference/ProcessHero.tsx`, replace lines 132-144:

```tsx
<a
  ref={chevronRef}
  href="#process"
  onClick={(event) => {
    event.preventDefault();
    document.getElementById("process")?.scrollIntoView({ behavior: "smooth", block: "start" });
  }}
  className="interactive cursor-pointer"
>
  <ChevronDown className="h-8 w-8 text-[var(--process-primary)]" />
</a>
```

With:

```tsx
<a
  ref={chevronRef}
  href="#process"
  onClick={(event) => {
    event.preventDefault();
    document.getElementById("process")?.scrollIntoView({ behavior: "smooth", block: "start" });
  }}
  className="interactive cursor-pointer flex h-12 w-12 items-center justify-center rounded-full border border-[var(--process-primary)]/40"
>
  <ChevronDown className="h-6 w-6 text-[var(--process-primary)]" />
</a>
```

Changes: Added `flex h-12 w-12 items-center justify-center rounded-full border border-[var(--process-primary)]/40` to the anchor. Reduced chevron from `h-8 w-8` to `h-6 w-6` to fit inside the 48px circle.

- [ ] **Step 2: Run type-check**

Run: `npm run type-check`
Expected: PASS

- [ ] **Step 3: Commit**

```bash
git add src/components/process-reference/ProcessHero.tsx
git commit -m "fix(process): add circular border to scroll chevron for better affordance"
```

---

### Task 9: Inline fix — "Access" card CTA restyle

**Files:**

- Modify: `src/components/landing/LandingStudioCards.tsx:79-86`

- [ ] **Step 1: Restyle Access card inner text as button**

In `src/components/landing/LandingStudioCards.tsx`, replace lines 79-86:

```tsx
<TransitionLink href="/contact" className="relative z-10 flex flex-col items-center">
  <p className="font-label text-[10px] uppercase tracking-[0.3em] mb-6 opacity-70 group-hover:opacity-100 transition-opacity">
    Join the Studio
  </p>
  <span className="font-headline text-4xl md:text-5xl border-b border-surface-deep/30 pb-2 group-hover:border-surface-deep transition-colors duration-500">
    Access
  </span>
</TransitionLink>
```

With:

```tsx
<TransitionLink href="/contact" className="relative z-10 flex flex-col items-center">
  <p className="font-label text-[10px] uppercase tracking-[0.3em] mb-6 opacity-70 group-hover:opacity-100 transition-opacity">
    Join the Studio
  </p>
  <span className="font-label text-xs uppercase tracking-[0.2em] font-semibold border border-surface-deep/35 px-8 py-3 group-hover:border-surface-deep transition-colors duration-500">
    Access
  </span>
</TransitionLink>
```

Changes: Replaced `font-headline text-4xl md:text-5xl border-b pb-2` (large italic serif with bottom border) with `font-label text-xs uppercase tracking-[0.2em] font-semibold border px-8 py-3` (small label with full border and padding). Now reads as a button inside the card.

- [ ] **Step 2: Run type-check**

Run: `npm run type-check`
Expected: PASS

- [ ] **Step 3: Commit**

```bash
git add src/components/landing/LandingStudioCards.tsx
git commit -m "fix(landing): restyle Access card text as bordered button label"
```

---

### Task 10: Full build verification

- [ ] **Step 1: Run full type-check**

Run: `npm run type-check`
Expected: PASS — zero errors

- [ ] **Step 2: Run production build**

Run: `npm run build`
Expected: PASS — build completes successfully

- [ ] **Step 3: Run lint**

Run: `npm run lint`
Expected: PASS — no new lint errors

- [ ] **Step 4: Visual verification checklist**

Run: `npm run dev`

Check each page in the browser:

1. **Landing page** (`/`): Verify editorial "See the work" button has visible border, exhibition "Explore Exhibition" has full border + arrow, Access card shows bordered label-style button
2. **Work page** (`/work`): Verify "View Work" link in StartAStory section has outline border. Filter buttons should be unchanged
3. **Album detail** (`/work/[any-slug]`): Verify hero CTA has bordered button with arrow
4. **Process page** (`/process`): Verify scroll chevron has circular border, timeline action links have visible borders
5. **About page** (`/about`): Verify "Get in touch" CTA at bottom is a bordered button with arrow
6. **Contact page** (`/contact`): Submit the form, verify "Send another message" has bordered button. Form submit button should be unchanged
7. **Hover states**: Every modified button changes on hover (border brighten, bg tint, or color shift)
8. **Keyboard focus**: Tab through each page — all modified buttons show primary-color focus ring with 4px offset

- [ ] **Step 5: Commit any fixes found during visual review**

If any visual issues are found, fix them and commit with descriptive messages.
