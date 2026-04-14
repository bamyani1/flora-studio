# Launch Checklist — floraohio.com

## Pre-launch Verification

- [ ] `npm run verify` passes (lint, type-check, unit tests, e2e, build)
- [ ] `npm run verify:deploy` passes (env vars, no placeholder:// in build, no large public files)
- [ ] `npm run smoke` passes (all routes 200, live Sanity data, OG tags, sitemap, robots.txt)
- [ ] No `placeholder://` URLs in any rendered page
- [ ] No TODO/FIXME comments in codebase

## Environment Variables (Vercel Dashboard)

All must be set before first production deploy:

| Variable                            | Value / Notes                                    |
| ----------------------------------- | ------------------------------------------------ |
| `NEXT_PUBLIC_SANITY_PROJECT_ID`     | Your Sanity project ID                           |
| `NEXT_PUBLIC_SANITY_DATASET`        | `production`                                     |
| `NEXT_PUBLIC_SANITY_API_VERSION`    | `2024-07-11`                                     |
| `SANITY_READ_TOKEN`                 | Viewer-level token (starts with `sk`)            |
| `ICLOUD_SMTP_USER`                  | iCloud mailbox address                           |
| `ICLOUD_SMTP_PASS`                  | App-specific password (not your iCloud password) |
| `CONTACT_EMAIL`                     | `info@floraohio.com`                             |
| `NEXT_PUBLIC_SITE_URL`              | `https://floraohio.com`                          |
| `NEXT_PUBLIC_ENABLE_COOKIE_CONSENT` | `true` (if launching with consent banner)        |

## DNS & Domain

- [ ] `floraohio.com` A/CNAME records point to Vercel
- [ ] `www.floraohio.com` redirect to apex verified (configured in vercel.json)
- [ ] SSL certificate provisioned automatically by Vercel
- [ ] HSTS header active (2-year max-age with preload, configured in vercel.json)

## Security Headers (configured in vercel.json)

- [ ] Content-Security-Policy
- [ ] Strict-Transport-Security
- [ ] X-Content-Type-Options: nosniff
- [ ] X-Frame-Options: DENY (SAMEORIGIN for /studio)
- [ ] Referrer-Policy: strict-origin-when-cross-origin
- [ ] Permissions-Policy (camera, microphone, geolocation disabled)

## Sanity CMS

- [ ] Studio accessible at floraohio.com/studio
- [ ] CORS configured for `https://floraohio.com` in Sanity dashboard
- [ ] All albums have title, description, cover image, hero image
- [ ] Featured album(s) configured
- [ ] About page content populated
- [ ] Process page content populated

## Contact Form

- [ ] iCloud SMTP credentials verified (app-specific password)
- [ ] Contact form tested end-to-end in production (receive email at info@floraohio.com)
- [ ] Form validation working (empty fields, invalid email)
- [ ] Success and error states display correctly

## Content

- [ ] All team member photos present (no placeholder gradients)
- [ ] Social links correct (Instagram only)
- [ ] Privacy policy reviewed by owner
- [ ] Terms of service reviewed by owner

## Cookie Consent

- [ ] Banner appears when `NEXT_PUBLIC_ENABLE_COOKIE_CONSENT=true`
- [ ] Accept/reject persists across sessions
- [ ] Vercel Analytics gated by analytics consent category
- [ ] Banner hidden when consent is disabled

## SEO & Social

- [ ] Every page has `og:title` and `og:image` meta tags
- [ ] `/sitemap.xml` returns valid XML with all routes + album pages
- [ ] `/robots.txt` allows / and disallows /studio/
- [ ] Google Search Console verified + sitemap submitted
- [ ] Social sharing tested (Facebook Sharing Debugger, Twitter Card Validator)

## Performance (Lighthouse Mobile)

Run `npm run lighthouse` to generate reports and fill in scores.

| Page              | Performance | Accessibility | Best Practices | SEO |
| ----------------- | ----------- | ------------- | -------------- | --- |
| `/` (home)        | 74          | 91            | 96             | 100 |
| `/work` (gallery) | 75          | 96            | 96             | 100 |
| `/contact`        | 75          | 100           | 96             | 100 |

_Scores recorded 2026-03-31 via `npm run lighthouse` (mobile, simulated throttling)._

## Post-launch

- [ ] Verify Vercel Analytics receiving data in dashboard
- [ ] Submit sitemap to Google Search Console
- [ ] Test on mobile devices (iOS Safari, Android Chrome)
- [ ] Check Core Web Vitals via Vercel Speed Insights after 24h traffic
- [ ] Verify Sanity webhook (if configured) triggers revalidation on content changes
