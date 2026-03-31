# WCAG 2.2 AA Accessibility Remediation — Design Spec

## Context

Bahar Studio's portfolio has strong accessibility foundations (reduced motion support, skip link, focus trap, aria-live regions, aria-current="page"). This spec addresses the remaining WCAG 2.2 AA violations found during audit.

## Scope

Critical-fix pass: only confirmed, real-world violations. No speculative fixes for color combinations that don't occur in the codebase.

## Color Contrast Audit Summary

| Combination | Ratio | AA Normal | AA Large | Status |
|---|---|---|---|---|
| Text #e8dfd4 on bg #242820 | 11.4:1 | PASS | PASS | No action |
| Header muted #b4bca6 on header bg | 7.9:1 | PASS | PASS | No action |
| Muted #8a9878 on bg #242820 | 4.9:1 | PASS | PASS | No action |
| Primary #c97b2a on bg #242820 | 4.5:1 | PASS (borderline) | PASS | No action |
| Muted #8a9878 on surface #303528 | 4.2:1 | FAIL | PASS | Not used in codebase |
| Primary-muted #a86520 on bg #242820 | 3.2:1 | FAIL | PASS | Decorative/hover only |
| Primary-muted #a86520 on surface #303528 | 2.7:1 | FAIL | FAIL | Decorative 1px divider only |

**Decision:** No color token changes. Primary-muted contrast failures are either decorative (dividers, gradients) or transient hover states. The button hover (dark text on #a86520 = 3.24:1) is accepted as a known limitation — resting state passes 4.5:1, hover is brief.

## Fix 1: Contact Form Focus Indicator

**File:** `src/components/ui/CinematicContactForm.tsx`
**WCAG:** 2.4.7 Focus Visible

**Problem:** `inputClass` uses `focus:border-primary/40 focus:outline-none focus:ring-0` — border-only focus at 40% opacity is insufficient.

**Fix:** Replace focus styles in `inputClass` with:
```
focus:outline-2 focus:outline-primary focus:outline-offset-2 focus:border-primary/40
```
Remove `focus:outline-none focus:ring-0`. Keep border change as supplemental visual cue.

## Fix 2: Contact Form Error Association

**File:** `src/components/ui/CinematicContactForm.tsx`
**WCAG:** 1.3.1 Info and Relationships, 4.1.2 Name Role Value

**Problem:** Error messages use `role="alert"` but aren't linked to inputs via `aria-describedby`. Inputs lack `aria-invalid` state.

**Fix:** For each of the 4 form fields (name, email, photographyType, message):
1. Add `id` to the error `<p>` element (e.g., `id="sender-error"`)
2. Add `aria-describedby` to the input, pointing to the error ID when error exists
3. Add `aria-invalid={!!fieldErrors.fieldName}` to the input

Affected inputs:
- `#sender` → error ID `sender-error`, field key `name`
- `#reply_to` → error ID `reply-to-error`, field key `email`
- `#photographyType` → error ID `photography-type-error`, field key `photographyType`
- `#message` → error ID `message-error`, field key `message`

## Fix 3: Mobile Menu Button ARIA

**File:** `src/components/layout/Header.tsx`
**WCAG:** 4.1.2 Name Role Value

**Problem:** Hamburger button (line ~208) has `aria-label="Open menu"` but missing `aria-expanded` and `aria-controls`.

**Fix:** Header already imports `useUIStore` (line 13) but only calls `getState()` imperatively. Add a reactive subscription `const menuOpen = useUIStore((s) => s.menuOpen)` and add to button:
```tsx
aria-expanded={menuOpen}
aria-controls="mobile-menu"
```

The `id="mobile-menu"` already exists on the MobileMenu container (MobileMenu.tsx line 141).

## Fix 4: Background Inert When Menu Open

**File:** `src/components/layout/RouteChrome.tsx`
**WCAG:** 2.4.3 Focus Order

**Problem:** When mobile menu is open, screen readers can still navigate background content via virtual cursor. The `useFocusTrap` hook traps Tab but doesn't prevent AT from reading background.

**Fix:** In `RouteChrome.tsx` (already a client component), subscribe to `menuOpen` from `useUIStore` and wrap the page content (`children`, Footer, BackToTop) in a `<div>` with `inert` when menu is open:

```tsx
const menuOpen = useUIStore((s) => s.menuOpen);
return (
  <>
    <Header />
    <MobileMenu socialLinks={socialLinks} />
    <div inert={menuOpen || undefined}>
      {children}
      {!hideFooter && <Footer socialLinks={socialLinks} />}
      {!hideFooter && <BackToTop />}
    </div>
    <TransitionOverlay />
  </>
);
```

**Note:** `inert` is supported in all modern browsers (Chrome 102+, Firefox 112+, Safari 15.5+).

## Fix 5: Footer Navigation Landmark

**File:** `src/components/layout/Footer.tsx`
**WCAG:** 1.3.1 Info and Relationships

**Problem:** Footer link groups aren't wrapped in `<nav>` landmarks. Screen reader users can't navigate to footer navigation via landmark shortcuts.

**Fix:** Wrap page links and legal/social links in nav elements:
```tsx
<nav aria-label="Footer navigation">
  {/* PRIMARY_NAV_ITEMS + NAV_CTA links */}
</nav>
<nav aria-label="Legal and social links">
  {/* LEGAL_NAV_ITEMS + socialLinks */}
</nav>
```

## Fix 6: Video Accessible Label

**File:** `src/components/sections/FolioGallery.tsx`
**WCAG:** 1.1.1 Non-text Content

**Problem:** The `<video>` element in `VideoContent()` (line ~504) has no accessible name.

**Fix:** Add `aria-label` to the video element:
```tsx
<video aria-label="Album video presentation" ... />
```

## Already Compliant (No Changes)

- **aria-current="page"**: Implemented in Header.tsx:131, MobileMenu.tsx:187
- **Skip link**: Present in layout.tsx, target `#main-content` exists on all pages
- **prefers-reduced-motion**: All animation components (FadeIn, ImageReveal, TextReveal, MagneticButton) check `useReducedMotion()` hook; Lenis disabled when reduced motion active
- **Grain overlays**: All use `aria-hidden="true"` consistently
- **Image alt text**: SiteMedia handles decorative (aria-hidden) vs meaningful (role="img" + aria-label) correctly
- **Focus trap + Escape**: MobileMenu properly traps focus and closes on Escape
- **aria-live regions**: Contact form success/error states announced

## Files Modified

1. `src/components/ui/CinematicContactForm.tsx` — focus indicator + error association
2. `src/components/layout/Header.tsx` — menu button aria-expanded/controls
3. `src/components/layout/RouteChrome.tsx` — inert wrapper for background content
4. `src/components/layout/Footer.tsx` — nav landmarks
5. `src/components/sections/FolioGallery.tsx` — video aria-label

## Verification

1. `npm run type-check` — ensure no TypeScript errors from new attributes
2. `npm run lint` — ensure no ESLint issues
3. `npm run test:unit` — verify no regressions
4. `npm run test:e2e` — verify e2e tests pass
5. Manual keyboard testing: Tab through all pages, verify focus visibility on contact form inputs
6. Lighthouse accessibility audit on: `/`, `/work`, one album page, `/about`, `/contact`, `/process`
7. `npm run build` — production build succeeds
