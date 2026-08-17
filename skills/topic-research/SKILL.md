---
name: topic-research
description: Use this skill when the user wants to research a topic for a short explainer video before writing a script — gathering angles, surprising facts, and candidate hooks for a specific channel. Triggers on requests like "research X for the UK channel", "find me angles on X for a TikTok", "what's interesting about X", or "look into X so we can make a video". It delegates to a research skill (the light `research` skill by default, escalating to `deep-research` only for high-stakes facts) and distills the result into a short-video brief. Do NOT use it to write the actual script — that's the script-writing skill.
---

# Topic Research (topic → short-video brief)

Turns a topic into a **brief** aimed at a short explainer video: the angles worth taking, the
surprising facts that make good hooks, candidate hooks in the channel's language, and the sources
behind them. It does not do the searching itself — it hands that to a research skill and distills the
report down to what a 20–40s video actually needs.

**Keep it cheap.** A 30-second video needs a handful of surprising facts and a plain-terms grasp of
the topic — not an exhaustive report. So the **default is the light `research` skill**, and you only
escalate to the expensive `deep-research` skill when the topic's facts are genuinely high-stakes
(health, legal, finance, safety, or contested/contradicted claims where being wrong on camera matters).
When in doubt, stay light; you can always deepen a specific fact later.

**Prerequisite:** none for research (the research skills use web tools, not the generation MCP
servers). You do need to know which channel this is for — the brief's hooks are written in that
channel's language.

**Shared references:** resolve the channel via `reference/channel-registry.md`; save the brief per
`reference/content-conventions.md` (the `brief.md` format and folder layout). Hook craft to aim the
distill at lives in `reference/short-video-craft.md`.

## Workflow

1. **Resolve the channel.** Get the channel id (ask if unclear — don't guess), look it up in
   `reference/channel-registry.md` for its **language**, and read its style profile so the candidate
   hooks match the channel's voice. Every audience-facing line in the brief is written in this language.

2. **Frame the research question.** Restate the topic as a specific question or two a short video could
   answer — narrow beats broad. If the user's topic is vague ("coffee"), sharpen it first ("why coffee
   makes you crash in the afternoon") so `deep-research` returns usable material, not an encyclopedia.

3. **Delegate to a research skill — light by default.** Invoke the `research` skill with the framed
   question and ask for a scoped answer: what's the basic mechanism, and 3–5 surprising, sourced facts —
   enough for a short video, not a survey. **Only escalate to `deep-research`** when the topic is
   high-stakes (health, legal, finance, safety, or contested claims) — in that case say so and note the
   added cost. Either way you get back a **prose, cited report** — plan to read and distill prose, not to
   parse structured fields. If neither skill is available, fall back to a couple of direct web searches.

4. **Distill into a short-video brief.** From the report, extract: a short **Details** paragraph (2–4
   sentences in plain terms on how the topic actually works — enough to aim the script and pick an angle
   with confidence), **angles** (why anyone cares), **surprising facts** (hook fuel — the
   counterintuitive, high-stakes, or oddly-specific bits), **3–5 candidate hooks** written in the channel
   language using the patterns in `reference/short-video-craft.md` (§2), and the **sources** each
   surprising fact came from. Drop everything that wouldn't earn a place in a 30-second video.

5. **Finalize: save + report.** Follow `reference/content-conventions.md` — write
   `content/<today>/<channel>-<slug>/brief.md` in the channel language and create/update the folder's
   `meta.json` (channel, language, topic). Report the local path, the strongest angle, and how many
   candidate hooks you drafted, then offer to move on to `script-writing`.

## Notes

- Keep the brief short — it's ammunition for a script, not a report. If it's longer than a screen, it's
  over-distilled from the wrong direction; cut to the surprising facts and hooks.
- The default is the light `research` skill; reach for `deep-research` only when being wrong on camera
  is costly (health, legal, finance, safety, contested claims). If neither is available, say so and offer
  a lighter pass with direct web search rather than fabricating facts — a brief is only as good as its
  sources.
- The **Details** section is the one part that isn't a hook — keep it plain and short (2–4 sentences).
  Its job is to orient whoever writes the script so they pick an angle on purpose, not to teach the topic.
- Don't write the script here, even if it's tempting. Hand the brief to `script-writing`, which applies
  the retention structure and the channel's full voice.
- Candidate hooks are drafts to choose from later, so make them genuinely different (a curiosity-gap
  one, a stakes one, a contrarian one), not five rewordings of the same line.
