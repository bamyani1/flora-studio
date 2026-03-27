# Layered GalleryHero: "Emergence" Redesign

## Problem

The layered hero (background + title-behind-subject + subject cutout) on `/work` has a disjointed entrance: three overlays fade simultaneously, the subject pops in fast then sits idle, and the title enters mid-chaos. There is no narrative arc — elements compete instead of building on each other.

The scroll parallax is functional but flat: three layers move at different speeds, but without atmospheric perspective the depth illusion is unconvincing.

## Scope

- **Component:** `src/components/sections/gallery/GalleryHero.tsx` — layered mode only (the `hasLayers` branch)
- **Presets:** `src/lib/animations.ts` — `layeredHeroReveal` object
- Standard (non-layered) hero is unchanged.

---

## Entrance: 3-Act Cinematic Reveal (~3.2s)

The entrance tells a story: darkness → subject → title. Each act has one clear hero element.

### Act 1: "The Darkness Lifts" (0–1.2s)

| Element      | From                    | To               | Duration | Ease           | Position |
| ------------ | ----------------------- | ---------------- | -------- | -------------- | -------- |
| Dark overlay | `autoAlpha: 0.9`        | `autoAlpha: 0.2` | 1.2s     | `power2.inOut` | 0        |
| Blur overlay | `autoAlpha: 1`          | `autoAlpha: 0`   | 1.2s     | `power2.inOut` | 0        |
| Background   | `scale: 1.15`           | `scale: 1.05`    | 1.2s     | `power2.out`   | 0        |
| Subject      | Hidden (`autoAlpha: 0`) | —                | —        | —              | —        |

The viewer sees a moody, emerging landscape. No subject, no text. Anticipation builds.

### Act 2: "The Subject Steps Forward" (1.0–2.2s)

| Element      | From                        | To                         | Duration | Ease         | Position |
| ------------ | --------------------------- | -------------------------- | -------- | ------------ | -------- |
| Subject      | `autoAlpha: 0, scale: 1.05` | `autoAlpha: 1, scale: 1.0` | 1.2s     | `power2.out` | 1.0      |
| Dark overlay | `autoAlpha: 0.2`            | `autoAlpha: 0`             | 1.0s     | `power2.out` | 1.2      |
| Background   | `scale: 1.05`               | `scale: 1.0`               | 2.0s     | `power3.out` | 1.0      |

The subject emerges from the atmosphere. Dark overlay finishes clearing. Background settles to its final scale.

### Act 3: "The Title Lands" (2.0–3.2s)

| Element       | From                            | To                            | Duration | Ease         | Position |
| ------------- | ------------------------------- | ----------------------------- | -------- | ------------ | -------- |
| Title         | `clipPath: "inset(100% 0 0 0)"` | `clipPath: "inset(0% 0 0 0)"` | 1.0s     | `power3.out` | 2.0      |
| Chapter label | `autoAlpha: 0, y: 15`           | `autoAlpha: 1, y: 0`          | 0.8s     | `power3.out` | 2.4      |
| Description   | `autoAlpha: 0, y: 15`           | `autoAlpha: 1, y: 0`          | 0.8s     | `power3.out` | 2.6      |
| Scroll cue    | `autoAlpha: 0`                  | `autoAlpha: 1`                | 0.8s     | `power3.out` | 2.8      |

Title reveals via clip-path wipe (clean upward reveal, not a fade). Bottom UI cascades after.

### Timeline Visualization

```
0.0s ─── Act 1: Darkness lifts ───────────────── 1.2s
         [dark overlay 0.9→0.2          ]
         [blur overlay 1.0→0            ]
         [background   1.15→1.05        ]

    1.0s ─── Act 2: Subject appears ──────────── 2.2s
              [subject     0→1, 1.05→1.0  ]
         1.2s [dark overlay 0.2→0     ]
         1.0s [background   1.05→1.0 ──── 3.0s]

         2.0s ─── Act 3: Title lands ─────────── 3.2s
                   [title clip  100%→0%    ]
              2.4s [label fade             ]
              2.6s [desc fade              ]
              2.8s [scroll cue             ]
```

Acts overlap slightly (Act 2 starts at 1.0s, before Act 1's 1.2s end) for smooth flow. Each act's hero element is unambiguous.

---

## Scroll: Atmospheric Depth Parallax

After the entrance completes, scrolling creates the sensation of pushing deeper into the scene. All ScrollTrigger instances use `scrub: true` with range `start: "top top"` → `end: "bottom top"`.

### Background Layer (farthest)

| Property   | From | To  | Purpose                         |
| ---------- | ---- | --- | ------------------------------- |
| `yPercent` | 0    | 25  | Drifts down (falls behind)      |
| `scale`    | 1.0  | 0.9 | Shrinks (recedes into distance) |

Plus a NEW **atmosphere overlay** element (sibling div with `bg-black`):

| Property    | From | To   | Purpose                                      |
| ----------- | ---- | ---- | -------------------------------------------- |
| `autoAlpha` | 0    | 0.35 | Darkens background on scroll (distance haze) |

This overlay is separate from the entrance dark overlay (which has `autoAlpha: 0` / `visibility: hidden` after entrance).

### Subject Layer (closest)

| Property   | From | To  | Purpose                         |
| ---------- | ---- | --- | ------------------------------- |
| `yPercent` | 0    | -10 | Drifts up (comes toward viewer) |
| `scale`    | 1.0  | 1.2 | Grows (advances toward camera)  |

### Title Layer (middle)

| Property    | From | To  | Purpose                                    |
| ----------- | ---- | --- | ------------------------------------------ |
| `yPercent`  | 0    | 15  | Drifts down (between bg and subject speed) |
| `autoAlpha` | 1    | 0   | Fades out (served its purpose, clean exit) |

### Depth Cue Summary

| Layer      | Position     | Scale   | Atmosphere  | Net impression          |
| ---------- | ------------ | ------- | ----------- | ----------------------- |
| Background | Falls behind | Shrinks | Darkens     | Convincingly "far away" |
| Title      | Medium drift | None    | Fades out   | Graceful exit           |
| Subject    | Advances     | Grows   | Stays crisp | "Coming toward you"     |

---

## Implementation Notes

### New DOM Element

Add one sibling div inside the background container for the scroll-driven atmospheric darkening:

```html
<div className="layered-hero-atmosphere absolute inset-0 bg-black" aria-hidden="true" />
```

Initial state: `autoAlpha: 0`. Only animated by scroll parallax (not the entrance timeline).

### Entrance Timeline Structure

Replace the current flat timeline with a multi-phase approach. All timings use absolute positions (not `-=` offsets) for clarity:

```
const tl = gsap.timeline({ ...withWillChange() });

// Act 1: Darkness lifts
tl.fromTo(darkOverlay, ..., 0);
tl.fromTo(blurOverlay, ..., 0);
tl.fromTo(background, ..., 0);

// Act 2: Subject emerges (overlaps Act 1)
tl.fromTo(subject, ..., 1.0);
tl.fromTo(darkOverlay, { autoAlpha: 0.2 }, { autoAlpha: 0 }, 1.2);
tl.fromTo(background, ..., 1.0);  // Continue settling

// Act 3: Title lands
tl.fromTo(title, ..., 2.0);
tl.fromTo(label, ..., 2.4);
tl.fromTo(desc, ..., 2.6);
tl.fromTo(scroll, ..., 2.8);
```

Note: The dark overlay is animated in TWO phases — first 0.9→0.2 (Act 1), then 0.2→0 (Act 2). This creates the "darkness lifts then fully clears" narrative beat.

### Preset Changes

Update `layeredHeroReveal` in `animations.ts` to reflect the new structure. The current single-phase presets become multi-phase:

```js
layeredHeroReveal = {
  act1: { darkOverlay, blurOverlay, background },
  act2: { subject, darkOverlayClear, backgroundSettle },
  act3: { title, label, description, scrollCue },
  parallax: { background, subject, title, atmosphere },
};
```

### Will-Change Management

- Entrance: `withWillChange()` on the timeline handles start/complete
- Parallax: Per-element `onEnter`/`onLeave` callbacks (existing pattern)
- Atmosphere overlay: Same lifecycle as parallax targets

### Reduced Motion Fallback

When `prefers-reduced-motion: reduce`:

- All layers set to `autoAlpha: 1, scale: 1, y: 0`
- Dark/blur overlays set to `autoAlpha: 0`
- Atmosphere overlay set to `autoAlpha: 0`
- Title clip-path set to `inset(0% 0 0 0)` (fully revealed)
- No scroll parallax

---

## Files to Modify

1. `src/lib/animations.ts` — rewrite `layeredHeroReveal` preset
2. `src/components/sections/gallery/GalleryHero.tsx` — rewrite layered entrance animation + parallax setup + add atmosphere overlay element

---

## Verification

1. `npm run type-check` and `npm run lint`
2. Dev server `/work` page:
   - Entrance: darkness lifts → subject appears → title clips in (clear 3-act sequence)
   - Scroll: background darkens + recedes, subject grows + advances, title fades out
   - Navigate away and back — animations replay cleanly after iris transition
3. Reduced motion: all layers visible immediately, no animation, no parallax
4. DevTools Layers panel: atmosphere overlay and entrance overlays use `autoAlpha` for clean compositor lifecycle
