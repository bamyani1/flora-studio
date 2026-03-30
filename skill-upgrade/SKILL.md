---
name: bahar-marketing
description: |
  Create on-brand flyers and Instagram content for Bahar Studio, a premium photography business. Use this skill whenever the user mentions flyer, promotional material, social media post, Instagram content, social media graphic, marketing material, promo, campaign visual, story design, or asks to create any visual marketing asset for Bahar Studio. Also triggers for requests like "make a flyer for my mini sessions", "create an Instagram post", "design a promo", "I need social media content", "make something I can hand out", or "create marketing materials." Covers flyers (HTML, print-ready), Instagram feed posts, Instagram stories, and captions with hashtags — all locked to the Bahar Studio brand identity. Use this skill even for vague requests like "I need to promote something" or "help me market this."
---

# Bahar Marketing — Flyers & Social Media Content

You are creating marketing materials for **Bahar Studio**, a premium photography studio. Everything you produce must feel like it belongs in a curated editorial magazine — dark, warm, cinematic, intentional. No generic templates. No stock-photo energy. Think Kinfolk magazine, Cereal, Apartamento — publications where restraint is the design.

## Before You Start

Read `references/brand-tokens.md` in this skill's directory. It contains the exact colors, fonts, spacing, and voice guidelines. Don't guess — use the tokens.

## What This Skill Produces

There are two categories of output, and the user might ask for one or both:

### 1. Flyers (HTML)
Single-page HTML files designed for both screen viewing and physical printing. These are promotional pieces — mini session announcements, seasonal offers, service showcases, event invitations.

### 2. Instagram Content
Visual mockups as HTML files (sized to Instagram dimensions) plus captions with hashtags. Feed posts (1080×1080 or 1080×1350), stories (1080×1920), or carousels.

---

## Design Philosophy

Bahar Studio's brand is built on restraint. The photography does the talking. Your designs should feel like a dark gallery wall with carefully placed type — not a busy advertisement.

**Guiding principles:**
- **Breathing room matters more than filling space.** Large margins, generous padding. Let elements float in dark space. When in doubt, add more whitespace.
- **Typography is the design.** With a dark background and warm cream text, elegant serif headlines (Cormorant Garamond) do most of the visual work. You rarely need decorative elements.
- **Copper is the accent, not the main event.** Use `#c97b2a` sparingly. See the Copper Accent Hierarchy section below for ranked usage.
- **The brand voice is poetic but grounded.** Write like a thoughtful artist, not a salesperson. "Moments worth keeping" not "BOOK NOW 50% OFF!!!"
- **Dark-first, always.** The background is always dark (#242820 or deeper). Never white, never light. This is a darkroom aesthetic.
- **Every design needs visual tension.** Something should feel slightly unexpected — an offset headline, a dramatic scale jump, asymmetric alignment. "Correct and centered" is not the goal. "Intentional and alive" is.

---

## Visual Hierarchy

Every piece must have a clear reading order. The viewer's eye should move through the design in this sequence:

**Level 1 — Dominant (Headline):** The largest, most impactful element on the page. This is what the eye hits first. It should occupy the most visual space through either size or dramatic positioning. Use Cormorant Garamond at large scale (72–108px for flyers).

**Level 2 — Supporting (Subline / Body):** Provides context for the headline. Noticeably smaller. Use Inter at 13–14px in muted color (#8a9878), or Cormorant Garamond italic at 18–22px for a more editorial feel. This should never compete with the headline for attention.

**Level 3 — Functional (Wordmark, Contact, QR):** Discoverable but quiet. These elements are found, not noticed first. Small (9–14px), muted, positioned at page edges. The wordmark should be 12–14px — a presence, not a shout. The headline *is* the brand expression; the wordmark is just the signature.

**The scale gap matters.** A 108px headline paired with 9px labels creates luxury tension. A 48px headline with 16px body feels "safe." Push for dramatic scale jumps between hierarchy levels — this is what separates premium design from competent design.

---

## Typographic Composition

The type scale in `brand-tokens.md` tells you sizes and weights. This section teaches you how to *compose* with type to create editorial energy.

### Scale Contrast
Pair dramatically different sizes. A massive Cormorant headline (80–108px) with tiny Inter labels (8–10px) creates the tension that makes luxury design feel luxurious. Avoid the "comfortable middle" where headline and body are close in size.

### Asymmetric Alignment
Not every headline needs to be centered or left-aligned. Techniques that create editorial energy:

**Staggered word stack** — place each word of a headline on its own line, with the second word offset/indented:
```html
<span style="display:block; font-size:108px;">Portrait</span>
<span style="display:block; font-size:108px; padding-left:1.8in;">Sessions</span>
```

**Mixed alignment** — headline left-aligned, supporting text right-aligned (or vice versa). Creates a dynamic diagonal reading line across the page.

**Baseline offset** — when two type elements sit on the same horizontal band, give them different vertical positions. The slight misalignment creates movement.

### Italic as Semantic Accent
Use italic on a single word within a headline to give the reader's eye a place to land and to add poetic inflection:
```html
<h1>Your Story, Held in <em>Light</em></h1>
<h1>The Quietest Moments Are the Ones Worth <em>Keeping</em></h1>
```
This technique is more editorial and refined than using copper color for emphasis. Use it as your first choice for headline emphasis.

### Tight Tracking on Display Type
Always apply negative letter-spacing on headlines larger than 48px. The tokens specify -0.02em to -0.04em. At large sizes (80px+), go closer to -0.03em. This tightness is essential — without it, large serif type looks loose and unfinished.

```css
.headline {
  font-family: 'Cormorant Garamond', Georgia, serif;
  font-weight: 300;
  font-size: 96px;
  line-height: 0.95;
  letter-spacing: -0.03em;
}
```

### Line Height on Display Type
For headlines above 48px, use tight line-heights (0.92–1.05). This makes multi-line headlines feel like a cohesive typographic block, not separated lines. The default 1.15 is for section heads; hero headlines need to feel compressed and intentional.

---

## Copper Accent Hierarchy

Use `#c97b2a` sparingly — maximum 2 uses per piece. Here's how to choose, ranked from most to least sophisticated:

**Tier 1 — Highest impact (prefer this):** A single italic word in a headline set in copper. This is the most editorial, most refined use. Example: "Your Story, Held in <em style="color:#c97b2a">Light</em>". One word. Maximum impact.

**Tier 2 — Strong accent:** A 4–6px copper dot, or a very thin line (1px, 40–60px long). Geometric, minimal. Works as an ornamental divider between sections.

**Tier 3 — Moderate accent:** Corner details on decorative frames. Elegant but more decorative — use on invitation-style pieces (weddings, events).

**Tier 4 — Subtle presence:** A service label or category tag (e.g., "MATERNITY & NEWBORN") in copper at 9px. Functional accent.

**Tier 5 — Atmospheric (barely visible):** A radial gradient glow in copper at 0.02–0.03 opacity behind the headline. Felt more than seen. Creates warmth.

**Never use:** Copper on body text paragraphs. Copper gradients as backgrounds. Copper borders around the entire page. Full copper buttons or large colored blocks — these feel commercial, not editorial.

---

## Texture & Depth

Flat dark backgrounds look digital and cheap. The best dark-themed designs use subtle layering to create physical depth — like looking at an actual darkroom print. Apply these techniques to every piece.

### Film Grain Overlay
Add to every flyer and Instagram piece. Barely visible, but it transforms flat digital color into something tactile:

```css
.flyer::before {
  content: '';
  position: absolute;
  inset: 0;
  opacity: 0.03; /* 0.02–0.04 range — barely visible */
  background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 256 256' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noise'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noise)'/%3E%3C/svg%3E");
  background-size: 128px 128px;
  pointer-events: none;
  z-index: 1;
}
```

### Radial Vignette
Darkens edges to draw focus to center content. Use on photo placeholder areas and full-page designs:

```css
.flyer::after {
  content: '';
  position: absolute;
  inset: 0;
  background: radial-gradient(ellipse at 50% 45%, transparent 35%, rgba(16, 19, 12, 0.35) 100%);
  pointer-events: none;
  z-index: 1;
}
```

### Warm Glow
A nearly invisible copper radial gradient behind the headline. Creates warmth without visible color:

```css
.warm-glow {
  position: absolute;
  top: 35%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 6in;
  height: 6in;
  background: radial-gradient(ellipse, rgba(201, 123, 42, 0.03) 0%, transparent 70%);
  pointer-events: none;
}
```

### Layered Backgrounds
Instead of a single flat color, use multi-stop gradients for organic depth:

```css
.photo-area {
  background: linear-gradient(160deg, #1c2018 0%, #2a3024 30%, #303528 60%, #242820 100%);
}
```

---

## Flyer Construction

### HTML Structure

Every flyer is a single self-contained HTML file. All CSS is inline or in a `<style>` block. No external dependencies except Google Fonts.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>[Flyer Title] — Bahar Studio</title>
  <link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300;0,400;0,500;0,600;0,700;1,300;1,400&family=Inter:wght@300;400;500&display=swap" rel="stylesheet">
  <style>
    /* Reset and page setup */
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

    @page { size: 8.5in 11in; margin: 0; }
    @media print {
      body { -webkit-print-color-adjust: exact; print-color-adjust: exact; }
      .flyer { break-after: page; }
      .screen-only { display: none; }
    }

    body {
      margin: 0; padding: 0;
      display: flex; justify-content: center; align-items: center;
      min-height: 100vh; background: #111;
    }

    .flyer {
      width: 8.5in;
      height: 11in;
      background: #242820;
      color: #e8dfd4;
      font-family: 'Inter', system-ui, -apple-system, sans-serif;
      position: relative;
      overflow: hidden;
    }
  </style>
</head>
<body>
  <div class="flyer">
    <!-- Always add grain overlay and vignette via ::before / ::after -->
    <!-- Content here -->
  </div>
</body>
</html>
```

**Important:** Always include the italic variants in the Google Fonts URL (`ital,wght@0,300;...;1,300;1,400`). Italic is used frequently for headline emphasis.

### Flyer Dimensions

For physical handouts, design at **8.5 × 11 inches** (US Letter) or **A5 (5.83 × 8.27 inches)** for half-sheets.

### Layout Patterns

Choose one of these layouts. Each includes specific CSS guidance — don't just place elements mechanically; follow the compositional intent.

**1. Asymmetric Editorial (best for single-service promos — portraits, headshots)**

The headline is the architecture. Words are stacked on separate lines with one word offset to create a diagonal reading line. A vertical accent line creates tension on the opposite side. The bottom third holds contact/QR as quiet functional elements.

```css
.flyer {
  display: flex;
  flex-direction: column;
}
.hero {
  flex: 1;
  display: flex;
  flex-direction: column;
  justify-content: center;
  padding: 0 0.85in;
}
/* Stack headline words with offset */
.headline-word { display: block; font-size: 108px; line-height: 0.92; }
.headline-word.offset { padding-left: 1.8in; } /* Creates diagonal energy */

/* Vertical accent — placed on opposite side to balance the offset */
.vertical-accent {
  position: absolute;
  right: 1.2in;
  top: 2.4in;
  width: 1px;
  height: 2in;
  background: linear-gradient(to bottom, transparent, #3a4034 20%, #3a4034 80%, transparent);
}
```

**Why this works:** The offset creates visual movement. The vertical line counterbalances it. The generous flex-grow on the hero means the headline floats in dark space — it's not "placed at the top" or "centered," it naturally settles into the golden zone.

**2. Framed Invitation (best for weddings, events, formal occasions)**

A thin decorative border with copper corner accents frames the entire page. Content is centered vertically within the frame. An ornamental divider (line-dot-line) separates headline from body. This layout evokes a wedding invitation.

```css
/* Outer frame */
.frame {
  position: absolute;
  inset: 0.65in;
  border: 1px solid #3a4034;
  pointer-events: none;
}
/* Copper corner accents — only on two diagonal corners for asymmetry */
.frame::before {
  content: '';
  position: absolute;
  top: -1px; left: -1px;
  width: 24px; height: 24px;
  border-top: 1px solid #c97b2a;
  border-left: 1px solid #c97b2a;
}
.frame::after {
  content: '';
  position: absolute;
  bottom: -1px; right: -1px;
  width: 24px; height: 24px;
  border-bottom: 1px solid #c97b2a;
  border-right: 1px solid #c97b2a;
}
```

**Why this works:** The frame creates a sense of occasion and formality. Copper corners at only two diagonal positions (top-left, bottom-right) create subtle asymmetry — it's not a perfect box, it's a gesture.

**3. Hero Image + Editorial Bottom (best for family, lifestyle, session types with photos)**

Top 55–60% is a photo area with a gradient overlay that bleeds the headline into the image. The headline sits at the very bottom of the photo area, emerging from darkness. Below: a clean info strip with body text and QR.

```css
.flyer {
  display: grid;
  grid-template-rows: 1fr auto;
}
.photo-section {
  position: relative;
  background: linear-gradient(160deg, #1c2018 0%, #2a3024 30%, #303528 60%, #242820 100%);
}
/* Gradient overlay that bleeds headline into photo */
.photo-overlay {
  position: absolute;
  bottom: 0; left: 0; right: 0;
  padding: 60px 0.85in 0;
  background: linear-gradient(to top, #242820 0%, rgba(36,40,32,0.95) 40%, transparent 100%);
}
.info-section {
  padding: 32px 0.85in 0.75in;
}
```

**Why this works:** The headline emerging from the photo gradient creates a cinematic transition — the text feels like it's part of the image, not floating above it. The info section below is clearly functional/secondary.

**4. Ultra-Minimal Statement (best for maternity, newborn, brand awareness)**

Maximum restraint. The deepest background (#161a12). A single centered statement fills the middle third. A warm glow (nearly invisible copper radial) creates subtle warmth. Top: wordmark. Bottom: QR + contacts. The middle 60% is almost entirely type and dark space.

```css
.flyer {
  background: #161a12; /* Deepest background */
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-between;
  padding: 1in 1.2in;
}
.center {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
  gap: 0;
}
/* Service label in copper — tiny, uppercase, above the headline */
.service-label {
  font-size: 9px;
  letter-spacing: 0.2em;
  text-transform: uppercase;
  color: #c97b2a;
  margin-bottom: 48px;
}
/* Vertical accent line between headline and body */
.accent-line {
  width: 1px;
  height: 48px;
  background: linear-gradient(to bottom, transparent, #3a4034, transparent);
  margin: 40px 0;
}
```

**Why this works:** On the deepest background, everything feels precious and intimate. The huge padding (1.2in sides) means the text is surrounded by darkness. The vertical accent line between headline and body is architectural — it creates a pause.

**5. Brand Showcase (best for general handouts listing multiple services)**

Large wordmark at top (36px — bigger than usual because this is the brand piece). Below: a poetic statement as the headline. Below that: a service list using dash-prefixed items. Bottom: tagline + contact strip + QR. A geometric accent (rotated diamond with copper dot center) adds a subtle decorative element.

```css
.wordmark-large {
  font-family: 'Cormorant Garamond', Georgia, serif;
  font-size: 36px;
  letter-spacing: 0.08em; /* Slightly tighter than standard wordmark */
  text-transform: uppercase;
}
/* Service list with dash leaders */
.service-item {
  display: flex;
  align-items: center;
  gap: 16px;
}
.service-dash {
  width: 16px;
  height: 1px;
  background: #3a4034;
}
.service-name {
  font-family: 'Inter', sans-serif;
  font-weight: 300;
  font-size: 13px;
  color: #8a9878;
}
/* Geometric diamond accent */
.geo-accent {
  position: absolute;
  top: 1.6in; right: 0.85in;
  width: 56px; height: 56px;
  border: 1px solid #3a4034;
  transform: rotate(45deg);
}
.geo-accent::after {
  content: '';
  position: absolute;
  top: 50%; left: 50%;
  transform: translate(-50%, -50%);
  width: 6px; height: 6px;
  background: #c97b2a;
  border-radius: 50%;
}
```

**Why this works:** This is the "hand this to anyone" flyer. The larger wordmark establishes authority. The service list is scannable but not a bulleted menu — the dashes feel curated. The geometric accent adds visual interest without being decorative clutter.

---

## Photo Placeholders

Since we can't embed actual client photos in HTML, the placeholder strategy must look intentional — not "missing."

**Strategy 1 — Skip the photo entirely (preferred for typography-forward and minimal layouts):** Don't show a placeholder. Let the type carry the design. A flyer with no photo area that's entirely type-on-dark can look more premium than one with a placeholder.

**Strategy 2 — Cinematic dark gradient (for hero-image layouts):** Use a rich multi-stop gradient with vignetting and grain that feels like an underexposed photograph — dark, moody, intentional. The viewer shouldn't think "there should be a photo here." They should think "this is atmospheric."

```css
.photo-area {
  background: linear-gradient(160deg, #1c2018 0%, #2a3024 30%, #303528 60%, #242820 100%);
  position: relative;
}
/* Grain texture */
.photo-area::before {
  content: '';
  position: absolute;
  inset: 0;
  opacity: 0.04;
  background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 200 200' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.85' numOctaves='3' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)'/%3E%3C/svg%3E");
  pointer-events: none;
}
/* Vignette */
.photo-area::after {
  content: '';
  position: absolute;
  inset: 0;
  background: radial-gradient(ellipse at 50% 40%, transparent 30%, rgba(36, 40, 32, 0.6) 100%);
  pointer-events: none;
}
```

**Strategy 3 — Geometric composition (for service cards or split layouts):** Use thin lines, circles, or rectangles in border color (#3a4034) as abstract compositional elements where photos would go. These look intentional, not placeholder.

**Never do this:** Never display a "Your Image Here" text label on a flyer the user might print and hand out. The label would literally print on the handout. Instead, include an HTML comment (`<!-- Replace .photo-area background with: background-image: url('your-photo.jpg'); background-size: cover; -->`) and make the design look complete without a photo.

---

## QR Codes

QR codes on handout flyers link directly to the booking page. They should feel like a natural part of the design — functional but styled.

### Generation
Use the qrserver.com API for reliable inline QR generation:
```html
<img src="https://api.qrserver.com/v1/create-qr-code/?size=200x200&data=https://baharstudio.com/book&format=png&qzone=1"
     alt="Scan to book a session" />
```
Change the `data=` parameter to match the actual booking URL.

### Styling

Always wrap the QR image in a styled container:

```css
.qr-wrapper {
  width: 84px;   /* 76–96px range. Smaller = more refined. Never exceed 100px. */
  height: 84px;
  padding: 6px;  /* Inner padding so the QR doesn't touch the edge */
  background: #e8dfd4; /* Warm cream container */
  border-radius: 3px;  /* Subtle rounding, never fully rounded */
}
.qr-wrapper img {
  width: 100%;
  height: 100%;
  display: block;
}
```

### Label
Below the QR, add a small label:
```css
.qr-label {
  font-family: 'Inter', sans-serif;
  font-weight: 400;
  font-size: 8px;          /* 8–9px, never larger */
  letter-spacing: 0.12em;
  text-transform: uppercase;
  color: #8a9878;           /* Muted color */
  text-align: center;
}
```

Label text options: "Scan to book" · "Book your session" · "Reserve yours" · "Scan to begin your story" (for wedding flyers)

### Placement Rules
- **Always bottom of the page** — bottom-right or bottom-center. Never at the top or middle. The QR is functional, not a focal point.
- **Group with contact info** — the QR, its label, and the contact details (website, Instagram) should form a single visual cluster.
- **Minimum 0.8in for print** — the QR wrapper must be at least 0.8 inches wide for reliable scanning from a physical handout.
- **Print considerations** — the warm cream wrapper (#e8dfd4) provides enough contrast against the dark background for scanning. Do not use a dark/inverted QR code — standard dark-modules-on-light-background is more reliable for scanners.

---

## Collection Coherence

When creating multiple pieces (e.g., a set of 5 flyers), they should feel like a curated collection — obviously from the same studio, but each visually distinct.

### Shared DNA (keep consistent across all pieces):
- **Outer margin:** 0.85in on all flyers
- **Wordmark treatment:** Cormorant Garamond, 12–14px, 0.12em letter-spacing, uppercase — same on every piece
- **Contact strip format:** Same layout for baharstudio.com and @baharstudio on every piece (either stacked or inline with separator)
- **QR wrapper style:** Same size, padding, border-radius across the set
- **Film grain overlay:** Same opacity and pattern on every piece

### Deliberate Variation (change between pieces):
- **Layout archetype:** Never use the same layout pattern for adjacent pieces. If flyer 1 is asymmetric editorial, flyer 2 should be framed or minimal — not another asymmetric variation.
- **Background depth:** Vary the background shade across pieces. One might use #161a12 (deepest), another #242820 (standard), another #1c2018 (surface-lowest). This creates subtle visual variety while staying in-palette.
- **Headline treatment:** One flyer might lead with a massive word stack, another with a single centered line, another with a long poetic sentence. Variety prevents the set from feeling like a single template applied multiple times.
- **Copper usage type:** If flyer 1 uses copper on a headline word, flyer 2 should use a copper dot/line, flyer 3 should use copper corner accents. Don't repeat the same copper treatment.

---

## Seasonal & Contextual Adaptation

The brand voice and visual tone can shift subtly for different seasons and contexts while staying on-brand.

### Seasonal Copy Tone
- **Spring:** "light," "bloom," "golden hour," "open," "new" — the language of emergence
- **Summer:** "warmth," "long light," "golden," "evening" — relaxed, abundant
- **Autumn:** "golden," "harvest," "amber," "soft" — warm, nostalgic
- **Winter:** "quiet," "intimate," "still," "glow" — hushed, precious

### Event-Specific Adaptations
- **Mini sessions:** Use "limited" or "a few dates remain" language. Frame as exclusive access, not a sale.
- **Studio openings / events:** Shift to "invitation" tone — "We'd love for you to join us" rather than "Come check out our studio."
- **Holiday / gift sessions:** Focus on the giving angle — "A portrait is a gift of attention."

### Visual Adaptation
The color palette doesn't change with seasons — the dark/cream/copper system is year-round. But you can subtly shift *which* background depths you reach for: winter pieces might favor the deepest backgrounds (#161a12, #10130c) while spring pieces might use the slightly lighter surface (#303528) for a touch more warmth.

---

## Instagram Content Construction

### Dimensions

| Format | CSS Size | Use |
|--------|----------|-----|
| Feed Square | `width: 1080px; height: 1080px` | Standard posts |
| Feed Vertical | `width: 1080px; height: 1350px` | Better engagement, more space |
| Story | `width: 1080px; height: 1920px` | Full-screen mobile |

### Instagram Design Rules

The Instagram pieces follow the same brand principles but adapted for mobile viewing:

- **Bigger type.** Headlines should be 64–96px on feed posts, 80–120px on stories. Body text minimum 24px. People scroll fast — your text needs to read at a glance.
- **Even more whitespace.** On a phone screen, breathing room is everything. Don't fill the canvas.
- **One message per post.** A feed post conveys one idea. Don't cram an entire promo onto a square.
- **Safe zones.** On stories, keep text within the center 1080×1420px area to avoid being covered by Instagram's UI (username top, reply bar bottom).
- **Apply the same texture techniques.** Grain overlay, vignette, and warm glow apply to Instagram content just as they do to flyers.

### Instagram HTML Template

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300;0,400;0,500;0,600;1,300;1,400&family=Inter:wght@300;400;500&display=swap" rel="stylesheet">
  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    body { margin: 0; padding: 0; display: flex; justify-content: center; align-items: center; min-height: 100vh; background: #111; }
    .post {
      width: 1080px;
      height: 1080px; /* or 1350px for vertical, 1920px for story */
      background: #242820;
      color: #e8dfd4;
      font-family: 'Inter', system-ui, sans-serif;
      position: relative;
      overflow: hidden;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      padding: 80px;
    }
  </style>
</head>
<body>
  <div class="post">
    <!-- Content here -->
  </div>
</body>
</html>
```

### Instagram Post Types

**Promotional Post** (announcing a special, mini sessions, seasonal offer):
- Large serif headline with the offer, using italic accent on one key word
- 1-2 lines of details in Inter, muted color
- Date/booking info
- Copper accent on key detail (prefer a headline word or small ornament)

**Portfolio Teaser** (showcasing work):
- Mostly photo placeholder area with cinematic gradient
- Small text overlay: project name, category
- Minimal — let the atmosphere breathe

**Quote / Brand Post** (building brand awareness):
- Centered Cormorant Garamond quote with tight line-height
- Attribution "— Bahar Studio" below in muted Inter
- Very minimal, very elegant
- Apply warm glow behind the quote

**Announcement** (new service, availability, event):
- Bold headline with dramatic scale
- Key details (date, location, what) in muted body
- CTA line

---

## Caption Writing

Every Instagram visual should come with a ready-to-post caption. Write captions that match the Bahar Studio voice: intentional, warm, never salesy.

### Caption Structure

```
[Hook — first 125 characters visible before "...more"]

[Body — 2-4 sentences of story, context, or value]

[CTA — one clear action, phrased warmly]

[3-5 hashtags]
```

### Voice Guidelines for Captions

- Write in first person plural ("we") or second person ("your")
- Use present tense for immediacy
- Short sentences. Intentional punctuation. Let lines breathe.
- No exclamation marks in excess (one per caption maximum)
- No ALL CAPS headlines in captions
- Avoid generic phrases like "Don't miss out!" or "Limited time only!"
- Instead: "A few spring sessions remain." or "We're opening our calendar for June."

### Hashtag Strategy

Use 3-5 hashtags per post, mixing:
- 1 branded: `#BaharStudio` or `#PhotographyWorthKeeping`
- 1-2 niche: `#IntentionalPhotography`, `#PortraitSession`, `#CinematicPortraits`
- 1-2 local/contextual: location-based or seasonal tags

Place hashtags at the end of the caption, separated by a line break.

### Example Captions by Post Type

**Promotional:**
```
Spring portrait sessions are here.

We're opening a handful of dates in April for outdoor sessions —
golden hour light, blooming backgrounds, and the kind of patience
that lets your real expressions surface.

Spots are limited. Link in bio to reserve yours.

#BaharStudio #SpringPortraits #PortraitPhotography
```

**Portfolio:**
```
Some frames you feel before you see them.

This one from Mara & James' engagement session — the way afternoon
light caught through the trees, the quiet between two people who
forgot the camera was there. That's the moment we wait for.

#BaharStudio #EngagementPhotography #IntentionalPhotography
```

**Brand:**
```
Photography with intention.

Every session starts with a conversation, not a shot list.
We want to understand your story before we pick up the camera.
That's how the best frames happen — not by accident, but by attention.

#BaharStudio #PhotographyWorthKeeping
```

---

## Required Elements on Every Flyer

1. **BAHAR STUDIO** wordmark (Cormorant Garamond, 400 weight, 12–14px, letter-spacing: 0.12em, uppercase) — quiet, at a page edge
2. **Headline** — the main message (Cormorant Garamond, large, tight line-height, negative letter-spacing)
3. **Supporting copy** — 1-3 short sentences (Inter, 300 weight, muted color #8a9878)
4. **Call to action** — what should the reader do? Phrased warmly, not urgently.
5. **Contact info** — website (baharstudio.com), Instagram (@baharstudio)
6. **Copper accent** — 1-2 uses, following the Copper Accent Hierarchy
7. **Film grain overlay** — on every piece, 0.02–0.04 opacity
8. **QR code** (if requested) — styled per the QR Codes section

---

## Output Checklist

Before delivering any piece, verify:

- [ ] Background is dark (#242820 or darker variant) — never white
- [ ] "BAHAR STUDIO" wordmark uses Cormorant Garamond, 0.12em letter-spacing, uppercase, 12–14px
- [ ] Headlines use Cormorant Garamond (300–400 weight) with negative letter-spacing and tight line-height
- [ ] Body text uses Inter (300–400 weight) in muted color (#8a9878)
- [ ] Copper accent (#c97b2a) appears 1–2 times maximum, following the hierarchy
- [ ] Text color is warm cream (#e8dfd4) not pure white
- [ ] Generous padding/margins (0.85in outer) — nothing feels cramped
- [ ] Film grain overlay applied (opacity 0.02–0.04)
- [ ] Visual hierarchy is clear: headline dominates > body supports > wordmark/contact/QR recede
- [ ] Print CSS included (for flyers): `print-color-adjust`, `break-after`, screen-only hiding
- [ ] Contact info present (for flyers): baharstudio.com, @baharstudio
- [ ] QR code properly wrapped and labeled (if requested)
- [ ] No "Your Image Here" labels that would print on physical handouts
- [ ] Caption included (for Instagram content) with 3–5 hashtags
- [ ] Brand voice check: reads as intentional and warm, not salesy
- [ ] File is self-contained HTML (no external deps except Google Fonts)
- [ ] If part of a collection: layout varies from adjacent pieces, shared DNA is consistent

---

## Delivering the Output

1. **Save files** to the user's workspace folder with clear names:
   - Flyers: `bahar-flyer-[description].html`
   - Instagram: `bahar-ig-[type]-[description].html`
   - Captions: include in a comment block at the bottom of the Instagram HTML, AND present them in your response text so the user can copy-paste

2. **Provide a computer:// link** so the user can open and view the HTML immediately

3. **Printing instructions** — always include:
   > To print: Open this file in Chrome → File → Print → check "Background graphics" → set margins to "None" → print at 100% scale. Designed for US Letter (8.5 × 11"). For best results, use heavyweight matte cardstock (80–100lb cover weight).

4. **Photo insertion instructions** — always include:
   > To add your photo: Find the `.photo-area` or `.photo-section` element in the HTML and replace its `background` CSS with:
   > `background-image: url('your-photo.jpg'); background-size: cover; background-position: center;`

5. **If the user provides a photo**, incorporate it using a relative path or base64 encoding. If no photo is available, use the styled placeholder areas that look complete without a photo.
