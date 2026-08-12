# Workflows

A **workflow** is a named, repeatable pipeline that chains the generation skills together with mostly-decided parameters — so a multi-step job you do often becomes one instruction.

Example: instead of "generate a product image, then animate it at 9:16 with Kling for 8s, then log the cost" every time, you save that once as `product-to-ad` and later just say *"run the product-to-ad workflow on this photo."*

## This folder is empty on purpose

You don't hand-write these. The **`workflow-spotter`** skill watches for sequences you repeat and offers to save them here for you. As your habits emerge, this folder fills up. You can also ask directly: *"save what we just did as a workflow."*

## How a workflow behaves when it runs

A workflow **composes** the existing skills — it never reimplements generation logic:
- It still price-checks across fal + OpenRouter per `../reference/model-catalog.md`.
- It still saves outputs and appends to the ledger per `../reference/output-conventions.md`.
- Fixed params (model, format) are baked in; per-run inputs (the source image, the subject) are asked each time.

## Template (what `workflow-spotter` writes)

```markdown
---
name: <workflow-name>
description: One line — what this pipeline does and when to run it.
---

# <Workflow Name>

**Chains:** image-generation → image-to-video   (which skills, in order)

## Fixed parameters
- Model: Kling (image-to-video)
- Format: 9:16 vertical
- Platform: whichever is cheaper at run time (still compare)

## Asked each run
- Source subject / reference image
- Duration (default 8s)

## Steps
1. <step, referencing the skill that does it>
2. ...
3. Finalize per reference/output-conventions.md (save + ledger).

## Cost note
Rough expected total per run, and what drives it — still confirm the live number before generating.
```
