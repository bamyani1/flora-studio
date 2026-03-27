# Layered Hero "Emergence" Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the disjointed layered GalleryHero entrance and scroll parallax with a cohesive 3-act cinematic reveal and atmospheric depth parallax.

**Architecture:** Rewrite the `layeredHeroReveal` preset in `animations.ts` to a 3-act structure (act1/act2/act3 + parallax with atmosphere). Rewrite the layered `useGSAP` block in `GalleryHero.tsx` to use the new preset with absolute timeline positions. Add one new DOM element (atmosphere overlay) for scroll-driven background darkening.

**Tech Stack:** GSAP 3 (timeline, ScrollTrigger, fromTo), React 19, Next.js 16, TypeScript

---

### Task 1: Rewrite `layeredHeroReveal` preset in `animations.ts`

**Files:**

- Modify: `src/lib/animations.ts:365-397`

- [ ] **Step 1: Replace the `layeredHeroReveal` export**

Replace the entire `layeredHeroReveal` block (lines 365-397) with the new 3-act structure:

```typescript
// --------------------------------------------------
// layeredHeroReveal — "Emergence" 3-act cinematic reveal
// Act 1: darkness lifts → Act 2: subject steps forward → Act 3: title lands
// --------------------------------------------------
export const layeredHeroReveal = {
  /** Act 1: Darkness lifts (0–1.2s) — moody landscape emerges */
  act1: {
    darkOverlay: {
      from: { autoAlpha: 0.9 },
      to: { autoAlpha: 0.2, duration: 1.2, ease: "power2.inOut" },
    },
    blurOverlay: {
      from: { autoAlpha: 1 },
      to: { autoAlpha: 0, duration: 1.2, ease: "power2.inOut" },
    },
    background: {
      from: { scale: 1.15 },
      to: { scale: 1.05, duration: 1.2, ease: "power2.out" },
    },
  },
  /** Act 2: Subject steps forward (1.0–2.2s) — emerges from atmosphere */
  act2: {
    subject: {
      from: { autoAlpha: 0, scale: 1.05 },
      to: { autoAlpha: 1, scale: 1.0, duration: 1.2, ease: "power2.out" },
    },
    darkOverlayClear: {
      from: { autoAlpha: 0.2 },
      to: { autoAlpha: 0, duration: 1.0, ease: "power2.out" },
    },
    backgroundSettle: {
      from: { scale: 1.05 },
      to: { scale: 1.0, duration: 2.0, ease: "power3.out" },
    },
  },
  /** Act 3: Title lands (2.0–3.2s) — editorial stamp on the composition */
  act3: {
    title: {
      from: { clipPath: "inset(100% 0 0 0)" },
      to: { clipPath: "inset(0% 0 0 0)", duration: 1.0, ease: "power3.out" },
    },
    label: {
      from: { autoAlpha: 0, y: 15 },
      to: { autoAlpha: 1, y: 0, duration: 0.8, ease: "power3.out" },
    },
    description: {
      from: { autoAlpha: 0, y: 15 },
      to: { autoAlpha: 1, y: 0, duration: 0.8, ease: "power3.out" },
    },
    scrollCue: {
      from: { autoAlpha: 0 },
      to: { autoAlpha: 1, duration: 0.8, ease: "power3.out" },
    },
  },
  /** Scroll-driven atmospheric depth parallax */
  parallax: {
    background: {
      to: { yPercent: 25, scale: 0.9, ease: "none" },
      scrollTrigger: { start: "top top", end: "bottom top", scrub: true },
    },
    atmosphere: {
      from: { autoAlpha: 0 },
      to: { autoAlpha: 0.35, ease: "none" },
      scrollTrigger: { start: "top top", end: "bottom top", scrub: true },
    },
    title: {
      to: { yPercent: 15, autoAlpha: 0, ease: "none" },
      scrollTrigger: { start: "top top", end: "bottom top", scrub: true },
    },
    subject: {
      to: { yPercent: -10, scale: 1.2, ease: "none" },
      scrollTrigger: { start: "top top", end: "bottom top", scrub: true },
    },
  },
};
```

- [ ] **Step 2: Run type-check**

Run: `npm run type-check`
Expected: PASS (the preset is a plain object — no type imports needed)

- [ ] **Step 3: Commit**

```bash
git add src/lib/animations.ts
git commit -m "refactor(animations): rewrite layeredHeroReveal to 3-act emergence structure"
```

---

### Task 2: Rewrite layered entrance animation in `GalleryHero.tsx`

**Files:**

- Modify: `src/components/sections/gallery/GalleryHero.tsx:41-160`

- [ ] **Step 1: Update the reduced-motion fallback (lines 47-56)**

Replace the existing reduced-motion block inside the layered `useGSAP` with one that also handles the new atmosphere overlay and title clip-path:

```typescript
if (reduced) {
  gsap.set(
    el.querySelectorAll(
      ".layered-hero-bg, .layered-hero-title, .layered-hero-subject, .gallery-hero-label, .gallery-hero-desc, .gallery-hero-scroll",
    ),
    { autoAlpha: 1, y: 0, scale: 1, rotationX: 0 },
  );
  gsap.set(el.querySelector(".gallery-hero-dark-overlay"), { autoAlpha: 0 });
  gsap.set(el.querySelector(".gallery-hero-blur-overlay"), { autoAlpha: 0 });
  gsap.set(el.querySelector(".layered-hero-atmosphere"), { autoAlpha: 0 });
  gsap.set(el.querySelector(".layered-hero-title"), {
    clipPath: "inset(0% 0 0 0)",
  });
  return;
}
```

- [ ] **Step 2: Rewrite the entrance timeline (lines 59-124)**

Replace everything from `const tl = gsap.timeline(...)` through the bottom UI `if (scroll)` block with the new 3-act timeline:

```typescript
const tl = gsap.timeline({ ...withWillChange() });

// --- Act 1: Darkness lifts (0–1.2s) ---
tl.fromTo(
  el.querySelector(".gallery-hero-dark-overlay"),
  layeredHeroReveal.act1.darkOverlay.from,
  { ...layeredHeroReveal.act1.darkOverlay.to },
  0,
);
tl.fromTo(
  el.querySelector(".gallery-hero-blur-overlay"),
  layeredHeroReveal.act1.blurOverlay.from,
  { ...layeredHeroReveal.act1.blurOverlay.to },
  0,
);
tl.fromTo(
  el.querySelector(".layered-hero-bg"),
  layeredHeroReveal.act1.background.from,
  { ...layeredHeroReveal.act1.background.to },
  0,
);

// --- Act 2: Subject steps forward (1.0–2.2s) ---
tl.fromTo(
  el.querySelector(".layered-hero-subject"),
  layeredHeroReveal.act2.subject.from,
  { ...layeredHeroReveal.act2.subject.to },
  1.0,
);
tl.fromTo(
  el.querySelector(".gallery-hero-dark-overlay"),
  layeredHeroReveal.act2.darkOverlayClear.from,
  { ...layeredHeroReveal.act2.darkOverlayClear.to },
  1.2,
);
tl.fromTo(
  el.querySelector(".layered-hero-bg"),
  layeredHeroReveal.act2.backgroundSettle.from,
  { ...layeredHeroReveal.act2.backgroundSettle.to },
  1.0,
);

// --- Act 3: Title lands (2.0–3.2s) ---
tl.fromTo(
  el.querySelector(".layered-hero-title"),
  layeredHeroReveal.act3.title.from,
  { ...layeredHeroReveal.act3.title.to },
  2.0,
);

const label = el.querySelector(".gallery-hero-label");
const desc = el.querySelector(".gallery-hero-desc");
const scroll = el.querySelector(".gallery-hero-scroll");

if (label) {
  tl.fromTo(label, layeredHeroReveal.act3.label.from, { ...layeredHeroReveal.act3.label.to }, 2.4);
}
if (desc) {
  tl.fromTo(
    desc,
    layeredHeroReveal.act3.description.from,
    { ...layeredHeroReveal.act3.description.to },
    2.6,
  );
}
if (scroll) {
  tl.fromTo(
    scroll,
    layeredHeroReveal.act3.scrollCue.from,
    { ...layeredHeroReveal.act3.scrollCue.to },
    2.8,
  );
}
```

- [ ] **Step 3: Rewrite the parallax block (lines 126-158)**

Replace the existing parallax setup with the new atmospheric parallax that includes the atmosphere overlay and title fade:

```typescript
// Atmospheric depth parallax
if (!smoothMode) {
  const parallaxTargets = [
    { sel: ".layered-hero-bg", config: layeredHeroReveal.parallax.background },
    { sel: ".layered-hero-title", config: layeredHeroReveal.parallax.title },
    { sel: ".layered-hero-subject", config: layeredHeroReveal.parallax.subject },
  ];

  for (const { sel, config } of parallaxTargets) {
    const target = el.querySelector<HTMLElement>(sel);
    if (target) {
      gsap.to(target, {
        ...config.to,
        scrollTrigger: {
          trigger: el,
          ...config.scrollTrigger,
          onEnter: () => {
            target.style.willChange = "transform";
          },
          onLeave: () => {
            target.style.willChange = "auto";
          },
          onEnterBack: () => {
            target.style.willChange = "transform";
          },
          onLeaveBack: () => {
            target.style.willChange = "auto";
          },
        },
      });
    }
  }

  // Atmosphere overlay: scroll-driven background darkening
  const atmosphere = el.querySelector<HTMLElement>(".layered-hero-atmosphere");
  if (atmosphere) {
    gsap.fromTo(atmosphere, layeredHeroReveal.parallax.atmosphere.from, {
      ...layeredHeroReveal.parallax.atmosphere.to,
      scrollTrigger: {
        trigger: el,
        ...layeredHeroReveal.parallax.atmosphere.scrollTrigger,
        onEnter: () => {
          atmosphere.style.willChange = "opacity";
        },
        onLeave: () => {
          atmosphere.style.willChange = "auto";
        },
        onEnterBack: () => {
          atmosphere.style.willChange = "opacity";
        },
        onLeaveBack: () => {
          atmosphere.style.willChange = "auto";
        },
      },
    });
  }
}
```

- [ ] **Step 4: Remove the `cinematicHeroReveal` import usage from the layered block**

The layered entrance no longer uses `cinematicHeroReveal` for its overlays or bottom UI — it uses `layeredHeroReveal.act1/act2/act3` instead. Verify that `cinematicHeroReveal` is no longer referenced anywhere in the layered useGSAP block (lines 41-160). It IS still used by the standard (non-layered) hero block below — don't remove the import.

- [ ] **Step 5: Run type-check**

Run: `npm run type-check`
Expected: PASS

- [ ] **Step 6: Commit**

```bash
git add src/components/sections/gallery/GalleryHero.tsx
git commit -m "refactor(gallery): rewrite layered hero entrance to 3-act emergence timeline"
```

---

### Task 3: Add atmosphere overlay DOM element to layered hero render

**Files:**

- Modify: `src/components/sections/gallery/GalleryHero.tsx:254-336` (layered render block)

- [ ] **Step 1: Add the atmosphere overlay element**

Inside the layered hero render block, add the atmosphere overlay as a sibling AFTER the existing `gallery-hero-dark-overlay` div (line 278). The new element sits inside the background container alongside the blur and dark overlays:

Find this block (lines 272-279):

```tsx
          {/* Cinematic overlays */}
          <div
            className="gallery-hero-blur-overlay absolute inset-0"
            style={{ backdropFilter: "blur(20px)", WebkitBackdropFilter: "blur(20px)" }}
            aria-hidden="true"
          />
          <div className="gallery-hero-dark-overlay absolute inset-0 bg-black" aria-hidden="true" />
```

Add the atmosphere overlay after it:

```tsx
          {/* Cinematic overlays */}
          <div
            className="gallery-hero-blur-overlay absolute inset-0"
            style={{ backdropFilter: "blur(20px)", WebkitBackdropFilter: "blur(20px)" }}
            aria-hidden="true"
          />
          <div className="gallery-hero-dark-overlay absolute inset-0 bg-black" aria-hidden="true" />
          <div className="layered-hero-atmosphere absolute inset-0 bg-black" aria-hidden="true" />
```

The atmosphere overlay starts invisible (`autoAlpha: 0` set by the scroll parallax `from` state). It is only animated by the scroll parallax — NOT by the entrance timeline.

- [ ] **Step 2: Run type-check and lint**

Run: `npm run type-check && npm run lint`
Expected: PASS (only pre-existing warning in `scripts/process-images.mjs`)

- [ ] **Step 3: Commit**

```bash
git add src/components/sections/gallery/GalleryHero.tsx
git commit -m "feat(gallery): add atmosphere overlay for scroll-driven depth darkening"
```

---

### Task 4: Verify and test the complete effect

- [ ] **Step 1: Run full type-check and lint**

Run: `npm run type-check && npm run lint`
Expected: PASS

- [ ] **Step 2: Visual verification on dev server**

Run: `npm run dev` (if not already running)

Navigate to `http://localhost:3000/work` and verify:

**Entrance sequence:**

1. Scene starts dark and blurred (Act 1: 0–1.2s)
2. Subject fades in while darkness clears (Act 2: 1.0–2.2s)
3. Title clips upward into view, then label/desc/scroll cascade (Act 3: 2.0–3.2s)
4. Each act has ONE clear hero element — no competing simultaneous fades

**Scroll parallax:**

1. Background drifts down, shrinks, and darkens (atmosphere overlay)
2. Subject drifts up and grows (comes toward viewer)
3. Title drifts down and fades out (graceful exit)
4. Overall impression: pushing deeper into the scene

**Edge cases:**

- Navigate away (click an album) then back — entrance replays cleanly after iris transition
- Reduced motion (DevTools → Rendering → "prefers-reduced-motion: reduce"): all layers visible immediately, no animation, no parallax, title clip-path fully open

- [ ] **Step 3: Commit final verification**

```bash
git add -A
git commit -m "verify(gallery): layered hero emergence effect complete"
```
