---
name: workflow-spotter
description: Use this skill when the user has repeated a similar multi-step media-generation sequence (e.g. generate an image then animate it, or the same model+format combo used several times), OR explicitly asks to save, automate, templatize, or "make a workflow/skill out of" something they've been doing. It recognizes a recurring pattern and proposes turning it into a reusable workflow doc or a new skill — the user does NOT need to know how to author workflows themselves. Do NOT use it to generate media; it only proposes and scaffolds automation.
---

# Workflow Spotter

Turns things you keep doing by hand into things you can invoke by name. You don't write the workflow — this skill notices the pattern and drafts it for you. It's the on-ramp to the (currently empty) `workflows/` layer.

The user is new to workflows, so this skill is deliberately proactive: it looks for repetition and *offers*, rather than waiting to be asked.

## What counts as a pattern worth proposing

Watch for these signals, from the current conversation and from `generations.jsonl` (paths in this skill are relative to the repo root, which is your working directory):

- **Repeated sequences** — the same chain done ≥2–3 times: e.g. "generate a product image → animate it with Kling → 9:16." A chain that spans two skills is the strongest signal.
- **Repeated parameters** — the same model + resolution + aspect ratio + platform reused across separate requests, especially with a consistent style/prompt scaffold.
- **A stated recurring job** — the user describes an ongoing task ("every week I make three TikTok clips from product shots"), even if you've only seen it once.
- **An explicit ask** — "can we save this," "make this repeatable," "I do this every time." Always act on these.

One-off requests are not patterns. Don't propose automation for something done once with no signal it'll recur.

## Workflow vs. new skill — which to propose

- **Workflow** (a doc in `workflows/`) — a *fixed pipeline* of existing skills with mostly-decided parameters. "Product photo → image-to-video at 9:16, Kling, 8s → log it." Best default for the patterns above. Cheap to create, easy to edit.
- **New skill** — a genuinely *new capability or decision-making* that the three generation skills don't cover (a new provider, a new media type, a distinct judgment the user keeps making). Higher bar; only propose when a workflow can't capture it because real branching/judgment is involved.

When unsure, propose a workflow — it's lighter and can graduate into a skill later.

## How to propose (don't just build it silently)

1. **Name the pattern back to the user** concretely, with the evidence:
   > "You've animated a generated product image at 9:16 with Kling three times this week. Want me to save that as a `product-to-ad` workflow so next time it's one step?"
2. **Get a yes**, and confirm the parts that should be fixed vs. asked-each-time (e.g. model fixed to Kling, but duration asked every run).
3. **Scaffold it** using the template in `workflows/README.md`:
   - Create `workflows/<name>.md` with the steps, fixed params, per-run inputs, and which skills it chains.
   - Reference the generation skills by name rather than re-describing their logic — a workflow *composes* skills, it doesn't reimplement them.
   - Keep the same discipline the skills have: still price-check per `reference/model-catalog.md`, still finalize per `reference/output-conventions.md`.
4. **Show the user the drafted file** and tell them how to run it next time ("just say: run the product-to-ad workflow").

## Notes

- A workflow is only worth it if it removes real repeated effort. If codifying it would save one question, say so and skip it — don't manufacture ceremony.
- Workflows are living docs: if the user tweaks a saved workflow's params two runs in a row, offer to update the file so it matches how they actually use it.
- This skill never spends money on its own. It proposes and writes docs; the generation skills do the actual work when the workflow runs.
