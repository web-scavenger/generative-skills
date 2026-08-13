# Output & Ledger Conventions (shared reference)

Used by: `image-generation`, `video-generation`, `image-to-video` skills (and any future workflow).

This is the single source of truth for **where generated media goes** and **how every generation is recorded**. All three generation skills follow the "Finalize" procedure at the bottom before telling the user they're done. Editing this file changes the behavior of all skills at once — same pattern as `model-catalog.md`.

The point: every asset should be self-describing (you can tell months later what made it and what it cost), and every dollar spent should be queryable from one place.

---

## 1. Where media lands

One folder per **request**, grouped by date:

```
outputs/<YYYY-MM-DD>/<slug>/
    image_01.png          ← the media file(s)
    image_02.png
    meta.json             ← one sidecar describing the whole request
```

- `<YYYY-MM-DD>` — the date the request was made (get it from the environment's current date, never guess).
- `<slug>` — short kebab-case summary of the subject, ≤5 words. e.g. `coffee-cup-marble`, `product-spin-9x16`, `mascot-wave`. If the same slug already exists for today, append `-2`, `-3`.
- Media files — named `<type>_NN.<ext>`, zero-padded, starting at `01`:
  - images → `image_01.png`
  - videos → `video_01.mp4`
  - Keep the extension the provider actually returned.

Downloaded media (the actual `.png` / `.mp4`) is git-ignored — see `.gitignore`. The `meta.json` sidecars are **not** ignored: they're the lightweight, shareable record of what was made.

---

## 2. The sidecar: `meta.json`

One per request folder. Describes the request once, then lists each output file with the exact params and price that produced it. Shape:

```json
{
  "date": "2026-08-12",
  "task": "image-generation",
  "request": "coffee cup on a marble counter, for Instagram",
  "slug": "coffee-cup-marble",
  "outputs": [
    {
      "file": "image_01.png",
      "model": "Seedream",
      "platform": "fal",
      "prompt": "top-down coffee cup on white marble, soft daylight, minimal",
      "resolution": "1024x1024",
      "duration_s": null,
      "audio": null,
      "unit_price_usd": 0.068,
      "quantity": 1,
      "cost_usd": 0.068,
      "price_check": { "fal": 0.068, "openrouter": 0.072 }
    }
  ]
}
```

Field notes:
- `task` — one of `image-generation`, `video-generation`, `image-to-video`.
- `duration_s`, `audio` — `null` for images; filled for video.
- `unit_price_usd` — per-image, or per-second, matching how the model is billed.
- `cost_usd` — the actual total for this file (`unit_price × quantity`, or `unit_price × duration_s`).
- `price_check` — what **both** platforms quoted at the requested tier, even the one you didn't pick. This is the audit trail that proves the cross-provider comparison happened, and lets you spot when the "cheaper" platform flips over time. If a model was only on one platform, record the other as `null`.

---

## 3. The ledger: `generations.jsonl`

Append-only, at the repo root. **One line per output file** (not per request) so cost math is trivial. Each line is a flat JSON object:

```json
{"date":"2026-08-12","task":"image-generation","model":"Seedream","platform":"fal","resolution":"1024x1024","duration_s":null,"audio":null,"unit_price_usd":0.068,"quantity":1,"cost_usd":0.068,"alt_platform_price_usd":0.072,"output_path":"outputs/2026-08-12/coffee-cup-marble/image_01.png","prompt":"top-down coffee cup on white marble, soft daylight, minimal"}
```

- `alt_platform_price_usd` — the price the platform you *didn't* choose quoted for the same tier (or `null` if unavailable). Mirrors `price_check` in the sidecar; kept flat here for easy querying.
- Never rewrite existing lines — only append. The ledger is history.

Handy queries once it has data:
- Total spend this month: `grep '"date":"2026-08"' generations.jsonl` then sum `cost_usd`.
- What made a given clip: search the `output_path`.
- Whether you're consistently picking the pricier platform: compare `cost_usd` vs `alt_platform_price_usd` across lines.

---

## 4. Finalize procedure (every skill runs this after a successful generation)

1. **Resolve the folder** — `outputs/<today>/<slug>/`, creating it if needed; de-dupe the slug if it already exists.
2. **Save the media** into it with the `<type>_NN.<ext>` naming above.
3. **Write / update `meta.json`** — add an entry to `outputs` for each file produced, including the `price_check` from the live comparison you already did before generating.
4. **Append to `generations.jsonl`** — one line per output file.
5. **Refresh the price cache** — upsert the price(s) you just used into `price-cache.json` with a fresh `fetched_at` (see the "Price cache procedure" in `model-catalog.md`). A generation always did a fresh live check right before running, so this keeps the cache warm for the next request at no extra cost.
6. **Report to the user** with the local path(s) and the actual cost. If more than one file, give the folder path.

Steps 1–5 are cheap bookkeeping; don't skip them, because the ledger is only useful if it's complete. If a generation *fails* or the user cancels, write nothing — the ledger records spend, not attempts.
