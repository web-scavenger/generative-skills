---
name: image-generation
description: Use this skill when the user wants to create, generate, or draft an image from a text description (social posts, marketing assets, product shots, illustrations, thumbnails, etc.), including cases where they provide a reference image to keep a character or product consistent. Triggers on requests like "generate an image of...", "make me a graphic for...", "create a product shot...", or "same character but...". Requires at least one generation provider to be connected: fal.ai's MCP server (mcp.fal.ai) or OpenRouter's MCP server. Do NOT use for animating a still image into a video — that's the image-to-video skill.
---

# Image Generation

Generates images via the connected generation provider MCP server, using a curated model shortlist so the user sees priced options instead of one silent default.

**Prerequisite:** both fal.ai MCP and OpenRouter MCP should be connected — see the top-level README for setup commands for each. If only one is connected, proceed with that one but tell the user you're only seeing prices from one platform and the other might be cheaper. If neither is available, tell the user to connect at least one before continuing — don't attempt a workaround.

**Shared catalog:** read `reference/model-catalog.md` → "Text-to-Image" section for the current model shortlist. Before quoting, follow the "Price cache procedure" (reuse a fresh <24h cached price from `price-cache.json`) and fall back to the "Live price-check procedure" on a miss/stale entry. (Paths in this skill are relative to the repo root, which is your working directory.)

## Workflow

1. **Clarify only what's missing.** Don't ask everything every time — infer from context first. Typically worth confirming if not already stated:
   - Aspect ratio / format (the user works across multiple formats — square, 9:16, 16:9 — don't assume)
   - Whether this needs to match a reference image (character/product consistency) — if they've attached or mentioned a reference image, this is implied, confirm rather than ask from scratch
   - Rough intent: quick draft to iterate on, or a final asset — this drives which tier to lead with

2. **Check the cache, then validate live if needed.** Pull the Text-to-Image shortlist from the reference doc, then run the "Price cache procedure": look each candidate up in `price-cache.json`. Use a fresh (<24h) cached price directly — and tell the user it's "as of `<time>`" with a "want me to re-check live?" offer. On a miss/stale entry (or if the user asks for fresh prices), run the "Live price-check procedure" across fal and OpenRouter and update the cache. Use whichever number is genuinely cheaper for that specific case; the catalog's prices are approximate starting points, not what you quote.

3. **Present 2-3 priced options, not one, and note the platform.** E.g.:
   > - **Flux Schnell** (fal) — ~$0.003/image — fast draft quality, good for iterating on composition first
   > - **Flux Kontext Pro** (fal) — ~$0.045/image — supports your reference image for consistent character/product look
   > - **Seedream** (OpenRouter — $0.052/image vs fal's $0.068/image for the same tier) — highest photorealism, best for final marketing assets

   Let the user pick — don't default silently to the cheapest or the best. If they've stated a clear priority before (speed, quality, cost), you can lead with a recommendation, but still show the alternatives.

4. **Confirm the estimated total** before generating, especially if generating multiple images (`count > 1` multiplies cost linearly — say so explicitly).

5. **Generate** via whichever MCP server's inference tool matches the chosen (cheaper/preferred) platform for that model. If a reference image was provided, pass it through as the conditioning image parameter (check the model's schema for the exact param name — this varies by model and by platform).

6. **Finalize: save, log, then report.** Follow the "Finalize procedure" in `reference/output-conventions.md` — save the image(s) into a dated request folder, write the `meta.json` sidecar (including the fal-vs-OpenRouter `price_check` you already did), and append one line per image to `generations.jsonl`. Then report the local path(s) and the actual cost. If the user will likely want variations or a different aspect ratio next, mention that's a quick follow-up rather than starting over.

## Notes

- For "keep this character consistent across multiple images," always lean toward Flux Kontext (or whichever catalog model explicitly supports reference conditioning) rather than plain text-to-image — regenerating from text alone won't preserve likeness.
- If the user's request is vague ("make something for Instagram"), it's fine to propose a concrete direction (subject, style, format) as part of the options rather than asking an open-ended question first — give them something to react to.
