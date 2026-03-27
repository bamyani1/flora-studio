---
name: verify
description: Run full lint and type-check to verify code quality before committing
---

Run the following commands in sequence to verify the codebase:

```bash
npm run lint && npm run type-check
```

Report any errors found. If both pass, confirm the codebase is clean.
