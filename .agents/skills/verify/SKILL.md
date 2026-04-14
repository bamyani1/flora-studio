---
name: verify
description: Run the full verification pipeline — lint, type-check, unit tests, e2e tests, and production build. Use before marking work done or creating PRs.
---

Run the master verification command from the project root:

```bash
npm run verify
```

This chains in order (stops on first failure):
1. `eslint .` — lint
2. `tsc --noEmit` — type-check
3. `vitest run` — unit and component tests
4. `playwright test` — main e2e suite (port 3101)
5. `playwright test --config playwright.consent.config.ts` — cookie consent e2e (port 3102)
6. `next build` — production build

If a step fails, fix it and re-run. E2E tests start their own dev servers on ports 3101 and 3102 — ensure these ports are free.
