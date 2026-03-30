# Bahar Studio — Brand Tokens Reference

Quick-reference for every design token. Use these exact values.

---

## Colors

### Primary Palette (Darkroom Moss)

| Token | Hex | Use |
|-------|-----|-----|
| `background` | `#242820` | Page/canvas background |
| `surface` | `#303528` | Cards, panels, sections |
| `surface-elevated` | `#3a4032` | Hover states, interactive surfaces |
| `surface-lowest` | `#1c2018` | Deep contrast, borders |
| `surface-deep` | `#161a12` | Hero sections, dark emphasis |
| `surface-abyss` | `#10130c` | Ultra-dark, rare use |
| `primary` / `accent` | `#c97b2a` | Copper — CTAs, links, emphasis |
| `primary-muted` | `#a86520` | Hover copper, secondary accent |
| `text` | `#e8dfd4` | Primary text, warm cream |
| `muted` | `#8a9878` | Captions, metadata, secondary text |
| `border` | `#3a4034` | Dividers, structural lines |

### Hero Gold Variant
| Token | Hex | Use |
|-------|-----|-----|
| `hero-gold` | `#e09438` | Bright warm accent (hero sections only) |
| `hero-muted` | `#b4bca6` | Muted text in hero areas |

### Print Variant
| Token | Hex | Use |
|-------|-----|-----|
| `ivory` | `#F2EDE6` | Light backgrounds for print contexts |
| `warm-sand` | `#D4C8B8` | Secondary text on light print |

---

## Typography

### Display: Cormorant Garamond
- Google Fonts: `Cormorant+Garamond:ital,wght@0,300;0,400;0,500;0,600;0,700;1,300;1,400`
- Fallback: `"Georgia", serif`
- Character: Elegant, editorial, authoritative
- Use for: Headlines, wordmark, navigation, poetic sublines

**Important:** Always load italic variants (`1,300` and `1,400`). Italic is a core design tool for headline emphasis.

### Body: Inter
- Google Fonts: `Inter:wght@300;400;500`
- Fallback: `"system-ui", "-apple-system", sans-serif`
- Character: Clean, modern, highly readable
- Use for: Body text, details, captions, CTAs, labels

### Wordmark
```css
.wordmark {
  font-family: 'Cormorant Garamond', Georgia, serif;
  font-weight: 400;
  font-size: 13px;  /* 12–14px — quiet presence, never dominant */
  letter-spacing: 0.12em;
  text-transform: uppercase;
  color: #e8dfd4;
}
```

### Type Scale

| Element | Size | Weight | Line-Height | Font | Letter-Spacing |
|---------|------|--------|-------------|------|----------------|
| Hero headline | 80–108px | 300 | 0.92–1.0 | Cormorant | -0.03em |
| Section headline | 48–72px | 300–400 | 1.0–1.1 | Cormorant | -0.02em |
| Subheading | 28–40px | 500 | 1.2 | Cormorant | 0 |
| Poetic subline | 18–22px | 400 italic | 1.6 | Cormorant | 0 |
| Body large | 20–24px | 400 | 1.5 | Inter | 0 |
| Body | 13–14px | 300 | 1.75 | Inter | 0.01em |
| Caption | 12–14px | 400 | 1.5 | Inter | 0 |
| Label/meta | 8–10px | 400–500 | 1.4 | Inter | 0.12–0.16em |
| Service label | 9px | 500 | 1.4 | Inter | 0.2em |

### Letter Spacing

| Context | Value |
|---------|-------|
| Wordmark | `0.12em` |
| Uppercase labels (8–10px) | `0.12–0.2em` |
| Headlines (48px+) | `-0.02em` to `-0.03em` |
| Hero headlines (80px+) | `-0.03em` to `-0.04em` |
| Body text | `0` to `0.01em` |

### Typographic Composition Rules

**Scale contrast creates luxury.** Pair a 108px headline with 9px labels — not 48px with 16px. The dramatic gap between hierarchy levels is what makes the design feel premium.

**Tight line-heights on display type.** Headlines above 48px use 0.92–1.05 line-height. This compresses multi-line headlines into cohesive typographic blocks. Default 1.15 is only for section-level heads.

**Italic as semantic accent.** Use italic on one key word within a headline to give the reader's eye a place to land: "Your Story, Held in *Light*". This is more editorial than using color for emphasis.

**Negative letter-spacing is mandatory on display type.** Without it, large serif type looks loose and unfinished. At 80px+ always apply -0.03em.

---

## Spacing

Base unit: **4px**

| Token | Value | Px |
|-------|-------|----|
| Micro | 0.25rem | 4px |
| Small | 0.5rem | 8px |
| Base | 1rem | 16px |
| Medium | 2rem | 32px |
| Large | 4rem | 64px |
| XL | 6rem | 96px |
| Section | 8rem | 128px |

### Flyer Margins
- Outer margin: **0.85in** (standardized across all pieces for collection coherence)
- Inner padding: 48–64px
- Between sections: 32–48px

### Instagram Padding
- Edge padding: 60–100px
- Between text blocks: 24–40px

---

## Copper Accent Hierarchy

Maximum 2 uses per piece. Ranked from most to least sophisticated:

| Tier | Usage | Impact |
|------|-------|--------|
| **1** | Single italic word in headline set in copper | Highest — editorial, refined |
| **2** | 4–6px dot or thin 1px line (40–60px long) | Strong — geometric, minimal |
| **3** | Corner details on decorative frames | Moderate — elegant, formal |
| **4** | Service label/category tag at 9px | Subtle — functional accent |
| **5** | Radial glow at 0.02–0.03 opacity behind headline | Atmospheric — felt, not seen |

**Never:** Copper on body text. Copper gradients. Copper borders around page. Full copper buttons. Large colored blocks.

---

## QR Code Tokens

| Property | Value |
|----------|-------|
| Wrapper size | 76–96px (prefer 84px) |
| Wrapper padding | 6px |
| Wrapper background | `#e8dfd4` |
| Wrapper border-radius | 3px |
| Label font | Inter, 400, 8–9px |
| Label letter-spacing | 0.12em |
| Label color | `#8a9878` |
| Label text-transform | uppercase |
| Placement | Bottom of page only — bottom-right or bottom-center |
| Min print size | 0.8in wrapper width |

---

## Texture Tokens

### Film Grain Overlay
Applied to every piece. Transforms flat digital color into something tactile.

```css
/* Apply as ::before on .flyer or .post */
opacity: 0.03; /* Range: 0.02–0.04 */
background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 256 256' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noise'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noise)'/%3E%3C/svg%3E");
background-size: 128px 128px;
```

### Radial Vignette
```css
/* Apply as ::after or dedicated element */
background: radial-gradient(ellipse at 50% 45%, transparent 35%, rgba(16, 19, 12, 0.35) 100%);
```

### Warm Copper Glow
```css
/* Positioned behind headline area */
background: radial-gradient(ellipse, rgba(201, 123, 42, 0.03) 0%, transparent 70%);
/* Size: ~6in × 6in, centered on headline */
```

---

## Brand Voice Quick Reference

**Do:**
- "Photography with intention"
- "We're opening our calendar for spring"
- "A few sessions remain"
- "Moments worth keeping"
- "We wait for the light"

**Don't:**
- "BOOK NOW!!!"
- "Don't miss this amazing deal!"
- "Limited time only!"
- "We're the best photographers in town"
- "Hurry before spots fill up!"

**Tone:** Warm, deliberate, poetic but grounded, first-person plural ("we"), never desperate or loud.

### Seasonal Voice Shifts
- **Spring:** "light," "bloom," "golden hour," "open," "new"
- **Summer:** "warmth," "long light," "golden," "evening"
- **Autumn:** "golden," "harvest," "amber," "soft"
- **Winter:** "quiet," "intimate," "still," "glow"

---

## Contact Information

- Website: baharstudio.com
- Instagram: @baharstudio
- Tagline: "Photography that's worth keeping"
