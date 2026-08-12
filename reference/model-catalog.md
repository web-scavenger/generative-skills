# Media Model Catalog (shared reference)

Used by: `image-generation`, `video-generation`, `image-to-video` skills.

This is a **curated shortlist**, not the full fal.ai/OpenRouter catalogs (fal alone has 500+ models — too many to be useful for quick decisions). Each entry below is picked for a specific reason: cheapest viable, best balance, or top quality for that task.

## Two providers, not one

This catalog now tracks models across **both fal.ai and OpenRouter**, because the same underlying model (e.g. Seedance) can price very differently depending on which platform is forwarding the request and which resolution tier is being compared. Neither platform is "the cheap one" in general — always compare resolution-matched, duration-matched numbers for the specific request before deciding.

**Known traps, learned the hard way:**
- Headline/"starting from" prices are usually the **lowest resolution tier**. A 480p rate and a 1080p rate for the same model can differ by 2x+. Always confirm which resolution the quoted price actually applies to.
- Audio inclusion changes price on some video models — check whether the quoted rate includes audio or not.
- OpenRouter sometimes forwards to a single backing provider for a given model — in that case it's not "OpenRouter routing to the cheapest host," it's just relaying that one host's rate. Worth knowing when explaining the price to the user, since it means the number can move if OpenRouter changes backing providers.

⚠️ **Prices below are approximate and drift constantly.** Before presenting options to the user, query **both** MCP servers live (fal's schema/search tool, OpenRouter's model-listing/pricing tool) for the specific resolution + duration + audio setting requested, and use those numbers — never the table below — for what you actually quote. The table is a starting shortlist of *which models to check*, not a price list to read from.

---

## Text-to-Image

| Model | Check on | Tier | Approx price | Notes |
|---|---|---|---|---|
| Flux Schnell | fal (`fal-ai/flux/schnell`) | Economy | ~$0.003/image | Fast, good for drafts/iteration, lower fidelity. Not currently a priority to check on OpenRouter — fal's rate is already near floor. |
| Flux Kontext Pro | fal (`fal-ai/flux-pro/kontext`) | Balanced | ~$0.04–0.05/image | Supports **reference-image conditioning** — use when the user wants to keep a character/product consistent across images. Check OpenRouter too if available; compare directly. |
| Ideogram v3 | fal + OpenRouter | Balanced | ~$0.03–0.06/image | Best for images containing readable text/logos/typography. Compare both — pricing has been volatile across platforms. |
| Seedream (current version) | fal + OpenRouter | Premium | ~$0.05–0.08/image | Strong general photorealism, good default for marketing hero images. **Always compare both** — same pattern as Seedance below. |
| Nano Banana Pro | fal (check OpenRouter availability) | Premium | ~$0.10–0.15/image | Highest fidelity/4K option, use for final client-facing assets |

**Default recommendation logic:** draft/iterate on Schnell → finalize on Kontext (if consistency needed) or Seedream (if not), whichever platform is cheaper for the final pick.

---

## Text-to-Video

| Model | Check on | Tier | Approx price | Notes |
|---|---|---|---|---|
| Wan (2.x) | fal + OpenRouter | Economy | ~$0.05–0.15/sec, varies heavily by resolution | Cheapest usable option, decent for short social clips. Compare resolution tiers carefully — this is where the 480p-vs-1080p trap is biggest. |
| Kling (v2.6+) | fal + OpenRouter | Balanced | ~$0.10–0.15/sec | Strong motion quality, good value, no native audio by default |
| Seedance (2.0 / 2.5) | fal + OpenRouter | Premium | Varies widely: seen ~$0.07/s (480p) up to ~$0.68/s (1080p+audio) depending on platform and tier | **Always check both platforms live at the exact resolution/duration requested** — this model has shown the largest cross-platform price spread of anything in this catalog. Don't quote a "typical" price for Seedance; look it up fresh every time. |

**Default recommendation logic:** short social clips (≤15s) → Kling for balance; anything client-facing/longer or needing native audio → Seedance (compare platforms first); pure budget testing → Wan.

---

## Image-to-Video (with reference image)

| Model | Check on | Tier | Approx price | Notes |
|---|---|---|---|---|
| Wan (2.x) image-to-video | fal + OpenRouter | Economy | ~$0.05–0.15/sec | Cheapest, decent motion from a still |
| Kling image-to-video | fal + OpenRouter | Balanced | ~$0.10–0.15/sec | Good default — reliably keeps subject likeness from the reference image |
| Seedance image-to-video | fal + OpenRouter | Premium | Same wide spread as text-to-video Seedance above | Best likeness + motion fidelity, use when brand/character consistency really matters. Compare both platforms live — don't assume fal or OpenRouter is cheaper by default. |

**Default recommendation logic:** this is the primary path for "animate my product/character image" requests — Kling is the sensible default unless budget or quality pushes to the edges. For Seedance specifically, the platform choice can change the total cost by 2x+, so it's worth the extra lookup on higher-budget jobs.

---

## Live price-check procedure (all skills follow this before quoting)

1. Identify the candidate model(s) from the tables above for the requested task.
2. For each candidate, query **both** MCP servers where the model is available:
   - fal: use its model search/schema tool to get the current per-unit rate for the exact resolution/tier requested.
   - OpenRouter: use its model listing/pricing tool for the same model, and check whether the listed rate is for the same resolution — if OpenRouter only shows a "starting from" rate, look up the rate for the specific resolution tier needed, not the headline number.
3. Normalize both to the same unit (per-image, or per-second × duration) before comparing — never compare a "starting from" number on one platform against a specific-tier number on the other.
4. Present the cheaper option to the user as part of the options list, but don't hide the other platform's number — showing "fal: $X vs OpenRouter: $Y for the same model" is useful information, not noise.
5. If a model is only available on one platform, note that rather than presenting a false comparison.

## Maintaining this catalog

- Review every 4-6 weeks, or whenever a skill surfaces a model that no longer exists / errors out.
- When fal's `search_models` tool surfaces a clearly better option (cheaper + comparable quality, or same price + better quality) for a task already listed here, update this table rather than mentioning it ad hoc — keeps all 3 skills in sync automatically since they share this file.
