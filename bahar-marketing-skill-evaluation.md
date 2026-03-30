# Bahar Marketing Skill — Evaluation Report

**Evaluator:** Claude Opus 4.6
**Date:** March 29, 2026
**Scope:** Full skill test — 5 premium handout flyers with QR booking codes
**Skill version:** Initial install

---

## Executive Summary

The Bahar Marketing skill provides a solid brand foundation and clear token system, but the design guidance it offers is too template-driven and lacks the sophistication needed to produce truly premium output. The skill reliably prevents bad decisions (no white backgrounds, no salesy copy) but doesn't push the model toward *great* design decisions. The result is outputs that feel "correct but safe" — on-brand but not elevated.

This report identifies specific areas where the skill can be improved to produce outputs that feel like they came from a high-end design studio rather than a competent template system.

---

## What the Skill Does Well

**Strong brand token system.** The `brand-tokens.md` reference file is well-structured. Exact hex values, font weights, spacing units, and voice examples give the model no room to guess. This is the strongest part of the skill.

**Clear voice guidelines.** The "Do / Don't" copy examples are effective. They prevent the most common failure mode (salesy, loud marketing language) and establish a consistent editorial tone. The caption structure and hashtag strategy sections are practical and ready to use.

**Good output checklist.** The verification checklist at the end catches the most common errors (wrong background, missing contact info, copper overuse). This is a useful safety net.

**Sensible file structure.** Self-contained HTML with Google Fonts as the only external dependency is the right call for this use case. The print CSS guidance ensures the flyers actually work as handouts.

---

## What Needs Improvement

### 1. Layout Guidance Is Too Generic

**Current state:** The skill lists 4 layout patterns (Hero Image, Typography-Forward, Split, Elegant Minimal) with bullet-point descriptions. These read like wireframe notes, not design direction.

**Problem:** When the model receives "Top 55-60%: full-bleed photo placeholder area / Bottom 40-45%: headline, details, CTA," it produces exactly that — a mechanical split with no visual tension, no asymmetry, no editorial sensibility. The layouts end up feeling like PowerPoint templates because the guidance describes *what goes where* but not *how it should feel*.

**Recommendation:** Replace the bullet-point layout descriptions with annotated CSS examples that demonstrate specific design techniques. For instance, instead of saying "Split Layout," provide a complete `.flyer` CSS grid that shows offset alignment, asymmetric column ratios (not 40/60 — more like 38/62 or golden ratio), and explain *why* the numbers are chosen. Include guidance on visual tension: "The headline should feel like it's pulling away from the body text, not sitting on top of it." Add references to editorial design principles — Kinfolk, Cereal, Apartamento magazine aesthetics. The model needs a *taste framework*, not just a placement grid.

### 2. No Guidance on Typographic Composition

**Current state:** The skill provides a type scale table (sizes, weights, line-heights) and letter-spacing values, but nothing about how to *compose* with type.

**Problem:** Knowing that a headline is 72-96px Cormorant at weight 300 doesn't teach the model how to create typographic drama. The skill never mentions stacking words at different sizes, offsetting baselines, using italic as a semantic accent within a headline, or creating tension between a massive serif headline and tiny sans-serif details. These are the techniques that separate premium design from competent design.

**Recommendation:** Add a "Typographic Composition" section that covers:

- **Scale contrast** — pairing a 108px headline with 9px labels creates luxury tension; pairing 48px with 16px feels "safe." Push for dramatic scale jumps.
- **Asymmetric alignment** — not every headline needs to be centered or fully left-aligned. Staggered lines (first word left, second word indented) create editorial energy.
- **Italic as emphasis** — using italic on one word within a headline (like "Held in *Light*") gives the reader's eye somewhere to land. This should be an explicit technique in the skill.
- **Negative letter-spacing on display type** — the tokens mention -0.02em to -0.04em for headlines, but this should be emphasized more strongly. Tight tracking on large type is what makes it feel premium.

### 3. Photo Placeholder Strategy Is Weak

**Current state:** The skill provides a single CSS gradient as a photo placeholder and suggests adding a "Your Image Here" label.

**Problem:** This is the single biggest design weakness. A gradient with a text label looks amateurish and immediately breaks the premium illusion. When the user opens the flyer, the first thing they see is a generic gradient — the design's credibility is gone before they read a word.

**Recommendation:** Offer multiple placeholder strategies ranked by sophistication:

- **For typography-forward flyers:** Skip the photo area entirely. Don't show a placeholder. Let the type carry the design. This is the most premium option.
- **For hero-image layouts:** Use a rich, multi-stop gradient with vignetting and subtle CSS-based grain that feels like an underexposed photograph. The placeholder should look intentionally dark and moody, not "missing."
- **For service cards:** Use abstract geometric shapes (thin lines, circles) in muted tones as compositional elements where photos would go. This looks intentional.
- Never use a "Your Image Here" text label on a handout flyer. If the user is printing this to hand out, that label will print. Instead, use an HTML comment explaining where to swap in the photo, and make the placeholder visually complete so the flyer looks good *without* a photo if needed.

### 4. No QR Code Guidance

**Current state:** The skill never mentions QR codes at all.

**Problem:** QR codes are now standard on physical handouts. When the user asks for one (as in this test), the model has to improvise — deciding QR size, styling, placement, and label text with no brand guidance. This leads to inconsistent results across sessions.

**Recommendation:** Add a dedicated "QR Codes" section covering:

- **Preferred API/method** — specify an approach (e.g., the qrserver.com API, or a JS library for inline generation) so the model doesn't have to guess.
- **Styling rules** — QR wrapper should use warm cream (#e8dfd4) background, 3px border-radius, specific padding ratio. Never let the QR float without a container.
- **Size guidelines** — minimum 76px for digital display, minimum 0.8in for print scanning reliability. Maximum 100px to prevent it from dominating the layout.
- **Label styling** — "Scan to book" in Inter, 8-9px, 0.12em letter-spacing, uppercase, muted color.
- **Placement hierarchy** — the QR code should always be a secondary element. Bottom-right or bottom-center, never top or mid-page. It should be discoverable, not prominent.
- **Color inversion option** — for certain layouts, a dark QR on light background works. For others, suggest inverting. Provide both CSS approaches.

### 5. Copper Accent Guidance Needs More Nuance

**Current state:** "Use #c97b2a sparingly — for one CTA button, one divider, one highlighted word. If copper appears in more than 2-3 places, you've overdone it."

**Problem:** This tells the model *how much* copper to use but not *how* to use it effectively. Copper on a headline word, copper as a dot accent, copper as an edge line, and copper as a button all have very different visual effects. The model needs guidance on *which* uses create the most premium result.

**Recommendation:** Rank copper usage by sophistication:

- **Highest impact (use first):** A single italic word in a headline set in copper. This is the most editorial, most refined use.
- **Strong accent:** A 4-6px dot or a very thin vertical/horizontal line (1px, short length). Geometric, minimal.
- **Moderate accent:** Corner details on frames, as in the wedding flyer approach. Elegant but more decorative.
- **Lowest impact (avoid on flyers):** Full copper buttons or large colored blocks. These feel commercial, not editorial.
- **Never:** Copper text on body copy. Copper gradients. Copper borders around the entire page.

### 6. No Design System Thinking Across Pieces

**Current state:** Each flyer is treated as a standalone file. The skill provides no guidance on how multiple pieces should relate to each other visually.

**Problem:** When a user asks for 5 flyers (as in this test), they need to feel like a *collection* — clearly from the same studio, but each distinct. Without guidance, the model might make 5 identical layouts or 5 unrelated ones.

**Recommendation:** Add a "Collection Coherence" section:

- **Consistent structural DNA:** All flyers should share the same outer margin (0.85in), the same wordmark treatment, and the same contact strip format. These are the "bones" that make them feel related.
- **Varied layout strategy:** When producing multiple pieces, deliberately use different layout archetypes. Never repeat the same layout pattern back-to-back.
- **Tonal variation:** One flyer can use the deepest background (#161a12), another the standard (#242820), another the surface color (#303528). This creates subtle visual variety while staying in-palette.
- **Typography variation across pieces:** One flyer might lead with a massive word stack, another with a single centered line, another with a long poetic sentence. Variety in headline treatment prevents the set from feeling like a single template applied five times.

### 7. The Skill Doesn't Address Hierarchy of Information

**Current state:** The "Required Elements" section lists what must appear (wordmark, headline, copy, CTA, contact, copper accent) but doesn't explain the visual hierarchy between them.

**Problem:** Without hierarchy guidance, the model sometimes gives the wordmark and the headline similar visual weight, or places the contact info too prominently. A premium flyer has a clear reading order: the eye should land on the headline first, then flow to the supporting line, then naturally find the QR/contact at the bottom.

**Recommendation:** Add explicit hierarchy rules:

- **Level 1 (dominant):** Headline — this is the largest, most impactful element. Should occupy the most visual space through either size or positioning.
- **Level 2 (supporting):** Subline or poetic body copy — provides context. Noticeably smaller than the headline. Muted or secondary color.
- **Level 3 (functional):** Contact info, QR code, wordmark — these should be discoverable but never compete with the headline. Small, muted, positioned at edges.
- **The wordmark should be quiet.** It's a presence, not a shout. 12-14px is sufficient. The headline *is* the brand expression; the wordmark is just the signature.

### 8. Missing Guidance on CSS Texture and Depth

**Current state:** The skill mentions a basic CSS gradient for photo placeholders but doesn't discuss texture techniques for the overall design.

**Problem:** Flat dark backgrounds (even with the right hex color) look digital and cheap on screen. The best dark-themed designs use subtle grain, vignettes, and layered gradients to create physical depth — like looking at an actual darkroom print.

**Recommendation:** Add a "Texture & Depth" section with reusable CSS patterns:

- **Film grain overlay** — provide the exact SVG noise texture pattern as a reusable snippet, with recommended opacity (0.02-0.04).
- **Radial vignette** — darken edges subtly to draw focus to the center content.
- **Warm glow** — a very subtle (opacity 0.02-0.03) radial gradient in copper tone behind the headline creates warmth without visible color.
- **Layered backgrounds** — instead of a single flat color, use 2-3 overlapping gradients at very low opacity to create organic depth variation.

### 9. The Delivery Section Undersells the Output

**Current state:** "Briefly note what the piece is, how to use it (print via browser, screenshot for Instagram, etc.), and where to swap in their own photos."

**Recommendation:** Expand this with:

- Explicit instructions for print: "Open in Chrome, press Cmd+P, select 'Save as PDF', ensure 'Background graphics' is checked."
- Guidance on paper stock recommendations for physical handouts (matte, uncoated, heavy cardstock).
- Note that the flyers are designed at exact US Letter size and will print edge-to-edge if the printer supports borderless printing.
- For photo insertion: provide the exact CSS property to change (`background-image: url('your-photo.jpg'); background-size: cover; background-position: center;`) so the user doesn't need to know CSS.

### 10. No Seasonal or Contextual Adaptation Framework

**Current state:** The skill treats all flyers as generic. No guidance on how to adapt the design or copy for specific contexts (spring mini sessions vs. holiday portraits vs. studio opening).

**Recommendation:** Add a brief "Contextual Adaptation" section with:

- Seasonal copy tone shifts (spring = "light and bloom" language; autumn = "golden, warm" language; winter = "quiet, intimate")
- When and how to adjust the color temperature — a winter session flyer might use cooler muted tones (#8a9878 shifted slightly blue) while a summer one stays warm
- Event-specific additions: for mini sessions, mention "limited availability" language; for studio openings, shift tone to "invitation" style

---

## Scoring Summary

| Category | Score (1-10) | Notes |
|---|---|---|
| Brand token system | 9 | Excellent. Nearly complete. |
| Voice / copy guidance | 8 | Strong. Could add seasonal variants. |
| Layout direction | 4 | Too generic. Needs editorial specificity. |
| Typography composition | 3 | Size/weight specs exist, composition guidance missing. |
| Visual depth / texture | 2 | Almost no guidance. Major gap. |
| QR code integration | 0 | Not addressed at all. |
| Multi-piece coherence | 1 | Not addressed. |
| Hierarchy guidance | 3 | Elements listed, visual order not explained. |
| Photo handling | 3 | Basic gradient only. Needs sophistication. |
| Delivery instructions | 5 | Functional but thin. |

**Overall skill maturity: 5/10** — The brand identity layer is strong. The design execution layer needs significant depth.

---

## Priority Fixes (in order)

1. Add QR code section (zero coverage, high user demand)
2. Rewrite layout patterns with CSS examples showing asymmetry and tension
3. Add typographic composition techniques
4. Add texture/depth CSS patterns
5. Add multi-piece coherence rules
6. Expand photo placeholder strategy
7. Refine copper accent hierarchy
8. Add information hierarchy rules
9. Add seasonal adaptation framework
10. Expand delivery instructions

---

*This report evaluates the skill's instructions and reference material, not the individual flyers produced. The flyers created during this test deliberately went beyond the skill's guidance to demonstrate the gap between what the skill teaches and what premium output requires.*
