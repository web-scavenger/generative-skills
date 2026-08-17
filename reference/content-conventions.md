# Content & Artifact Conventions (shared reference)

Used by: `topic-research`, `script-writing`, `style-learning`, `storyboard` skills.

This is the creative-side twin of `output-conventions.md`. Where that file governs **generated media**
(`outputs/`), this one governs the **text artifacts** that come before generation — the research
brief, the script, and the storyboard. It defines **where they save**, **their formats**, and the
**language rule** that keeps two channels from bleeding into each other. Editing this file changes the
save behavior of all four front-end skills at once.

The point: every video's paper trail — why this topic, what the hook is, what each shot should be — is
self-describing and lives next to its siblings, so months later you can see what made a video and in
which channel's voice.

---

## The language rule (read this first)

**Every output artifact is written in the channel's language** (from `channel-registry.md`): the
brief's hooks and angles, every script line, and all on-screen-text in the storyboard. The skill docs,
reference docs, `meta.json` field *values that are metadata* (dates, ids, model names), and section
*labels* stay in English — but the creative content the viewer would see or hear is in the channel
language. When in doubt, ask: "would the audience see/hear this?" If yes → channel language.

Resolve the channel (and thus the language) as step 1 of every skill. Never infer language from the
topic or from the user's prompt language.

---

## 1. Where artifacts land

One folder per **video idea**, grouped by date, tagged with the channel:

```
content/<YYYY-MM-DD>/<channel>-<slug>/
    brief.md          ← research distilled for this video (from topic-research)
    script.md         ← the scenario / script (from script-writing)
    storyboard.md     ← shot-by-shot plan + generation handoff (from storyboard)
    meta.json         ← one sidecar describing the idea + its channel + provenance
```

- `<YYYY-MM-DD>` — the date the idea was started (from the environment's current date, never guess).
- `<channel>` — the channel id from `channel-registry.md` (e.g. `en`, `uk`).
- `<slug>` — short kebab-case summary of the topic, ≤5 words, in **English** (it's a filename, not
  audience-facing) — e.g. `why-coffee-crashes`, `black-holes-explained`. De-dupe with `-2`, `-3`.
- The three `.md` files accrue over time — a folder may have only `brief.md` at first; `script.md` and
  `storyboard.md` join as the pipeline advances. All three and `meta.json` are **tracked in git**
  (they're the lightweight creative record). Any media the storyboard generates lands in `outputs/`
  (git-ignored) per `output-conventions.md`, not here.

---

## 2. Artifact formats

Keep these light and skimmable. Exact headings matter less than having the load-bearing pieces.

**`brief.md`** (from `topic-research`)
```markdown
# Brief: <topic> — <channel>
**Channel:** uk   **Language:** Ukrainian   **Date:** 2026-08-17

## Angles (why anyone cares)
- <angle>, <angle>, ...

## Surprising facts (hook fuel)
- <fact> — <source ref>

## Candidate hooks   ← in the channel language
1. <hook>
2. <hook>

## Sources
- <title> — <url>
```

**`script.md`** (from `script-writing`) — all audience-facing text in the channel language:
```markdown
# Script: <topic> — <channel>
**Channel:** uk   **Format:** 9:16   **Target length:** ~30s   **~words:** 84

## Hook (0–2s)
<hook line>   |  on-screen: <text>

## Beats
1. (2–5s) <line>   |  on-screen: <text>
2. (…)   <line>   |  on-screen: <text>
...

## CTA
<cta line>
```

**`storyboard.md`** (from `storyboard`) — one row per beat; see `storyboard` skill for how the
handoff column is chosen:
```markdown
# Storyboard: <topic> — <channel>
**Source script:** script.md

| # | Time | Script beat | Visual | Handoff | Reference asset / prompt | Target output |
|---|------|-------------|--------|---------|--------------------------|---------------|
| 1 | 0–2s | <hook>      | <shot> | image-to-video | channels/uk/references/host.png | outputs/2026-08-17/why-coffee-crashes/ |
| 2 | 2–5s | <beat>      | <shot> | image-generation→image-to-video | <prompt> | outputs/… |
```

---

## 3. The sidecar: `meta.json`

One per idea folder. Records the idea, its channel, and which artifacts exist — the provenance link
between the creative side and the generated media. Shape:

```json
{
  "date": "2026-08-17",
  "channel": "uk",
  "language": "uk",
  "topic": "why coffee makes you crash",
  "slug": "why-coffee-crashes",
  "artifacts": {
    "brief": "brief.md",
    "script": "script.md",
    "storyboard": "storyboard.md"
  },
  "format": "9:16",
  "target_length_s": 30,
  "generated_into": ["outputs/2026-08-17/why-coffee-crashes/"]
}
```

- `channel` / `language` — copied from `channel-registry.md` at creation; how any downstream skill
  re-derives voice/language without asking again.
- `artifacts` — only the files that actually exist yet; add keys as the pipeline advances.
- `generated_into` — the `outputs/` folder(s) the storyboard generated into, filled once generation
  runs. Its twin is the `source_storyboard` back-reference written into each generated
  `outputs/.../meta.json` (see §4), so the trail is navigable both directions.

---

## 4. Linking to generated media (`outputs/`)

The front-end artifacts and the generated media are two dated trees on purpose (different lifecycles,
different git rules). Keep them linked so nothing is orphaned:

- In `content/.../meta.json`, list each `outputs/.../` folder produced in `generated_into`.
- When `storyboard` hands a beat to a generation skill, pass along the source storyboard path; the
  generation skill's Finalize step records it as `source_storyboard` in the media's `outputs/.../meta.json`.

That's the only coupling between the two trees — a path in each direction.

---

## 5. Finalize procedure (every front-end skill runs the relevant part)

1. **Resolve the channel** (id → language + style profile) per `channel-registry.md`. Do this before
   writing any audience-facing words.
2. **Resolve the folder** — `content/<today>/<channel>-<slug>/`, creating it if needed; de-dupe the slug.
3. **Write your artifact** (`brief.md` / `script.md` / `storyboard.md`) in the channel language, in the
   format above.
4. **Write / update `meta.json`** — create it if you're first; otherwise add your artifact to
   `artifacts` and fill any fields you now know (format, target length).
5. **Report to the user** with the local path and a one-line summary (for a script: the hook + length;
   for a brief: the top angle + hook count).

`style-learning` is the exception — it writes to `channels/<id>/style-profile.md`, not to `content/`
(see that skill). It still resolves the channel first.
