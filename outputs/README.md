# Outputs

Generated media lands here, one folder per request, grouped by date:

```
outputs/
  2026-08-12/
    coffee-cup-marble/
      image_01.png        ← git-ignored (the actual media)
      meta.json           ← tracked (self-describing record: model, cost, price-check)
```

The full convention — folder naming, the `meta.json` sidecar schema, and how it ties into the `generations.jsonl` ledger — lives in `../reference/output-conventions.md`. The skills follow that doc automatically after every generation.

**What's tracked in git:** the folder structure and every `meta.json`. **What's ignored:** the media files themselves (`.png`, `.mp4`, etc.) — see the repo `.gitignore`. So the repo stays a lightweight, shareable record of *what was made and what it cost*, without committing large binaries.
