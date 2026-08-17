# Channel Registry (shared reference)

Used by: `topic-research`, `script-writing`, `style-learning`, `storyboard` skills.

This is the **single source of truth for what a "channel" is.** Every front-end skill's first
Workflow step is "Resolve the channel" — it takes a channel id, looks it up here, and reads back the
channel's **language**, its **style-profile path**, and its **format defaults**. Nothing about a
channel (least of all its language) is hardcoded in a skill; it all comes from this table. Editing a
row here re-points every skill at once — same leverage pattern as `model-catalog.md`.

The point: the user runs more than one channel in parallel, and the single most common failure is a
skill writing a script in the wrong language. Making the channel a first-class, looked-up value is how
that's prevented.

---

## Channels

| id | Name | Language | Style profile | Default format | Default length | Niche / notes |
|---|---|---|---|---|---|---|
| `en` | (name TBD) | English (`en`) | `channels/en/style-profile.md` | 9:16 vertical | 20–40s | Explainer TikToks, English audience |
| `uk` | (name TBD) | Ukrainian (`uk`) | `channels/uk/style-profile.md` | 9:16 vertical | 20–40s | Explainer TikToks, Ukrainian audience |

- **id** — short, stable, kebab-case. Used verbatim in artifact folder names (`content/<date>/<id>-<slug>/`)
  and in the `channel` field of every `meta.json`. Don't rename an id lightly — it's a key.
- **Language** — the language every *output artifact* for this channel is written in (hooks, script
  lines, on-screen text). Skill docs and reference docs themselves stay in English regardless.
- **Style profile** — path to the channel's learned voice, produced by `style-learning`. If the file
  is still a stub (never learned), skills should say so and fall back to `short-video-craft.md` defaults
  rather than inventing a voice.
- **Default format / length** — the starting assumption for `script-writing` and `storyboard`; always
  overridable per request.

---

## How a skill resolves a channel

1. Get the channel id from the user (or infer it — e.g. "the UK channel" → `uk`). If ambiguous or the
   user runs only one channel this session, ask which one; never guess the language.
2. Read this table for that id → `language`, `style-profile` path, format defaults.
3. Read the style profile at that path. If it's a stub, note that and proceed on craft defaults.
4. Write every output artifact in `language`; stamp `channel: <id>` into the artifact's `meta.json`.

## Adding or renaming a channel

Add a row (unique id, language, a `channels/<id>/style-profile.md` path) and create the matching
`channels/<id>/` folder with a stub profile. That's the whole registration step — the skills pick it
up on the next run.
