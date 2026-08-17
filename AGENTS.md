# Project context

This repo turns a coding agent (Claude Code or Codex CLI) into a **cost-aware media generation
assistant**, backed by the official MCP servers of **both fal.ai and OpenRouter**. There is no custom
server and no npm package — just Agent Skills plus the two providers' own tools, compared live on
price before anything is generated.

**Run agents from the repo root.** All skill paths below are relative to the repo root, which is your
working directory. The skills read/write files here (`outputs/`, `generations.jsonl`, `workflows/`),
so they only work correctly when the repo is the working directory.

## Prerequisite: connect at least one MCP server (both recommended)

The generation skills require **at least one** of **fal.ai's MCP server** (`mcp.fal.ai`) or
**OpenRouter's MCP server** to be connected. Connect **both** to get the live cross-platform price
comparison, which is the whole point — the same model (Seedance especially) can price 2x+
differently between platforms. See `README.md` for the exact connection commands (fal + OpenRouter)
for each tool. If only one is connected, the skills still work but should say so and note the
missing comparison.

## Skills (auto-discovered from `skills/`)

**Generation (back-end)** — turn a prompt or image into media:

- **image-generation** — text-to-image, including reference-image conditioning for consistent
  characters/products.
- **video-generation** — text-to-video (no starting image). More expensive; always confirm cost.
- **image-to-video** — animate an existing image while preserving its look.
- **workflow-spotter** — meta-skill: notices repeated multi-step sequences and offers to save them as
  a named workflow or a new skill.

Each generation skill follows the same shape: clarify what's missing (format, duration, budget
priority) → check **both** fal and OpenRouter for live pricing on the specific request → show 2–3
priced options noting the platform → confirm cost → generate via the matching provider's tools →
save + log. Skills never reimplement generation logic; the providers' MCP servers do that.

**Creative front-end** — plan a short explainer video *before* generation. These write text, delegate
research to the global `deep-research` skill, and (in storyboard) hand off to the generation skills
above. Everything is scoped to a **channel** (language + learned voice) resolved from
`reference/channel-registry.md`:

- **topic-research** — research a topic → distill into a short-video brief (angles, facts, hooks).
- **script-writing** — brief/topic → a hook-first scenario in the channel's language.
- **style-learning** — learn a channel's voice from example scripts/videos → write its style profile.
- **storyboard** — script → timed beats → reuse-asset vs generate per beat → hand off to the
  generation skills (never generates or price-checks itself).

## Shared references

- `reference/model-catalog.md` — curated model shortlist (price/quality notes) + the live
  cross-platform price-check procedure. All three generation skills read from this one file.
- `reference/output-conventions.md` — where generated media goes and how every generation is recorded
  (dated `outputs/<date>/<slug>/` folders + `meta.json` sidecar + append-only `generations.jsonl`
  ledger). Follow its "Finalize procedure" after every generation.
- `reference/channel-registry.md` — first-class list of channels (id → language, style profile, format
  defaults). Every front-end skill resolves the channel here first, so language is never hardcoded.
- `reference/short-video-craft.md` — the encoded craft of short video (hook taxonomy, retention curve,
  pacing, CTA). Read by `script-writing` and `storyboard`.
- `reference/content-conventions.md` — where creative artifacts save and their formats: dated
  `content/<date>/<channel>-<slug>/` folders (`brief.md` / `script.md` / `storyboard.md` + `meta.json`),
  plus the language rule. Per-channel config and assets live under `channels/<id>/`.
