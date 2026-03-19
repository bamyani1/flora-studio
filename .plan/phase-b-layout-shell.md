# Phase B — Layout Shell (3–4 days)

## Objective

Build the site's structural chrome: Header, Footer, MobileMenu, and the page transition system (TransitionOverlay + TransitionLink). After this phase, the site has a navigable shell with animated transitions between pages.

## Done When

You can navigate between stub pages (Home, Work, About, Contact) with full overlay wipe transitions, the nav works on desktop and mobile, and the header blur activates on scroll.

## Dependencies from Prior Phases

- **Phase A:** All foundation files must exist — design tokens, fonts, GSAP setup, Zustand store, Providers, root layout.

## Components to Build

### `Header` — `src/components/layout/Header.tsx`

- **Type:** Client Component (`'use client'`)
- **Props:**
  ```typescript
  interface HeaderProps {
    className?: string;
  }
  ```
- **Behavior:** Fixed position. Wordmark "BAMYAN" in Instrument Serif (left) + text links "Work", "About", "Contact" (right) in Satoshi. Background: transparent at top → `backdrop-filter: blur(12px)` + `background: rgba(12, 15, 20, 0.6)` after scrolling past 100vh. Nav links have underline draw-on animation from left on hover (2px thickness, `var(--color-primary)`, 4px offset below text).
- **Animation:** ScrollTrigger detects scroll position. Blur backdrop activates via CSS class toggle. Links use `clipPath` or `scaleX` underline animation on hover.
- **Responsive:** On `< md` (768px): wordmark left + hamburger icon right. Text links hidden. Hamburger triggers `menuOpen` in Zustand.
- **Accessibility:** `<header>` landmark, `<nav>` with `aria-label="Main navigation"`, links are `<a>` tags via Next.js `<Link>`. Hamburger has `aria-expanded`, `aria-controls="mobile-menu"`.

### `MobileMenu` — `src/components/layout/MobileMenu.tsx`

- **Type:** Client Component
- **Props:**
  ```typescript
  interface MobileMenuProps {
    // No required props — reads menuOpen from Zustand
  }
  ```
- **Behavior:** Full-screen overlay (`position: fixed; inset: 0; z-index: 50`). Dark background `var(--color-overlay)`. "BAMYAN" wordmark centered top. Menu items stacked vertically in Instrument Serif at `--text-4xl`. Social links at bottom in JetBrains Mono at `--text-sm`.
- **Animation:** Uses `navOverlayOpen` / `navOverlayClose` presets. Backdrop fades in, menu items reveal line-by-line via SplitText mask. Close reverses with faster timing.
- **Responsive:** Only renders on `< md`. Body scroll locked via Lenis `.stop()` when open.
- **Accessibility:** `id="mobile-menu"`, `role="dialog"`, `aria-modal="true"`, focus trap (first focusable on open, return focus to hamburger on close). Escape key closes.

### `Footer` — `src/components/layout/Footer.tsx`

- **Type:** Server Component (static content) wrapping a Client Component for hover animation
- **Props:**
  ```typescript
  interface FooterProps {
    className?: string;
  }
  ```
- **Behavior:** Large typographic CTA: "Let's work together" in Instrument Serif at `--text-footer-cta` (80px). On hover, text shifts color to `var(--color-primary)` and a magnetic pull effect activates. Below: email link + social icon links (Instagram, Behance, LinkedIn) horizontal row. Copyright in JetBrains Mono `--text-xs`.
- **Animation:** CTA text uses `textRevealLines` on scroll enter. Social links stagger `fadeUp`.
- **Responsive:** CTA scales to `--text-3xl` on mobile. Social icons remain horizontal.
- **Accessibility:** `<footer>` landmark. CTA is `<a href="mailto:...">`. Social links have `aria-label`.

### `TransitionOverlay` — `src/components/layout/TransitionOverlay.tsx`

- **Type:** Client Component
- **Props:**
  ```typescript
  interface TransitionOverlayProps {
    // No required props — reads from Zustand: isTransitioning, transitionType
  }
  ```
- **Behavior:** Full-screen overlay (`position: fixed; inset: 0; z-index: 100`). Background: `var(--color-overlay-solid)`. Hidden by default (`scaleX: 0`). Activated by Zustand `isTransitioning`.
  - `transitionType === 'wipe'`: overlay scales from left → full → reveals from right.
  - `transitionType === 'morph'`: overlay transparent, GSAP Flip handles the visual.
- **Animation:** Uses `pageTransitionLeave` and `pageTransitionEnter` presets.

### `TransitionLink` — `src/components/layout/TransitionLink.tsx`

- **Type:** Client Component
- **Props:**
  ```typescript
  interface TransitionLinkProps {
    href: string;
    children: React.ReactNode;
    transitionType?: "wipe" | "morph";
    flipId?: string;
    className?: string;
    onClick?: () => void;
    prefetch?: boolean;
  }
  ```
- **Behavior:** Wraps Next.js `<Link>`. Intercepts click: sets Zustand `isTransitioning: true` + `transitionType`, runs leave animation via GSAP timeline, calls `router.push()` after animation completes, new page runs enter animation on mount.
  - For morph: captures `Flip.getState()` of source element, stores in Zustand `morphSource`.
- **Prefetch:** `<Link prefetch>` enabled by default. On hover, additionally triggers `router.prefetch()`.

### `ScrollIndicator` — `src/components/layout/ScrollIndicator.tsx`

- **Type:** Client Component
- **Props:**
  ```typescript
  interface ScrollIndicatorProps {
    className?: string;
  }
  ```
- **Behavior:** Positioned absolute bottom-right of hero. Vertical "Scroll" text in JetBrains Mono `--text-xs` rotated 90°, with animated line below.
- **Animation:** Uses `scrollIndicatorPulse` preset. Fades out when user scrolls past hero (ScrollTrigger at 50vh).
- **Responsive:** Hidden on mobile (`< md`).
- **Accessibility:** `aria-hidden="true"`.

### `BackToTop` — `src/components/layout/BackToTop.tsx`

- **Type:** Client Component
- **Behavior:** Small circular button, fixed bottom-right, appears after scrolling past 100vh. Click triggers `lenis.scrollTo(0)`.
- **Animation:** Fade in/out via ScrollTrigger.
- **Accessibility:** `<button aria-label="Back to top">`, keyboard focusable.

## Relevant Animation Presets

### `navOverlayOpen`
```typescript
export const navOverlayOpen = {
  backdrop: { from: { autoAlpha: 0 }, to: { autoAlpha: 1, duration: 0.4, ease: easings.smooth } },
  menuItems: {
    splitConfig: { type: "lines", mask: "lines" },
    from: { yPercent: 100 },
    to: { yPercent: 0, duration: 0.8, ease: easings.smooth, stagger: 0.08 },
    delay: 0.2,
  },
};
```

### `navOverlayClose`
```typescript
export const navOverlayClose = {
  menuItems: {
    to: { yPercent: -100, duration: 0.5, ease: easings.smoothInOut, stagger: 0.04 },
  },
  backdrop: { to: { autoAlpha: 0, duration: 0.3, ease: easings.smoothInOut, delay: 0.2 } },
};
```

### `scrollIndicatorPulse`
```typescript
export const scrollIndicatorPulse = {
  line: {
    from: { scaleY: 0, transformOrigin: "top center" },
    to: { scaleY: 1, duration: 0.8, ease: easings.smoothInOut, repeat: -1, yoyo: true, repeatDelay: 0.5 },
  },
  text: {
    from: { autoAlpha: 0 },
    to: { autoAlpha: 1, duration: 0.6, ease: easings.smooth, delay: 0.5 },
  },
};
```

### `pageTransitionLeave`
```typescript
export const pageTransitionLeave = {
  overlay: {
    from: { scaleX: 0, transformOrigin: "left center" },
    to: { scaleX: 1, duration: 0.5, ease: easings.smoothInOut },
  },
  content: {
    to: { autoAlpha: 0, y: -20, duration: 0.3, ease: easings.smooth },
  },
  totalDuration: 0.5,
};
```

### `pageTransitionEnter`
```typescript
export const pageTransitionEnter = {
  overlay: {
    from: { scaleX: 1, transformOrigin: "right center" },
    to: { scaleX: 0, duration: 0.5, ease: easings.smoothInOut, delay: 0.1 },
  },
  totalDuration: 0.6,
};
```

## Routing & Transition Map

| Route A | Route B | Transition | Timing | Special Behavior |
|---------|---------|------------|--------|-----------------|
| `/` | `/work` | Overlay wipe right | 1.0s | Standard |
| `/` | `/about` | Overlay wipe right | 1.0s | Standard |
| `/` | `/contact` | Overlay wipe right | 1.0s | Standard |
| `/work` | `/work/[slug]` | Shared element morph | 0.9s | Card → album hero via GSAP Flip |
| `/work/[slug]` | `/work` | Overlay wipe left | 0.9s | Reverse = "going back" |
| `/work/[slug]` | `/work/[other]` | Overlay wipe right | 1.0s | Via next/prev nav |
| Any (back) | Previous | Overlay wipe left | 0.9s | `popstate` event |
| Any (forward) | Next | Overlay wipe right | 0.9s | Standard |

### Transition Architecture

```
Click TransitionLink
  → Set Zustand: isTransitioning: true, transitionType
  → If morph: Flip.getState(sourceElement) → store in morphSource
  → Run pageTransitionLeave GSAP timeline
  → On timeline complete: router.push(href)
  → New page mounts, template.tsx detects isTransitioning
  → If wipe: run pageTransitionEnter (overlay reveals from right)
  → If morph: Flip.from(morphSource) on hero image
  → On enter complete: set isTransitioning: false
```

### Browser Back/Forward

- Listen for `popstate` in TransitionOverlay
- On `popstate`: run leave with leftward direction, let browser handle route
- Edge case: if `isTransitioning` already true during popstate, skip animation

## Page/Routing Context

Create stub pages for all routes so transitions can be tested:
- `src/app/page.tsx` — Home stub
- `src/app/work/page.tsx` — Work stub
- `src/app/about/page.tsx` — About stub
- `src/app/contact/page.tsx` — Contact stub

Each stub should have enough height for scroll testing and display the page name.
