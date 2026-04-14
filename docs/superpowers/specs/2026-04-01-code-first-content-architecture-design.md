# Code-First Content Architecture

## Problem

The site fetches ~65 text fields from Sanity CMS at runtime (page copy, brand name, CTAs, descriptions, team info, process steps). This creates two problems:

1. **Rebrand failure**: After renaming "Flora Studio" to "Flora Studio" in code, the contact page still shows the old name because `studioName` comes from Sanity, which wasn't updated.
2. **Split source of truth**: Some text lives in code (metadata, legal, footers, email templates), some in Sanity. Changes require editing two systems.

## Decision

**Move all site-level text content to code. Sanity becomes an image CMS + album manager.**

- Albums stay fully in Sanity (titles, descriptions, image collections for `/work` and `/work/[slug]`)
- Sanity studio stays at `/studio` for album management
- All other text (site settings, page copy, hero text, CTAs, team info, process steps) moves to code
- Image `alt` text stays in Sanity (it's semantically about the image, not the brand)
- Text changes become git commits — version-controlled, CI-testable, PR-reviewable

## Architecture

### Before (current)

```
Page renders → getSiteSettings() / getHomePageContent() / etc.
  → try Sanity CMS (text + images together in one document)
  → catch: soft fallback to PLACEHOLDER_* (local/preview)
  → catch: throw ContentUnavailableError (production)
```

### After (target)

```
Page renders → text from SITE_SETTINGS / HOME_PAGE / etc. (code, always available)
            → images from Sanity via getHomePageImages() / etc. (fetch, with fallback)
            → merge text + images into same content interfaces
Albums      → full data from Sanity (unchanged)
```

The content type interfaces (`src/types/content.ts`) remain unchanged. Pages and components see the same typed objects — only the data source changes.

## Detailed File Changes

### 1. Rename and promote placeholder content

**`src/lib/placeholder-site-content.ts` → `src/lib/site-text-content.ts`**

- Remove `PLACEHOLDER_` prefix from all exports (e.g., `SITE_SETTINGS`, `HOME_PAGE`, etc.)
- This becomes the canonical source of all site text
- Update all imports across the codebase (4 files import from this)

### 2. Rewrite content functions in `src/lib/site-content.ts`

| Current Function          | New Behavior                                                                                                                                                                                                        |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `getSiteSettings()`       | Return `SITE_SETTINGS` from code directly. No Sanity fetch needed — site settings have no images.                                                                                                                   |
| `getHomePageContent()`    | Start with `HOME_PAGE` text from code. Fetch **only images** from Sanity (`heroMediaCycle[]`, `editorialImage`, `exhibitionImage`, `studioImage`). Merge images into the text object, replacing placeholder images. |
| `getAboutPageContent()`   | Start with `ABOUT_PAGE` text from code. Fetch **only images** from Sanity (`teamMembers[]{name, portrait}`, `processImage`). Merge portraits by matching `name`.                                                    |
| `getProcessPageContent()` | Start with `PROCESS_PAGE` text from code. Fetch **only images** from Sanity (`heroImage`, `steps[]{id, images[]}`). Merge step images by matching `id`.                                                             |
| `getContactPageContent()` | Return `CONTACT_PAGE` from code directly. No Sanity fetch (no images on contact page).                                                                                                                              |

**Image merge strategies:**

- **Home page**: Direct field replacement — `content.hero.mediaCycle = sanityImages.heroMediaCycle`
- **About team portraits**: Join by `member.name`. For each code-side team member, look up portrait from Sanity data. Fall back to local image if no match.
- **Process step images**: Join by `step.id`. For each code-side step, look up images from Sanity data. Fall back to local images if no match.
- **All images**: Sanity `imageWithAlt` objects carry `alt` text — preserve it. Only override with code fallback if Sanity `alt` is missing.

**Fallback behavior:**

- Image fetch failures: use existing placeholder images from code (already present in the content objects)
- `resolveContentAvailabilityFailure()` changes: fallbacks now return only image data (partial), not full content objects. The merge function applies these image overrides to the code-based text content.
- In production: if Sanity is unreachable, pages still render with code text + placeholder images (no crash).

### 3. Simplify Sanity queries in `src/sanity/queries.ts`

| Query                 | Action               | Keep                                                                                                                                         |
| --------------------- | -------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| `SITE_SETTINGS_QUERY` | Delete entirely      | Nothing (no images)                                                                                                                          |
| `HOME_PAGE_QUERY`     | Strip to images only | `heroMediaCycle[]{..., asset->{url}}, editorialImage{..., asset->{url}}, exhibitionImage{..., asset->{url}}, studioImage{..., asset->{url}}` |
| `ABOUT_PAGE_QUERY`    | Strip to images only | `teamMembers[]{name, portrait{..., asset->{url}}}, processImage{..., asset->{url}}` (keep `name` as merge key)                               |
| `PROCESS_PAGE_QUERY`  | Strip to images only | `heroImage{..., asset->{url}}, steps[]{id, images[]{..., asset->{url}}}` (keep `id` as merge key)                                            |
| `CONTACT_PAGE_QUERY`  | Delete entirely      | Nothing (no images)                                                                                                                          |
| `ALBUMS_QUERY`, etc.  | Keep unchanged       | Everything (albums stay fully in Sanity)                                                                                                     |

### 4. Update Sanity schemas

**Delete (text-only, no images):**

- `src/sanity/schemas/contactPage.ts`

**Strip to image-only fields:**

- `src/sanity/schemas/siteSettings.ts` — delete entirely (no images). Social links move to code.
- `src/sanity/schemas/homePage.ts` — keep only image fields (`heroMediaCycle`, `editorialImage`, `exhibitionImage`, `studioImage`)
- `src/sanity/schemas/aboutPage.ts` — keep only `teamMembers[]{name, portrait}` and `processImage`
- `src/sanity/schemas/processPage.ts` — keep only `heroImage` and `steps[]{id, images[]}`

**Update shared object schemas in `src/sanity/schemas/objects.ts`:**

- `imageWithAlt` — keep (still used for all image fields)
- `ctaLink` — delete (all CTAs are now in code)
- `socialLink` — delete (social links move to code)
- `teamMember` — strip to `{name, portrait}` only (name kept as merge key)
- `aboutProcessCard` — delete (text-only)
- `processStep` — strip to `{id, images[]}` only (id kept as merge key)

**Update `src/sanity/schemas/index.ts`:**

- Remove `contactPage` and `siteSettings` from schema type registration
- Keep `album`, stripped `homePage`, `aboutPage`, `processPage`

### 5. Update CMS validation in `src/lib/cms-validation.ts`

- Delete `siteSettingsDocumentSchema` and `contactPageDocumentSchema` entirely
- Strip `homePageDocumentSchema` to validate only image fields
- Strip `aboutPageDocumentSchema` to validate only `teamMembers[]{name, portrait}` and `processImage`
- Strip `processPageDocumentSchema` to validate only `heroImage` and `steps[]{id, images[]}`
- Remove unused helper schemas (`ctaLinkSchema`, `socialLinkSchema`) if no longer referenced
- Keep `sanityImageSchema` (validates image objects with `alt`/`caption`)
- Album validation schemas: unchanged

### 6. Update E2E content in `src/lib/e2e-content.ts`

- Update import path from `placeholder-site-content` → `site-text-content`
- Rename constants from `E2E_PLACEHOLDER_*` pattern — these are re-exports so the change is just the import source

### 7. Update `scripts/migrate-sanity.ts`

- Update import path from `placeholder-site-content` → `site-text-content`

### 8. Rewrite tests in `tests/unit/site-content.test.ts`

This is significant work. Currently the tests:

- Construct full Sanity document objects (`rawHomePage()`, `rawAboutPage()`, etc.) with all text fields
- Mock `sanityFetch` to return these full documents
- Assert the normalized output matches expected content

After the change:

- `getSiteSettings()` and `getContactPageContent()` return code constants — test that they return the right data without any fetch
- `getHomePageContent()`, `getAboutPageContent()`, `getProcessPageContent()` merge code text + Sanity images — test the merge logic
- Test raw document helpers must be rewritten to image-only shapes
- Test fallback behavior: when Sanity image fetch fails, pages render with placeholder images

## What stays unchanged

- Album system (`src/lib/albums.ts`, album pages, album queries, album schemas)
- Content type interfaces (`src/types/content.ts`)
- All page components and their props — they receive the same typed objects
- All child components (LandingHero, AboutPageClient, ProcessExperience, etc.)
- Legal content (`src/lib/legal-content.ts`)
- Email templates (`src/app/(site)/contact/action.ts`)
- Metadata (`src/lib/metadata.ts`)
- OG image routes
- `SiteMedia` component
- Image URL resolution (`src/lib/image-url.ts`)
- LQIP generation (`src/lib/lqip.ts`)

## Known risks and mitigations

| Risk                                                                     | Mitigation                                                                                                                                                               |
| ------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Process step image merge: Sanity has different number of steps than code | Join by `id`. Missing steps in Sanity → use placeholder images. Extra steps in Sanity → ignore.                                                                          |
| Team portrait merge: name mismatch between code and Sanity               | Join by `name`. Unmatched members → use placeholder portrait. Log warning.                                                                                               |
| `LandingHero` uses `media.asset._ref` as React key                       | Ensure `localImage()` produces unique `_ref` values (include image path hash, not just dimensions).                                                                      |
| Production image fetch failure crashes page                              | Change to graceful degradation: render text + placeholder images. Log error but don't throw.                                                                             |
| `resolveContentAvailabilityFailure()` shape changes                      | Refactor fallback to return image-partial objects, not full content objects.                                                                                             |
| `resolveContentAvailabilityFailure()` is shared with albums              | Albums module calls it 4 times with full `Album[]` fallbacks. Any refactor MUST keep the generic signature working for albums. Use overloads or keep it generic.         |
| `_id` field on code constants becomes synthetic                          | `SiteSettings` requires `_id: string`. The code constant hardcodes `_id: "siteSettings"` — this is intentional, not a real Sanity document ID. Document in code comment. |
| Stale Sanity image data (e.g., deleted team member still has portrait)   | Merge is code-authoritative: if a member isn't in code, their Sanity portrait is ignored.                                                                                |

## Complete affected file list

### Core implementation changes:

1. `src/lib/placeholder-site-content.ts` → rename to `src/lib/site-text-content.ts`, remove `PLACEHOLDER_` prefix
2. `src/lib/site-content.ts` — rewrite all 5 content functions
3. `src/sanity/queries.ts` — delete 2 queries, strip 3 to image-only
4. `src/lib/cms-validation.ts` — delete 2 schemas, strip 3 to image-only
5. `src/lib/e2e-content.ts` — update import path
6. `src/lib/content-runtime.server.ts` — update fallback behavior for image-only fetching

### Sanity schema changes:

7. `src/sanity/schemas/siteSettings.ts` — delete
8. `src/sanity/schemas/contactPage.ts` — delete
9. `src/sanity/schemas/homePage.ts` — strip to image fields
10. `src/sanity/schemas/aboutPage.ts` — strip to image fields
11. `src/sanity/schemas/processPage.ts` — strip to image fields
12. `src/sanity/schemas/objects.ts` — delete `ctaLink`, `socialLink`, `aboutProcessCard`; strip `teamMember`, `processStep`
13. `src/sanity/schemas/index.ts` — remove deleted types

### Test changes:

14. `tests/unit/site-content.test.ts` — major rewrite

### Minor import path updates:

15. `scripts/migrate-sanity.ts` — update import

### Unchanged but affected (consumers to verify):

16. `src/app/(site)/layout.tsx` — calls `getSiteSettings()` (no code change, but behavior changes)
17. `src/app/(site)/page.tsx` — calls `getHomePageContent()` + `getSiteSettings()`
18. `src/app/(site)/about/page.tsx` — calls `getAboutPageContent()` + `getSiteSettings()`
19. `src/app/(site)/contact/page.tsx` — calls `getContactPageContent()` + `getSiteSettings()`
20. `src/app/(site)/process/page.tsx` — calls `getProcessPageContent()` + `getSiteSettings()`

## Verification

1. `npm run type-check` — no broken types
2. `npm run lint` — clean
3. `npm run test:unit` — all pass (after test rewrite)
4. `npm run test:e2e` — all pass
5. `npm run build` — production build succeeds
6. Manual: contact page shows "FLORA STUDIO" (from code, not Sanity)
7. Manual: all pages render correct text without Sanity connection
8. Manual: albums still load from Sanity
9. Manual: `/studio` still works for album management
10. Manual: disconnect Sanity → pages render with text + placeholder images (no crash)
