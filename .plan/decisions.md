# Decision Log — Bamyan Storyworks

Append-only. Never edit existing entries — only add new rows.

| ID | Phase | Decision | Reason |
|----|-------|----------|--------|
| 1 | A | Upgraded `next-sanity` from ^9.0.0 to ^12.0.0, `sanity` from ^3.80.0 to ^5.16.0 | next-sanity v9 has peer dep on Next.js ^14/^15, v12 supports Next.js 16 |
| 2 | A | Added `forceConsistentCasingInFileNames: false` to tsconfig.json | GSAP ships Flip.js (uppercase) but types/flip.d.ts (lowercase), causing TS error on case-insensitive macOS |
| 3 | A | Sanity project not created on sanity.io — schemas only | No Sanity credentials yet; schemas/client/queries are ready, just needs project ID in .env |
