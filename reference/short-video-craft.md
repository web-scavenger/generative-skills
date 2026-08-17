# Short-Video Craft (shared reference)

Used by: `script-writing`, `storyboard` skills.

This is the **encoded craft of short-form explainer video** — the part the user doesn't have expertise
in yet, captured once so every script inherits it. `script-writing` reads it to structure a scenario;
`storyboard` reads it to keep the visual rhythm matched to the retention curve. It is
**language-agnostic** — the patterns hold for any channel; only the words change per the channel
language (see `channel-registry.md`).

This encodes *structure and craft*, not voice. Voice/tone lives in the per-channel
`style-profile.md`. When the two seem to conflict, style profile wins on wording, this file wins on
structure.

---

## 1. The retention curve (what actually keeps people watching)

TikTok/Reels/Shorts are retention machines. Watch-time and re-watches decide reach, so structure the
script around attention, not around the information:

- **0–2s — the hook.** The single most important moment. If the first ~2 seconds don't create a reason
  to stay, nothing after matters. Open on the most surprising/tense/useful point, not on a greeting or
  a channel intro. Never "Hi guys, today we're going to talk about…".
- **2–5s — the setup / stakes.** Confirm the promise the hook made and raise the stakes ("…and it's
  costing you money every day"). This is where a weak video loses people who the hook caught.
- **middle — delivery in beats.** Deliver the payoff in short, ordered beats, each one small enough to
  land in a single breath. Every beat should either advance the point or re-hook (see below). Cut
  filler ruthlessly — dead words are dead views.
- **re-hooks — every ~5–8s.** Attention decays; renew it. A re-hook is a mini open loop: "but here's
  the part nobody tells you…", a visual change, a question, a number. Place one whenever a beat ends.
- **payoff — deliver the promise.** The hook wrote a check; cash it clearly. Don't bury the answer or
  save it so long that people leave first.
- **CTA — one, at the end, low-friction.** See §4.

**Rule of thumb:** if you can't say what the hook promises and where that promise is paid off, the
script isn't done.

---

## 2. Hook taxonomy (pick one, make it specific)

A hook is a promise plus a reason to believe staying will pay off. Generic hooks fail; specific ones
work. Patterns that reliably work for explainers:

- **Curiosity gap** — name a surprising outcome but withhold the mechanism. "This one setting doubled
  my battery life." Open the loop; the video closes it.
- **Contrarian / myth-bust** — challenge a common belief. "Everything you've heard about X is wrong."
  Only if you can actually back it up in the payoff.
- **Stakes / cost** — make the cost of not knowing concrete. "You're overpaying for X and don't know it."
- **Pattern interrupt** — an unexpected image, claim, or number in the first frame that doesn't fit
  the scroll. Works visually as much as verbally (storyboard reads this).
- **Direct value / listicle** — "3 ways to X" — weakest as a pure open but reliable; strengthen it by
  front-loading the best item, not saving it for last.
- **Question the viewer answers in their head** — "Ever wonder why X?" — engages, but pair it with
  specificity or it reads as filler.

Write 3–5 candidate hooks, then pick the most specific one. Vague is the enemy — "X is interesting" is
not a hook; "X is why your Y keeps breaking" is.

---

## 3. Pacing (beats, cuts, words)

- **Beat length** — one idea per beat, ~1–2 short sentences, roughly 2–4 seconds spoken. If a beat
  needs a comma-heavy sentence, split it.
- **Cut cadence** — the visual should change at least every beat (a cut, a new element, a text pop).
  Static shots kill retention on this format; the storyboard should give each beat its own visual.
- **Words per second** — short-form narration runs ~2.5–3 words/second. Use this to sanity-check length:
  a 30s script is ~75–90 words, not a paragraph. Overwritten scripts are the most common mistake — cut
  until every word earns its place.
- **On-screen text** — reinforces the spoken line (especially the hook and the payoff); it's read even
  with sound off. Keep it to a few words per beat.

---

## 4. CTA (call to action)

- **Exactly one**, at the end, after the payoff has landed — never before.
- **Low-friction and specific.** "Follow for part 2" (if there's a real part 2), "Comment X if you want
  the detail", "Save this for later" beat a generic "like and subscribe".
- **Optional re-loop.** The strongest ending sometimes loops back to the hook so the video re-plays
  seamlessly (boosts watch-time). Consider it when the topic allows.

---

## 5. Common failure modes (check the draft against these)

- Slow open (greeting/intro before the hook) → cut it, start on the hook.
- Hook promises something the payoff never delivers → align them or change the hook.
- Too long / too many words → cut to the words-per-second budget for the target length.
- One flat middle with no re-hooks → insert an open loop every ~5–8s.
- Two or more CTAs, or a CTA before the payoff → keep one, move it to the end.
- Vague hook → make it concrete and specific to a stake the viewer feels.
