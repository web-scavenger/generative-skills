---
name: script-writing
description: Use this skill when the user wants to write a short-form video scenario/script with a strong hook — for TikTok, Reels, or Shorts — for a specific channel. Triggers on requests like "write a script about X for the UK channel", "turn this brief into a scenario", "give me a hook and script for X", or "make this into a TikTok". It writes the script in the channel's language, applying the encoded short-video craft and the channel's learned style. Do NOT use it to research a topic (that's topic-research) or to plan shots/visuals (that's storyboard).
---

# Script Writing (brief/topic → hook-first scenario)

Writes a full short-video scenario — hook, retention-structured beats, and a single CTA — **in the
channel's language**, applying the craft in `reference/short-video-craft.md` and the channel's voice
from its style profile. Produces words only; visuals come later in `storyboard`.

**Prerequisite:** none for the MCP servers (this skill writes text). You need the channel (for
language + voice) and ideally a brief from `topic-research`; a raw topic works too, but a brief makes
a sharper script.

**Shared references:** resolve the channel via `reference/channel-registry.md`; structure the script
with `reference/short-video-craft.md`; apply the channel's `style-profile.md`; save per
`reference/content-conventions.md` (the `script.md` format).

## Workflow

1. **Resolve the channel.** Get the channel id, look it up in `reference/channel-registry.md` for its
   **language**, default **format** (9:16) and **target length**, and read its `style-profile.md`. The
   entire script — hook, beats, on-screen text, CTA — is written in this language. If the style profile
   is still a stub, say so and fall back to the craft defaults for voice.

2. **Gather the material.** Prefer an existing `brief.md` in the idea's `content/` folder (angles,
   facts, candidate hooks). If there's no brief, work from the topic the user gives — but if the topic
   is thin, suggest running `topic-research` first for better hooks.

3. **Confirm length/format if unset.** Words scale with duration (~2.5–3 words/sec, per
   `short-video-craft.md` §3). If the user hasn't said, propose the channel default (e.g. "~30s, so
   ~80 words") and let them adjust — this drives how much script to write.

4. **Choose and sharpen the hook.** Pick the strongest candidate hook (or write one) using the taxonomy
   in `short-video-craft.md` §2 — specific over vague. The hook is the 0–2s open; make it the first
   words, not a greeting.

5. **Write the script to the retention curve.** Follow `short-video-craft.md` §1: hook → setup/stakes →
   beats with a re-hook every ~5–8s → clear payoff of the hook's promise → one low-friction CTA at the
   end. One idea per beat; add an on-screen-text line per beat (in the channel language). Apply the
   style profile for tone, rhythm, vocabulary, and don'ts. Stay within the word budget — cut filler.

6. **Self-check against the failure modes.** Run the draft past `short-video-craft.md` §5: no slow open,
   hook and payoff aligned, within the word budget, re-hooks present, exactly one CTA at the end.

7. **Finalize: save + report.** Follow `reference/content-conventions.md` — write
   `content/<today>/<channel>-<slug>/script.md` in the channel language and update the folder's
   `meta.json` (add the script artifact, format, target length). Report the hook and the approximate
   length, and offer to move on to `storyboard`.

## Notes

- The hook is the whole ballgame on this format — if you're unsure, draft 2–3 and show them so the user
  picks, rather than committing to a weak one.
- Overwriting is the most common mistake. A 30s script is ~80 words, not a paragraph — if the draft
  runs long, cut until every word earns its place before showing it.
- Write in the channel's language natively; don't write in English and translate — idiom and rhythm
  don't survive translation, and hooks especially depend on them.
- Keep the script and its visuals separate: don't specify shots or camera here. `storyboard` maps beats
  to visuals and hands off to the generation skills.
