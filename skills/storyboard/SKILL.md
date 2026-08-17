---
name: storyboard
description: Use this skill when the user wants to turn a finished script into a shot-by-shot plan and start generating the visuals — breaking the scenario into timed beats and deciding, per beat, whether to reuse a reference asset or generate something new. Triggers on requests like "storyboard this script", "plan the shots for this video", "turn my script into visuals", or "let's generate the clips for this". It maps each beat to the right existing generation skill (image-generation / image-to-video / video-generation) and hands off — it never generates or price-checks itself. Do NOT use it to write the script (that's script-writing).
---

# Storyboard (script → shots → generation handoff)

Breaks a finished script into timed visual **beats**, decides per beat whether to reuse one of the
channel's reference assets or generate something new, writes a good visual prompt for each, and hands
each beat to the existing generation skill that fits. It **plans and delegates** — it never calls a
provider MCP or touches pricing itself, so all the cross-platform price-check and cost-ledger
discipline is inherited unchanged from the generation skills.

**Prerequisite:** the generation handoff needs the generation skills' prerequisite — at least one of
fal.ai's or OpenRouter's MCP server connected (see the top-level README). Planning the storyboard
needs nothing; only the generation step does.

**Shared references:** resolve the channel via `reference/channel-registry.md`; match the shot rhythm to
`reference/short-video-craft.md`; pull the channel's visual style cues from its `style-profile.md`;
save the storyboard and link to media per `reference/content-conventions.md`. Generation itself follows
`reference/model-catalog.md` + `reference/output-conventions.md` **inside the skills you hand off to** —
don't re-implement it here.

## Workflow

1. **Resolve the channel.** Get the channel id from `reference/channel-registry.md` for its language,
   default format (9:16), and its `style-profile.md` **visual style cues** (color, framing,
   on-screen-text style). Any on-screen text in the storyboard is written in the channel language.

2. **Read the script.** Load `script.md` from the idea's `content/` folder. Segment it into beats
   aligned to the script's beats — hook, each middle beat, payoff, CTA. Aim for the pacing in
   `short-video-craft.md` §3: the visual changes at least once per beat.

3. **Inventory the channel's reference assets.** Look in `channels/<id>/references/` for reusable
   images/clips (recurring host/avatar, brand background, logo). These are the user's own **reference
   assets** (distinct from the `reference/` docs). Consistency across a channel comes from reusing these.

4. **Decide the handoff per beat.** For each beat, pick one:
   - **Reuse a reference asset** → hand the asset + a motion description to **image-to-video** (the
     brand/character-consistency path that skill is built for).
   - **Generate a new still, then animate** → hand a per-beat prompt (optionally conditioned on a
     channel reference asset for consistency) to **image-generation**, then feed the result to
     **image-to-video**.
   - **Text-to-motion directly** → hand a per-beat prompt to **video-generation** (only when there's no
     source still and no consistency need — it's the most expensive path, so prefer the others).
   Write each visual prompt in the channel's visual style; keep the subject consistent across beats.

5. **Write the storyboard.** Save `content/<today>/<channel>-<slug>/storyboard.md` per
   `reference/content-conventions.md` — one row per beat with time, script beat, visual, chosen handoff,
   the reference asset or prompt, and the target `outputs/.../` folder. Update the folder's `meta.json`
   (add the storyboard artifact).

6. **Confirm, then hand off beat by beat.** Video especially costs real money — before generating,
   show the plan and the rough number of clips/images so the user can confirm scope. Then invoke the
   chosen generation skill for each beat. Those skills do the live fal-vs-OpenRouter price check,
   confirm cost, save into `outputs/<date>/<slug>/`, and append to `generations.jsonl` — let them; don't
   duplicate any of it here. Pass the storyboard path along so each generated `outputs/.../meta.json`
   records `source_storyboard` (per `content-conventions.md` §4).

7. **Report.** Once beats are generated, list the `outputs/` folder(s), note the total actual cost the
   generation skills reported, and record them in the idea's `meta.json` `generated_into`.

## Notes

- Consistency is the main job. Reuse the channel's reference assets and keep the subject/style steady
  across beats — a video where the host's face changes every shot reads as broken.
- Prefer the cheaper paths: reuse-and-animate or still-then-animate over text-to-motion. Flag the cost
  difference when a beat really needs direct text-to-video.
- Don't reimplement pricing, saving, or the ledger — that lives in the generation skills and
  `output-conventions.md`. Your outputs are the plan and the right handoff, nothing more.
- If a repeated research→script→storyboard→generate sequence emerges per channel, `workflow-spotter`
  can offer to save it as a one-instruction workflow — these skills are the pieces it would chain.
