---
name: style-learning
description: Use this skill when the user wants to teach a channel its voice by example — deriving a reusable style profile from existing scripts, videos, or creators they emulate. Triggers on requests like "learn my style from these scripts", "here are creators I want to sound like", "build a style profile for the EN channel", or "update the UK voice from these examples". It analyzes the examples and writes the channel's style-profile.md, which script-writing and storyboard then read. Do NOT use it to write a script or generate media — it only produces the profile.
---

# Style Learning (examples → channel style profile)

Derives a channel's reusable **style profile** from example material the user provides — their own
past scripts, videos they made, or creators they want to sound like — and writes it to
`channels/<id>/style-profile.md`. That profile is the voice every future script for the channel
inherits: `script-writing` and `storyboard` both read it. This is a producer/consumer split — this
skill *writes* the profile; the others *read* it.

**Prerequisite:** example material to learn from (pasted scripts, links, or files in
`channels/<id>/examples/`). For analyzing *video* links or transcripts, this skill delegates to the
global `deep-research` skill rather than inventing a media pipeline.

**Shared references:** resolve the channel via `reference/channel-registry.md`; write the profile to
the `style-profile` path that registry gives. Structural craft stays in `reference/short-video-craft.md`
— this profile captures **voice**, not structure, so the two don't overlap.

## Workflow

1. **Resolve the channel.** Get the channel id and its `style-profile.md` path from
   `reference/channel-registry.md`. Note the channel language — quoted examples in the profile stay in
   that language even though the profile's labels are English.

2. **Gather the examples.** Accept pasted script text, files already dropped in
   `channels/<id>/examples/`, links to videos, or creator handles. Ask for at least a couple so the
   profile isn't overfit to one piece. Prefer the user's *own* best-performing content when available —
   that's the truest signal of the channel's voice.

3. **Analyze — delegate media, read text directly.** For plain text examples, read them directly. For
   video links / creators / transcripts, invoke the global `deep-research` skill to fetch and summarize
   them, then work from that. Look across examples for what's *consistent*, not one-off.

4. **Extract the voice.** Capture: tone/personality, sentence rhythm and length, preferred hook styles,
   pacing (words per beat), any recurring structural template, vocabulary/slang register, recurring
   phrases or catchphrases, visual style cues (color, framing, on-screen-text style — used by
   `storyboard`), and hard **don'ts**. Quote a few short real examples (in the channel language) to
   anchor each trait — a profile with concrete examples is far more usable than adjectives alone.

5. **Write / update the profile.** Write `channels/<id>/style-profile.md` in the structure below. If a
   profile already exists, update it — reconcile new signal with old rather than overwriting wholesale,
   and note what changed. Keep the audience-facing quotes in the channel language; keep the labels in
   English.

6. **Report.** Summarize the voice in 2–3 sentences, point to the file, and offer to test it by drafting
   a hook via `script-writing`.

## Profile structure (what to write)

```markdown
# Style Profile: <channel id> (<language>)
_Learned from: <what examples>, on <date>._

## Voice & tone
## Sentence rhythm & length
## Hook preferences
## Pacing (words per beat)
## Recurring structure / template
## Vocabulary & register   ← with real short quotes in the channel language
## Catchphrases / signatures
## Visual style cues (for storyboard)
## Don'ts
```

## Notes

- Learn from real examples, don't invent a voice. If the user gives no examples, don't fabricate a
  profile — leave the stub in place and say the channel will run on craft defaults until examples exist.
- Two or three strong examples beat ten mediocre ones. Overfitting to a single viral video produces a
  brittle profile; look for the traits that repeat.
- This is voice only. Retention structure, hook taxonomy, and pacing rules stay in
  `reference/short-video-craft.md` — don't duplicate them into the profile; reference them.
- Refreshing the profile updates every future script for the channel at once — the same leverage as
  editing a shared reference doc. Re-run this whenever the channel's voice evolves.
