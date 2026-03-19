# Phase F — About & Contact Pages (2–3 days)

## Objective

Build the About page (portrait, bio, approach, services grid, social links) and Contact page (validated form with Server Action email delivery via Resend). Style all form UI components to match the design system.

## Done When

All pages complete with content and animations. Contact form validates, submits, delivers email, and shows success state.

## Dependencies from Prior Phases

- **Phase A:** Design tokens, fonts, Sanity (about singleton), Zod, Resend.
- **Phase B:** Header, Footer, TransitionLink.
- **Phase C:** FadeIn, TextReveal, ImageReveal, ParallaxSection, MagneticButton, staggerGrid.

## Components to Build

### `Button` — `src/components/ui/Button.tsx`

- **Type:** Client Component
- **Props:**
  ```typescript
  interface ButtonProps {
    children: React.ReactNode;
    variant?: "primary" | "outline" | "ghost"; // default: "primary"
    size?: "sm" | "md" | "lg";                 // default: "md"
    magnetic?: boolean;                         // default: true
    href?: string;                              // renders as <a> if provided
    className?: string;
    [key: string]: any;
  }
  ```
- **Behavior:**
  - `primary`: Background `var(--color-primary)`, text `var(--color-background)`.
  - `outline`: Border `1px solid var(--color-border)`, transparent bg.
  - `ghost`: No border, no background.
- **Hover:** Border animation — dashed border rotates (for outline) or solid border draws around element. Magnetic pull when `magnetic: true`.
- **Accessibility:** Renders `<button>` by default, `<a>` if `href`. Focusable, `focus-visible` ring uses `var(--color-primary)`.

### `Input` — `src/components/ui/Input.tsx`

- **Type:** Client Component
- **Props:**
  ```typescript
  interface InputProps extends React.InputHTMLAttributes<HTMLInputElement> {
    label: string;
    error?: string;
  }
  ```
- **Behavior:** Bottom-border only (`border-bottom: 1px solid var(--color-border)`). On focus: animated underline draws from left in `var(--color-primary)` via `scaleX(0→1)` CSS transition. Label above in Satoshi `--text-sm`. Error below in red-ish accent.
- **Accessibility:** `<label>` via `htmlFor`/`id`. Error via `aria-describedby`. `aria-invalid="true"` when error.

### `Textarea` — `src/components/ui/Textarea.tsx`

- **Type:** Client Component
- **Props:**
  ```typescript
  interface TextareaProps extends React.TextareaHTMLAttributes<HTMLTextAreaElement> {
    label: string;
    error?: string;
  }
  ```
- **Behavior:** Same styling as Input — bottom-border with animated focus underline.
- **Accessibility:** Same pattern as Input.

### `Select` — `src/components/ui/Select.tsx`

- **Type:** Client Component
- **Behavior:** Select dropdown for photography type, styled to match the bottom-border design system aesthetic.

### `ContactForm` — `src/components/ui/ContactForm.tsx`

- **Type:** Client Component
- **Props:**
  ```typescript
  interface ContactFormProps {
    // No required props — uses Server Action directly
  }
  ```
- **Fields:**
  - name (text)
  - email (email)
  - photographyType (select: Personal / Event / Sports / Solo)
  - preferredDate (date)
  - message (textarea)
- **Validation:** Zod schema validates client-side on blur and on submit. Server Action revalidates with same schema.
- **Submission:** React Server Action → Resend API.
  - Success: form content fades out, "Thank you" message fades in with `fadeUp`.
  - Error: inline error messages per field + toast at top.
- **Animation:** Form fields stagger entrance via `fadeUp` with 0.08s stagger. Focus underline draws on.

### Server Action — `src/app/contact/action.ts`

```typescript
// Server Action: validate with Zod, send via Resend
// Zod schema: name (string, min 2), email (email), photographyType (enum), preferredDate (optional date), message (string, min 10)
// Resend: send to CONTACT_EMAIL env var
// Return: { success: boolean, error?: string }
```

## Page Blueprints

### About Page (`/about`) — `src/app/about/page.tsx`

**Server Component** with Client animation wrappers.

**Data:**
```groq
*[_type == "about"][0] {
  bio,
  portrait,
  "portraitBlur": portrait.asset->metadata.lqip,
  approach,
  services[],
  socialLinks[]
}
```

**SEO:**
- Title: `"About — Bamyan Storyworks"`
- JSON-LD: `Person` schema with `jobTitle: "Photographer"`

**Scroll Timeline:**

1. **Hero Section** (0vh–80vh)
   - Portrait image (large, ~60% viewport width) with `imageReveal`
   - Name + title in Instrument Serif, `textRevealLines`

2. **Bio** (80vh–180vh)
   - Full bio text in Satoshi `--text-lg` to `--text-xl`
   - `TextReveal variant="lines"` on scroll enter

3. **Approach / Philosophy** (180vh–260vh)
   - Short statement about photographic approach
   - Could use `NarrativeSection` (word highlight scrub) for emphasis

4. **Services / What I Shoot** (260vh–340vh)
   - Four categories (Personal, Event, Sports, Solo) with brief descriptions
   - 2×2 grid, each card fades in via `staggerGrid`
   - Each category has a representative thumbnail image

5. **Social Links** (340vh–380vh)
   - Links to Instagram, Behance, LinkedIn
   - Staggered `fadeUp` entrance

### Contact Page (`/contact`) — `src/app/contact/page.tsx`

**Server Component** wrapping Client Component form.

**Data:** Minimal — form is client-side, submission via Server Action.

**SEO:**
- Title: `"Contact — Bamyan Storyworks"`
- JSON-LD: `ContactPage`

**Scroll Timeline:**

1. **Heading** (0vh–30vh)
   - "Let's create something" in Instrument Serif `--text-5xl`, `textRevealLines`
   - Short description in Satoshi below

2. **Contact Form** (30vh–end)
   - `ContactForm` with fields staggering in via `fadeUp` (stagger 0.08s)
   - Bottom-border inputs with animated underline on focus
   - Select dropdown styled to match
   - Submit button with `MagneticButton` + border animation on hover
   - Success: form fades out, "Thank you — I'll be in touch" fades in
   - Right column on desktop: email address, social links, optional location text
