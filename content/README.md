# Content

The **creative artifacts** for each video idea — the paper trail that comes *before* generation. This
is the twin of `outputs/` (which holds generated media): same dated-folder + sidecar style, but for
the text that plans a video rather than the media itself.

```
content/<YYYY-MM-DD>/<channel>-<slug>/
    brief.md        research distilled for a short video   (topic-research)
    script.md       the scenario / script, in the channel language   (script-writing)
    storyboard.md   shot-by-shot plan + generation handoff   (storyboard)
    meta.json       describes the idea: channel, language, topic, which artifacts exist, links to outputs/
```

- Folders are named `<channel>-<slug>` so the channel (and thus language) is visible at a glance.
- The three `.md` files and `meta.json` are **tracked in git** — they're small and they're the record.
- Media generated from a storyboard lands in `outputs/` (git-ignored), linked both ways: `meta.json`
  here lists `generated_into`, and each generated `outputs/.../meta.json` carries a `source_storyboard`
  back-reference.

The full layout, artifact formats, and the language rule live in
`../reference/content-conventions.md`. This folder is empty until you start making videos.
