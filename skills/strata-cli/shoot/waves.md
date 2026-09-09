# Generate in waves, and animate every still

How to spend generation time in parallel rather than in sequence, and why no photograph stays a photograph.

Part of the asset-generation reference — the index, and every other part, is in [assets.md](../assets.md).

## Generate in WAVES, not in sequence — parallel is the default

Every generation is 10 s (image, TTS) to 3–9 min (video). Doing them one after another is
the single biggest time sink in a multi-asset job: a 10-scene piece is **~10 minutes in
waves and ~60 serially**. *Measured:* seven video generations submitted at once all
completed, five at once, four at once, five renders at once — the API takes concurrent
submits without throttling. The only real constraint is dependency.

**The waves:**

1. **Wave 1 — everything with no inputs:** every character sheet, product sheet, location
   plate, every TTS line, the music. All at once, in the background.
2. **Wave 2 — every clip whose inputs now exist:** all the scenes of a storyboard,
   together — each cites the sheets and its TTS from wave 1.
3. **Wave 3 — only what depends on wave 2:** a continuation chained off a last frame, a
   companion clip covering a scene that came back short, a matte of a delivered clip.

**While a wave runs, do the work that needs no output:** author the VASCO scene and its
layout, the end card, the lower-thirds, the captions plan, the `bible.md`; `preview --grid`
the layout. The clips land into a scene that is already built — never sit and wait.

**Two rules that keep parallel safe:**
- **Unique filenames for parallel renders.** *Measured:* five renders of files all named
  `scene.json` into one library failed with error 3000 (upload filename collision); unique
  names (`scene_a.json`, `scene_b.json`, or the versioned `promo_v3.json`) fixed every one.
  3000 is a generic exporter code with four known causes — [traps.md](../traps.md#error-3000-is-a-generic-exporter-code).
- **Verify each result before it feeds the next wave** — a wave-2 clip is checked against
  its storyboard row and sheet ([production-bible.md](../make/production-bible.md)) before a wave-3
  continuation is built on it; a bad input propagates.

Report per wave, not per asset: *"wave 1: 6 assets launched"*, then a table of what landed.

## Every image becomes a video — no still photos
**Any image used as a visual in the scene becomes a clip before it goes in.** Backgrounds,
hero shots, scenery, products, people — all of them. Do not ask first and do not leave the
still in: a static photo in a motion-design piece reads as a slideshow.

**The rule is "no stills" — it does not prescribe HOW.** Pick the mode per asset:

| | use it when |
|---|---|
| `generate video "<motion>" --first-frame <url>` | the **composition** is the point — an approved hero shot, a frame that must match the layout. Frame 0 *is* that image |
| `generate video "<the shot>" --ref-image <url>` | the **subject** is the point and framing is free — a character across several shots, a product, a world. Composes new angles instead of pushing into one still; **this is what makes a series of clips of the same person look right**. Costs one shot of budget (≤4 per 12 s) |
| plain text-to-video | no image exists yet and nothing downstream needs that exact still — don't manufacture a PNG just to animate it |
| `generate fastvideo <image>` | **only when fast mode was explicitly asked for.** A different, older endpoint — not a quality tier of `generate video`, whose own `--fast` flag is an unrelated thing |

⚠ Never both: `--first-frame` and `--ref-*` are mutually exclusive.

**The only exceptions are a genuine icon / logo / flat UI graphic**, where motion would look
wrong, **a keyed cut-out prop or device** that gets its life at the layer level (*Drawn or
generated?*, above) — **and a PERSONALIZED media slot**, which must stay an `image` layer because the API
substitutes a still per viewer: give it layer-level motion (Ken-Burns on an anchored, `fit:"fill"`
layer). For a fixed image, Ken-Burns is the fallback only when image-to-video is unavailable.

⚠️ **Check every `src` before compiling.** A real failure seen in the wild: `sky.mp4` was
generated and then the scene still pointed at `sky.png`, so the background sat frozen. If a
video exists for an asset, the scene must reference the video.

---
