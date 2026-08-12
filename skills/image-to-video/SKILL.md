---
name: image-to-video
description: Use this skill when the user wants to animate an existing image into a video — bringing a product shot, character, or illustration to life while keeping its look consistent. Triggers on requests like "animate this image", "turn this into a video", "make my product photo move", or when a reference image is attached alongside a video request. Requires at least one generation provider to be connected: fal.ai's MCP server (mcp.fal.ai) or OpenRouter's MCP server. This is the primary path for brand/character consistency work — prefer it over text-to-video whenever a source image exists.
---

# Image-to-Video

Animates a reference image via the connected generation provider MCP server. This is the skill to reach for whenever the user already has an image (product photo, character art, generated image from the image-generation skill) and wants motion added while preserving likeness.

**Prerequisite:** both fal.ai MCP and OpenRouter MCP should be connected — see the top-level README. Same platform-spread caveats apply here as text-to-video (Seedance especially). If only one is available, proceed but flag that the other might be cheaper for the same model.

**Shared catalog:** read `reference/model-catalog.md` → "Image-to-Video" section for the current model shortlist, and follow the "Live price-check procedure" section before quoting anything. (Paths in this skill are relative to the repo root, which is your working directory.)

## Workflow

1. **Confirm the source image.** Get the image path/URL if not already clear — this is the one required input that's easy to leave ambiguous when a user says "animate that" referring to something generated earlier in the conversation. Confirm which specific file before proceeding.

2. **Clarify what's missing:**
   - Duration (seconds) — main cost driver, same as text-to-video
   - What kind of motion (subtle/ambient vs dynamic camera/action) — affects prompt phrasing, not just model choice
   - Aspect ratio, if it should differ from the source image's own ratio

3. **Check the catalog, then validate live on both platforms** for the exact resolution/duration needed — run the "Live price-check procedure" from the reference doc.

4. **Present 2-3 priced options with platform noted**, with likeness/consistency quality called out explicitly since that's the whole point of this workflow:
   > For an 8s animation of your product image at 1080p:
   > - **Wan** (fal) — ~$0.10/s → **~$0.80 total** — cheapest, likeness may drift a bit on complex subjects
   > - **Kling** (OpenRouter — cheaper here than fal for this tier) — ~$0.12/s → **~$0.96 total** — reliable likeness, good default
   > - **Seedance** (fal) — ~$0.45/s → **~$3.60 total** — best likeness + motion fidelity, use for final brand assets

5. **Confirm before generating**, same as video-generation — explicit go-ahead on model + total cost.

6. **Generate** via whichever MCP server matches the chosen platform, passing the source image as the conditioning input (check the model schema for the exact param — usually `image_url` or similar, and this can differ between fal and OpenRouter for the same underlying model). These are async — poll for completion, don't go silent.

7. **Finalize: save, log, then report.** Follow the "Finalize procedure" in `reference/output-conventions.md` — save the video into a dated request folder, write the `meta.json` sidecar (recording the source image, duration, resolution, and the fal-vs-OpenRouter `price_check`), and append a line to `generations.jsonl`. Then report the local path and the actual cost. If likeness didn't hold up well on a cheaper model, suggest re-running on the next tier up rather than starting the whole flow over.

## Notes

- If the "reference image" is something the user wants generated first (they don't have one yet), that's actually two steps: use the image-generation skill first (ideally with Flux Kontext for a clean base), then come back to this skill to animate the result.
- Likeness quality genuinely varies by subject complexity (faces vs simple product shots vs illustrated characters) — it's fine to be upfront that the cheaper tier is a gamble on trickier subjects rather than presenting all three as equally safe.
