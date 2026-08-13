---
name: video-generation
description: Use this skill when the user wants to create a video directly from a text description, with no starting image (social clips, ads, promos, cinematic shots described in words). Triggers on requests like "generate a video of...", "make a 15-second clip showing...", "create an ad video for...". Requires at least one generation provider to be connected: fal.ai's MCP server (mcp.fal.ai) or OpenRouter's MCP server. Do NOT use when the user provides or references an existing image to animate — that's the image-to-video skill. Video is more expensive than image generation, so always confirm cost before generating.
---

# Video Generation (text-to-video)

Generates video via the connected generation provider MCP server, using a curated model shortlist. Video costs scale with duration, so the cost-confirmation step here matters more than in image generation — don't skip it.

**Prerequisite:** both fal.ai MCP and OpenRouter MCP should be connected — see the top-level README. Video pricing has shown the biggest cross-platform spread of anything in this catalog (Seedance especially), so having both connected matters more here than for images. If only one is available, proceed but flag that the other platform might be significantly cheaper for the same model.

**Shared catalog:** read `reference/model-catalog.md` → "Text-to-Video" section for the current model shortlist. Follow the "Price cache procedure" (reuse fresh <24h prices from `price-cache.json`) then the "Live price-check procedure" on a miss — the latter is what catches the resolution-tier and platform-spread traps. **Note:** Seedance and any high-cost job (≳ $1.00) are *always* re-verified live, never served from cache. (Paths in this skill are relative to the repo root, which is your working directory.)

## Workflow

1. **Clarify what actually changes the cost/output**, only what's missing:
   - Duration (seconds) — this is the main cost driver, always needed
   - Aspect ratio / format (vertical for TikTok/Reels vs horizontal vs square)
   - Whether native audio is needed (some models charge more for audio-inclusive generation — check the catalog note)
   - Quick draft vs final/client-facing asset — drives which tier to lead with

2. **Check the cache, then validate live at the exact resolution requested.** Run the "Price cache procedure": reuse a fresh (<24h) price from `price-cache.json` for stable models like Wan/Kling, telling the user it's "as of `<time>`" with a re-check offer. For **Seedance** and any **high-cost job**, skip the cache and run the "Live price-check procedure" — query fal and OpenRouter matched on resolution and audio setting, not the headline "starting from" number. This is where Wan/Seedance in particular can show a 2x+ gap between platforms depending on which resolution tier you actually need. Update the cache with whatever you fetch live.

3. **Present 2-3 priced options with the total for THIS request, and note the platform + resolution the price is based on:**
   > For a 15s vertical clip at 1080p:
   > - **Wan** (fal) — ~$0.10/s → **~$1.50 total** — cheapest at this resolution, decent motion, no audio
   > - **Kling** (OpenRouter) — ~$0.12/s → **~$1.80 total** — strong motion quality, good default
   > - **Seedance** (fal, 1080p+audio tier — checked against OpenRouter's 720p-only listing, not directly comparable) — ~$0.45/s → **~$6.75 total** — cinematic quality, native audio

   Always show the multiplied-out total at the resolution actually being used, not a generic per-second rate — this is the single most common way to misquote video cost.

4. **Confirm before generating.** Video jobs cost real money and take longer to run — get an explicit go-ahead on the specific model + total cost, don't proceed on an implied choice.

5. **Generate** via whichever MCP server's inference tool matches the chosen platform for that model. Video jobs on both fal and OpenRouter are async/queue-based — expect a job id and poll for completion rather than a synchronous result; tell the user it's processing rather than going silent.

6. **Finalize: save, log, then report.** Follow the "Finalize procedure" in `reference/output-conventions.md` — save the video into a dated request folder, write the `meta.json` sidecar (recording duration, resolution, audio setting, and the fal-vs-OpenRouter `price_check`), and append a line to `generations.jsonl`. Then report the local path and the actual cost, and mention duration/format used so the user can sanity-check it matches what they asked for.

## Notes

- If the user hasn't specified duration, don't assume — a "quick video" could mean 5s or 30s and the cost difference is large. Ask, or propose a specific default (e.g. "I'll do 10s unless you want longer") and let them correct it.
- If this request is really about animating something the user already has an image of, redirect to the image-to-video skill instead — it'll produce better likeness/consistency than describing the same thing in text.
