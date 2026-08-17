# Channels

Per-channel **config**, one folder per channel. A "channel" is a first-class concept for the
front-end skills — the user runs more than one (e.g. an English and a Ukrainian channel) in parallel,
each with its own language and voice. The canonical list of channels lives in
`../reference/channel-registry.md`; this folder holds each channel's learned config and assets.

```
channels/<id>/
    style-profile.md   the channel's learned voice — written by the style-learning skill, read by
                       script-writing and storyboard. Tracked in git. Starts as a stub.
    examples/          your source material (past scripts, transcripts) that style-learning learns
                       from. Git-ignored.
    references/        your reusable image/video REFERENCE ASSETS for this channel (recurring host,
                       brand background, logo). Git-ignored. The storyboard skill reuses these for
                       cross-video consistency.
```

**Naming note:** `../reference/` (singular) is shared *docs*. `channels/<id>/references/` (plural) is
your own media *assets*. They are different things — the skills always say "reference docs" vs
"reference assets."

## Adding a channel

1. Add a row to `../reference/channel-registry.md` (unique id, language, style-profile path).
2. Create `channels/<id>/` with a stub `style-profile.md` and empty `examples/` + `references/`.
3. Run the `style-learning` skill with a few examples to fill in the profile.

Seeded channels: `en` and `uk`. Rename their ids in the registry if you have real channel names —
just keep the folder name matching the registry id.
