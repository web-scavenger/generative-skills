# Media Generation Skills for Claude Code & Codex

Agent Skills that turn **Claude Code or Codex CLI** into a cost-aware media generation assistant, backed by **both fal.ai and OpenRouter's** official MCP servers. No custom server, no npm package — just Skills + two providers' own tools, compared live before anything gets generated.

`SKILL.md` is a shared open standard, so the same skill files work in both tools. This repo commits the discovery wiring for both (`.claude/skills` and `.codex/skills`), so cloning it and running either agent from the repo root is all it takes.

## What this is

- `skills/image-generation` — text-to-image, with reference-image support for consistent characters/products
- `skills/video-generation` — text-to-video
- `skills/image-to-video` — animate an existing image while preserving its look
- `skills/workflow-spotter` — a meta-skill: notices multi-step sequences you repeat and offers to save them as a reusable workflow or new skill (you don't have to author workflows yourself)
- `reference/model-catalog.md` — shared curated model shortlist (price/quality notes) all three generation skills read from, including the live cross-platform price-check procedure
- `reference/output-conventions.md` — shared rules for **where generated media goes** and **how every generation is recorded** (dated folders + `meta.json` sidecar + append-only ledger)

Each generation skill's job is the same shape: figure out what's missing (format, duration, budget priority), check **both** fal and OpenRouter for real current pricing on the specific request, show 2-3 priced options with the platform noted, confirm cost, call whichever provider's generation tools match the chosen option, then save the result into a dated folder and log the cost. The skills never reimplement generation logic — the providers' own MCP servers do that part.

**Why two providers:** the same model (Seedance especially) can price very differently between platforms depending on resolution tier and backing host — sometimes by 2x or more. One provider alone risks silently overpaying. See `reference/model-catalog.md` for the specifics.

## Setup

### 1. fal.ai

Get an API key at [fal.ai/dashboard/keys](https://fal.ai/dashboard/keys). Like OpenRouter (below), this repo is pre-wired for **API-key auth from a local `.env` file** — it keeps the secret out of git and needs no per-machine `claude mcp add`.

The generation skills need fal's **model-inference** MCP server — `https://mcp.fal.ai/mcp` — which searches models, reads their schemas, and actually runs image/video inference. (Note the auth scheme: this server uses `Authorization: Bearer ${FAL_KEY}`.) This is a **different server** from fal's read-only *Platform* API at `https://api.fal.ai/v1/mcp/platform`, which only exposes account/usage/monitoring tools and **cannot generate anything** — don't wire that one expecting generation. This repo commits both: the generation server as `fal`, plus the platform server as `fal-platform` for optional spend/usage debugging.
```json
{
  "mcpServers": {
    "fal": {
      "type": "http",
      "url": "https://mcp.fal.ai/mcp",
      "headers": { "Authorization": "Bearer ${FAL_KEY}" }
    },
    "fal-platform": {
      "type": "http",
      "url": "https://api.fal.ai/v1/mcp/platform",
      "headers": { "Authorization": "Key ${FAL_KEY}" }
    }
  }
}
```

So the only per-machine setup is dropping your key into `.env` (see the shared `set -a; source .env; set +a` step under OpenRouter below — it exports both keys at once). On first launch, run `/mcp` to approve the project-scoped servers. The `fal` (generation) server is the one the skills require; `fal-platform` is optional.

Prefer a one-off command instead of the committed config? Register the generation server directly with **Claude Code** (this stores it in your local config, not the shared `.mcp.json`):
```bash
claude mcp add --transport http fal \
  https://mcp.fal.ai/mcp \
  --header "Authorization: Bearer YOUR_FAL_KEY"
```

For **Codex**, register the same server with its own CLI. The generation server uses the `Bearer` auth scheme:
```bash
codex mcp add fal --url https://mcp.fal.ai/mcp \
  --bearer-token-env-var FAL_KEY
```
Flag names for auth headers evolve — check `codex mcp add --help` and the [Codex MCP docs](https://developers.openai.com/codex/mcp) for the current syntax (raw `http_headers` vs. bearer-token env var). Codex stores this in `~/.codex/config.toml` (global) or `.codex/config.toml` (project, trusted only). The platform server, if you want it, uses the `Key` scheme instead: `--http-header "Authorization: Key YOUR_FAL_KEY"`.

### 2. OpenRouter

Get an API key at [openrouter.ai/keys](https://openrouter.ai/keys). OpenRouter's MCP server supports OAuth discovery, but this repo is pre-wired for **API-key auth from a local `.env` file** instead — more convenient for local dev, and it keeps the secret out of git.

This repo commits `.mcp.json` with the OpenRouter server already registered for Claude Code, reading the key from an environment variable:
```json
{
  "mcpServers": {
    "openrouter": {
      "type": "http",
      "url": "https://mcp.openrouter.ai/mcp",
      "headers": { "Authorization": "Bearer ${OPEN_ROUTER_AUTH_TOKEN}" }
    }
  }
}
```

So the only per-machine setup is dropping your key into a gitignored `.env` (copy `.env.example` → `.env`):
```bash
cp .env.example .env
# then edit .env and set OPEN_ROUTER_AUTH_TOKEN=sk-or-...
```

**Important:** Claude Code does *not* auto-load `.env`. The `${OPEN_ROUTER_AUTH_TOKEN}` reference is expanded from your shell environment when `claude` launches, so export the file's contents before starting the agent:
```bash
set -a; source .env; set +a   # export every var in .env into the shell
claude                        # launch from the repo root
```
`set -a` makes `source` export each variable so Claude Code (and through it the MCP client) can see it. On first launch, run `/mcp` to approve the project-scoped `openrouter` server; after that it connects via the `Bearer` header with no OAuth flow. To make this automatic, put the `set -a; source .env; set +a` line in a `direnv` `.envrc` for this directory.

For **Codex**, register the same server with its own CLI (Codex supports Streamable HTTP MCP servers and reading the token from an env var):
```bash
export OPEN_ROUTER_AUTH_TOKEN=sk-or-...
codex mcp add openrouter --url https://mcp.openrouter.ai/mcp --bearer-token-env-var OPEN_ROUTER_AUTH_TOKEN
```
Flag names for auth headers evolve — check `codex mcp add --help` and the [Codex MCP docs](https://developers.openai.com/codex/mcp) for current syntax. If you'd rather use OpenRouter's OAuth path instead of a key, see their MCP docs (openrouter.ai/docs/mcp-server).

### 3. Install the skills — nothing to copy

This repo already carries the discovery wiring for both tools: `.claude/skills` and `.codex/skills` are symlinks to the canonical `skills/` folder, so both agents auto-discover all four skills from one physical copy. **Just clone the repo and run your agent from the repo root** (`claude` or `codex`) — no copying, no per-machine setup.

The skills read and write files in this repo (`outputs/`, `generations.jsonl`, `workflows/`), so always run from the repo root — that's the working directory the skills assume.

> **Windows note:** the wiring uses symlinks (fine on macOS/Linux, and on Windows with Developer Mode / `git config core.symlinks true`). If symlinks aren't available, copy `skills/` into `.claude/skills/` and `.codex/skills/` instead — you then keep those copies in sync manually.

### 4. Verify

Restart the agent from the repo root. Try: *"Generate an image of a coffee cup on a marble counter for Instagram"* — the agent should ask a couple quick questions, then show you priced options **noting which platform each price came from** before generating anything. In Codex you can also invoke a skill explicitly (`$image-generation ...`) or pick from the `/skills` selector.

If only one provider connects successfully, the skills will still work but will say so explicitly — you'll be missing the cross-platform comparison that's the main point of running both.

## Outputs & cost ledger

Every generation is saved and recorded automatically — this is what makes the repo something to build on rather than a series of throwaway calls.

- **Media** lands in `outputs/<date>/<slug>/` with a `meta.json` sidecar describing what made it, at what cost, and what *both* platforms quoted (the audit trail for the cross-provider comparison). The media files are git-ignored; the sidecars are tracked, so the repo stays a lightweight record without committing large binaries.
- **`generations.jsonl`** is an append-only ledger — one line per output file — so you can answer "what did I spend this month" or "what prompt made that clip" from one place.

The full schema and the "Finalize" procedure all skills follow live in `reference/output-conventions.md`.

## Workflows (grow into this later)

`workflows/` is empty by design. Rather than hand-authoring pipelines up front, the `workflow-spotter` skill watches for sequences you repeat (e.g. generate a product image → animate it at 9:16) and offers to save them as named, one-instruction workflows. You can also just ask: *"save what we just did as a workflow."* See `workflows/README.md` for the shape.

## Client compatibility

These skills work as-is with **Claude Code** and **Codex CLI** (both auto-discover the committed skill wiring), and with other SKILL.md-aware tools if you point them at `skills/`. fal's official MCP server currently requires OAuth for Claude Desktop, which isn't supported there yet, so Claude Desktop isn't a fit today. OpenRouter's MCP server's OAuth support may behave differently across clients — verify each provider's current client compatibility if you switch clients later.

## Maintaining the catalog

Model prices and line-ups on both platforms shift every few weeks — video pricing (Seedance especially) has shown the most volatility. Review `reference/model-catalog.md` periodically. Since all three skills read from this one file, updating it here keeps all three in sync without touching the skill files themselves. The live price-check procedure in that file means stale numbers in the table are a minor issue (skills verify live anyway) — but keep the *shortlist of which models to check* reasonably current so skills aren't checking models that no longer exist.
