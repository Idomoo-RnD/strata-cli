# Asset generation — image, video, narration, music

The CLI generates media via the Idomoo AI API (Lucas). Needs auth (`strata auth login`).
Each command saves the file locally (default `./strata_assets/`, or `-o <file>` /
`--out-dir <dir>`) and prints the local path **and** a hosted URL; add `--json` for
machine-readable output. Image/video are **async** (polled to completion); narration is sync.

**The chain:** image → `generate video --first-frame` (or `--ref-image`) → narration + music for
the audio bed → point the scene's `src`/`audio` at the saved files.

**Local files vs URLs.** `generate image --reference` and `generate fastvideo` accept either
a hosted URL or a **local file path** (the CLI base64-encodes it into a data-URI; no upload
step). **`generate video` is the exception: its `--first-frame` / `--last-frame` / `--ref-*`
inputs are URLs only** — every `generate` command prints one, so chain off that; a genuinely
local input needs `strata upload` first (the CLI says so rather than publishing it silently).

---

## Contents

- [Source resolution vs canvas — read before choosing the comp size (MEASURED)](#source-resolution-vs-canvas--read-before-choosing-the-comp-size-measured)
- [`strata generate image "<prompt>" [flags]`](#strata-generate-image-prompt-flags)
  - [Reference images — art style, characters, composition (the important part)](#reference-images--art-style-characters-composition-the-important-part)
- [`strata generate video "<prompt>" [flags]`](#strata-generate-video-prompt-flags)
- [`strata generate fastvideo <image> [flags]`  — the OLD image-to-video path](#strata-generate-fastvideo-image-flags---the-old-image-to-video-path)
- [`strata generate narration "<text>" --voice <voice_id>`](#strata-generate-narration-text---voice-voice_id)
- [`strata generate music "<prompt>" [--duration <sec>]`](#strata-generate-music-prompt---duration-sec)
- [`strata upload <file>` — ONLY for generation inputs with no URL](#strata-upload-file--only-for-generation-inputs-with-no-url)
- [Generate in WAVES, not in sequence — parallel is the default](#generate-in-waves-not-in-sequence--parallel-is-the-default)
- [Every image becomes a video — no still photos](#every-image-becomes-a-video--no-still-photos)
- [Alpha overlays — `.jet`, and how to get anything into it](#alpha-overlays--jet-and-how-to-get-anything-into-it)
- [Command reference — every generation and media command, with the rules](#command-reference--every-generation-and-media-command-with-the-rules)

## Source resolution vs canvas — read before choosing the comp size (MEASURED)

| asset | native size |
|---|---|
| `generate image` | **1376×768** (16:9; other aspects the same area) |
| `generate video` / `fastvideo` | **1280×720** — the CLI clamps to 720p |
| `generate avatar` | 1280×720 @ 25 fps |

**Scene fps follows the footage:** `generate video` clips arrive at 24 fps, avatars at 25 —
set the scene's `fps` to the rate of the clips it carries (one rate per piece; a `.jet`, an
envelope and a `strata beats --fps` must use the same), and when both kinds appear pick the
one with the speaking clip, since lip-sync tolerates no resample.

The default comp is 1920×1080, so a generated plate used **full-bleed** is already upscaled
**1.4–1.5×** before the mandatory push-in adds more. There is no upscaler in the CLI.
Author around it:

- **Full-bleed generated footage → author the comp at 1280×720** (or 720×1280 / 1080×1080
  for 1:1, where a 1080 square from a 1376×768 source is also ~1:1). The cloud render is
  the deliverable size; design at the footage's size and let the platform scale once.
- **In a 1920 comp, keep generated media in a framed slot ≤ its native size** — the split,
  card and mosaic layouts in layouts.md already do this, and it is why they look sharper
  than a full-bleed plate with a caption.
- **Never stack a push-in on an already-upscaled full-bleed plate**: at 1.5× plus a 10 %
  Ken-Burns the frame ends at 1.65×, and softness reads as "AI footage" faster than anything.
- Match `--aspect` / `--ratio` to the **slot**, not the comp — a 9:16 slot in a 16:9 frame
  wants a 9:16 asset, so none of the resolution is cropped away.

## `strata generate image "<prompt>" [flags]`
A still PNG (async, ~10–20s).

| flag | meaning |
|---|---|
| `--aspect` | `16:9 4:3 3:4 1:1 9:16 21:9` (default `1:1`). Match the comp (e.g. `9:16` for vertical). |
| `--colors "#hex,#hex"` | brand palette to bias the result |
| `--reference <img\|url>` | **reference image(s)** — repeatable (and comma-splittable); each a local file or URL |
| `-o <file>` / `--out-dir <dir>` | output path / folder (default `./strata_assets/`) |

### Reference images — art style, characters, composition (the important part)
Reference images steer the result toward a **look**, a **character**, a **logo**, or a
**composition**. Use them whenever:
- the user gives an image — a brand character, mascot, logo, product photo, style frame, or a
  previous generation — and wants that look/subject, **or**
- you need a **recurring character or consistent art style across shots**. Generate every shot
  from the **same reference(s)** so they stay on-model — pure text prompts drift shot to shot.

**Drive them by INDEX in the PROMPT.** References are numbered by order: the 1st `--reference`
is **image 0**, the 2nd **image 1**, etc. Cite the index in the prompt text:
- *"using **image 0**'s art style, draw a dog"* — style transfer onto a new subject.
- *"put the character from **image 1** into **image 0**'s scene"* — combine across references.

References are passed in the **`images` array** of `generate-image` and cited **by index in
the prompt**. (The API's separate `reference_image` param is *not* used — it routes to a
different, sometimes-unpaid model.)

**Verified behaviour (tested live):**
- **Same person / character** — *"the same person as image 0, at a cafe with a laptop"*
  preserves her exact face, curly red hair and freckles in a brand-new pose and scene. Pass
  the same reference into **every** image to keep a person, mascot, product or brand character
  on-model across shots.
- **Same art style** — *"in image 0's art style, draw a car"* transfers the reference's
  palette and linework to a different subject (a pastel thick-outline house → a matching car).
- To draw a **new** subject rather than reproduce the reference's subject, say "image 0's
  **style/look**", not just "image 0".
- **Multiple references compose** — pass several and combine them: *"the character from image 0
  in image 1's art style"*, *"image 0 and image 1 standing together"*.
- Index maps to the `--reference` **order**: 1st `--reference` = image 0, 2nd = image 1.
- Local **PNG/JPG/WebP** (auto-base64) and hosted URLs both work.

Examples:
```bash
strata generate image "using image 0's art style, draw a dog" --reference ./mascot.png
strata generate image "image 1's character standing in image 0's scene" --reference ./bg.jpg --reference ./hero.png
strata generate image "hero banner, brand palette" --aspect 16:9 --colors "#2563eb,#16a34a"
```

---

## `strata generate video "<prompt>" [flags]`
An MP4 clip (async, **3–9 min**). **One command, five modes**, chosen by which inputs you
give it: prompt only = text-to-video · `--first-frame` = image-to-video · `+ --last-frame` =
keyframe interpolation · `--ref-*` = reference-driven · a source clip in `--ref-video` =
editing/extension.

🚫 **`--first-frame`/`--last-frame` and `--ref-image`/`--ref-video`/`--ref-audio` are MUTUALLY
EXCLUSIVE** — the CLI rejects the combination before spending anything.

| flag | meaning |
|---|---|
| `--first-frame <url>` `--last-frame <url>` | exact opening / closing frame |
| `--ref-image <url>` (repeatable) | character, product, world or style to keep consistent |
| `--ref-video <url>` | copy a camera plan or cut rhythm (see `strata sketch`) |
| `--ref-audio <url>` | a voice / audio bed to sync to |
| `--duration <4..15>` | clip length (default 5); longer needs chaining |
| `--ratio <16:9\|9:16\|1:1\|4:3\|3:4\|21:9\|adaptive>` | output aspect |
| `--seed <N>` | reproducibility. (`--camera-fixed` is rejected by the model on every task type — lock the camera in the prompt: [video-generation.md](video-generation.md#locking-the-camera--in-the-prompt-not-the-flag)) |
| `--audio` | native synced audio (real AAC 44.1 kHz stereo) |
| `--last-frame-out <file>` | save the last frame for chaining — its URL expires in 24 h |
| `--realistic-human` | required for real faces, logos and real products; **applied automatically** on a content rejection |
| `--fast` · `--model <id>` | the fast model (quicker, drops shots) · an explicit model |
| `--resolution` | **clamped to 720p** — 720p is always the max |

Reference the result as a `video` layer. If the comp outlives the clip, `playback_mode: "hold"` freezes the last frame — do **not** `loop` it to fill time (the restart reads as a glitch, format.md).

```bash
strata generate image "hero shot" -o hero.png          # prints url:
strata generate video "slow cinematic push-in, dust in the light" \
  --first-frame <that url> --duration 8 --audio -o hero.mp4
```

**Prompting is the whole game here** — a one-line prompt wastes a 3–9 minute render. The
shot-list structure, the identity lock, the `.jet` no-cuts rule, keyframes and chaining are in
[video-generation.md](video-generation.md); references, dialogue, animatics and editing in
[video-generation-advanced.md](video-generation-advanced.md).

## `strata generate fastvideo <image> [flags]`  — the OLD image-to-video path
Quick and cheap (~1–3 min), and **only for when fast mode is explicitly asked for**. It is a
different, older endpoint — **not** a quality tier of `generate video`, whose own `--fast`
flag selects a faster model within the new API and is an unrelated thing.

Takes a hosted URL **or a local file path** (auto-base64), plus `--prompt "<motion>"`,
`--duration`, `--ratio`. That is the whole surface: **an image is required** (no
text-to-video), and there are no keyframes, no references, no `--audio`, no
`--last-frame-out`, no `--realistic-human` and no shot list. None of the prompt craft in
[video-generation.md](video-generation.md) applies — it produces one continuous move, which
does at least make it safe to matte into a `.jet` overlay.

```bash
strata generate fastvideo ./hero.png --prompt "slow cinematic push-in" --duration 5
```

## `strata generate narration "<text>" --voice <voice_id>`
TTS voiceover MP3 (sync). Returns the spoken **duration** in seconds — size the scene around it.
- `strata generate voices [--search <text>]` lists `voice_id  name · gender · accent · use-case`.
- `--voice <voice_id>` (required) · `--normalize <mode>` for text normalization.
**Where the MP3 goes depends on what it is for — and these two are mutually exclusive:**

| The narration is… | Then |
|---|---|
| **Narration / VO over visuals with no one speaking on camera** (motion graphics, b-roll, product shots, kinetic text) | reference the MP3 as an `audio` layer in the scene. This is the normal case |
| **Anyone speaks on camera** — a `{line}` in a text-to-video prompt, a voice fed to `--ref-audio`, an avatar, the user's footage | the **clip's own audio** is the voice. Put the clip in the scene and **do NOT add a TTS of the same line as an audio layer** — it cannot be synced and the voice doubles |

⚠️ **Never lay the source TTS over a clip that was generated from it.** *Measured:* the
spoken lines land **1.3 s, 3.8 s and 6.3 s into their clips** — never at 0 — because the
model places each line against the picture and re-paces the delivery. The MP3 at `t=0` is
seconds early and **no fixed offset fixes it**; you also get the voice twice. Details in
[video-generation-advanced.md](video-generation-advanced.md).

Pair the personalized value with the visual so it lands.

## `strata generate music "<prompt>" [--duration <sec>]`
An instrumental track (default 30s). Reference as an `audio` layer at low `volume` with
`ducking: true` so it sits under narration.

---

## `strata upload <file>` — ONLY for generation inputs with no URL

**The rule, in one line:** **Upload only an asset we created ourselves that has no URL, and use the resulting URL only as an input to `generate` (image/video/avatar) — nothing else.** (`strata captions` uploads for itself when it must; that is the one other consumer, and it is inside the CLI.)

The full policy — the two-condition test, which inputs already have a URL, the temporary-only rule,
when a URL is actually required, why public-and-permanent means temp-only, and the extension-must-match-bytes
check — is in [upload.md](upload.md). Read it before the first `strata upload` of a job.

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
- **Verify each result before it feeds the next wave** — a wave-2 clip is checked against
  its storyboard row and sheet ([production-bible.md](production-bible.md)) before a wave-3
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

**The only exception is a genuine icon / logo / flat UI graphic**, where motion would look
wrong — **and a PERSONALIZED media slot**, which must stay an `image` layer because the API
substitutes a still per viewer: give it layer-level motion (Ken-Burns on an anchored, `fit:"fill"`
layer). For a fixed image, Ken-Burns is the fallback only when image-to-video is unavailable.

⚠️ **Check every `src` before compiling.** A real failure seen in the wild: `sky.mp4` was
generated and then the scene still pointed at `sky.png`, so the background sat frozen. If a
video exists for an asset, the scene must reference the video.

---

## Alpha overlays — `.jet`, and how to get anything into it

Every layer that sits over another layer needs alpha → a `.jet`; a full-frame plate or unoverlapped
footage stays an MP4 (`matte` is the slowest step and `.jet` is lossy). How to get any source into a
`.jet` — the source→method table, same-source occlusion with no alpha at all, `matte` at half width,
the quality setting, keying the VIDEO not a still, and text BEHIND the subject from the SAME clip —
is in [alpha.md](alpha.md).

## Command reference — every generation and media command, with the rules

**Every command's signature, in one line:** `compile` · `validate` · **`versions <scene>`** / **`revert <scene> --to N`** (automatic per-scene history — [format.md](format.md)) · **`preview <scene> [--at <sec>] [--grid] [--comp <name>]`** (free local layout wireframe — no cloud) · **`studio [scene.json] [--port 4321] [--no-open]`** (local browser layout designer → writes `*.guide.json`; only with the user's go-ahead — it waits for them to press Save) · **`jet <frames|video> [--key R,G,B] [--method chroma] [--fps N]`** (alpha-video overlay — .jet, not .mp4) · **`matte <video> [--width N]`** (remove the background from footage **of a PERSON or a character** → .jet — it is a human matting model, so a product/object/logo clip fails with `no subject found`; key those instead) · **`track <video> [--comp WxH | --point x,y]`** (track a surface → corner_pin, or an element → position keyframes) · **`captions <file|url> [-o cues.json] [--srt f]`** (speech-to-text WITH TIMINGS from audio **or video** — caption bars, VO timing, verifying a generated clip said its line; ⚠ segment-level, not per-word) · **`glyphs <font.ttf> "<text>" | <scene.json>`** (does the font cover the copy — before authoring) · **`beats <audio> [--fps N --bands N]`** (onsets + bpm, and the per-frame envelope — never hand-time to music) · **`upload <file>`** (host a generation input that has no URL — public + permanent) · `inspect <file.idm> [--assets <dir>] [-o doc.json]` (unpack an .idm: every asset + the VASCO doc) · `repack <doc.json> -o out.idm` (re-encode an unpacked .idm — **escape hatch only**, VASCO is baked output so text/asset swaps yes, motion no) · `sketch <plan.json>` (blocking animatic → `generate video --ref-video`) · `generate image|video|fastvideo|avatar|narration|music|voices` · `add <block>` · `render --library <id> [--data row.json|rows.json]` (a data object personalizes one render by layer name; an array renders one video per row — the template is uploaded once) · `snapshot --library <id> [--data row.json]` (poster-only, fast QA — proof a personalized frame cheaply) · `library list|create <name>` (**create is get-or-create — ask the user which library before the first render, never pick one**) · `init` · `auth login|status` · `schema` · `update` · `uninstall`. Add `--json` for machine-readable output (errors on stderr; nothing reads a TTY non-interactively). Exit codes: 0 ok · 1 compile/schema · 2 missing file · 3 auth · 4 render timeout.
The CLI creates media via the Idomoo AI API (needs auth; saves to `./strata_assets/`):

| command | makes |
|---|---|
| `strata generate image "<prompt>" [--aspect 9:16] [--colors …] [--reference <img\|url> …]` | a still PNG (async) |
| `strata generate video "<prompt>" [--first-frame <url>] [--last-frame <url>] [--ref-image/--ref-video/--ref-audio <url>] [--duration 5] [--ratio 9:16] [--audio] [--last-frame-out <f>]` | a **video clip** (async, 3–9 min). One command, five modes chosen by the inputs: text-to-video, image-to-video, keyframe interpolation, reference-driven, editing. ⚠ frames and references are **mutually exclusive**. **A clip shorter than its scene is never stretched** — cover the gap with a companion clip of more shots, or extend off the last frame. `--ref-audio` lip-syncs a voice **or** choreographs the cut to a music track. Prompting is a craft — [video-generation.md](video-generation.md) |
| `strata generate fastvideo <image\|url> [--prompt "<motion>"] [--duration 5] [--ratio 9:16]` | the OLD quick image-to-video path. **Only when fast mode is explicitly asked for** — no text-to-video, keyframes, references or audio |
| `strata sketch <plan.json> -o plan.mp4` | a grey-box 3D **animatic** to drive a complex camera plan, fed back as `--ref-video`: [video-generation-advanced.md](video-generation-advanced.md) |
| `strata path <file.svg> -o out.jet [--duration N] [--stroke N] [--color #hex] [--head N]` | a **stroke reveal** (draw-on) as an alpha `.jet` overlay - logo draw-on, map routes, signatures, the line-draw style. Paths draw in SVG document order; `--head` adds a travelling dot. The `.jet` fps MUST match the scene |
| `strata retime <clip> --ramp "0:1.0, 2.0:0.25, 3.2:1.0" [-o out]` | a **keyframed speed ramp** - slow segments are motion-interpolated (no stutter), audio tempo-adjusted through the ramp. For a deliberate impact beat ONLY - never to make a clip fit a scene |
| `strata grade <clip> --match <ref> \| --lut <f.cube> \| --look <name> [-o out]` | **colour-match / grade** - `--match` histogram-matches a companion clip to its reference so the cut reads as one shoot; `--lut` applies the brand's LUT; `--look` restrained named looks |
| `strata chart bars\|donut\|line --box x,y,w,h ... [scene.json]` | **animated chart layers** as scene JSON - bars GROW from data, donuts sweep, lines draw on. Named layers, so `render --data` personalises the value TEXT; heights and sweeps are baked at emit time - per-viewer heights are one scene per row, see [personalization.md](personalization.md) |
| `strata generate avatar <image URL> --audio <url> [--aspect 9:16] [--motion "..."]` | a **talking presenter** from one still + audio (lip-synced). The image must NOT be a flat front-facing headshot — angle it, free the hands, light it: [avatar.md](avatar.md). **Not the default for a presenter.** A presenter, host or testimonial is a **filmed** shot — `generate video --ref-image --ref-audio` (portrait + TTS) — unless the piece is **personalized** (then a stable avatar plate is what Idomoo swaps) or the user wants a fixed plate / a quick cut. Name the route and its trade-off in the storyboard: [avatar.md](avatar.md) |
| `strata generate narration "<text>" --voice <voice_id>` | TTS voiceover MP3 (`generate voices` lists ids) |
| `strata generate music "<prompt>" [--duration 30]` | an instrumental track (**Stable Audio 3**, 44.1kHz stereo **WAV**). Prompt it properly — tags, arc, BPM: [music.md](music.md) |
| `strata upload <file>` | a **public URL** for a local file — **only** when a file has no URL yet and an endpoint accepts nothing else (`generate avatar`, `generate video`'s `--first-frame`/`--ref-*`; `strata captions` does its own). ⚠ TEMP handoff only, and public + permanent: [assets.md](assets.md) |

Chain: **image → animate into video → narration + music**, then point `src`/`audio` at the saved files. `generate image` accepts a **local file or a URL** for `--reference` (auto-encoded, no upload); `generate video` takes **URLs only** for `--first-frame`/`--ref-*`, and every `generate` command prints one. **CRITICAL — upload only an asset we created ourselves that has no URL, and use that URL only as a reference for `generate` (image/video). Nothing else.** Every `generate` command already prints a hosted `url:`, so use that string and never re-upload a generated asset. `strata upload` is only for a **generation INPUT** (something fed to the AI API, which takes URLs) that has **no URL yet** — the user's own photo/footage, or something rendered locally to use as a reference ([upload.md](upload.md)). It is a throwaway handoff, never asset storage. **Scene assets are never uploaded**: `src` values — images, MP4s, **`.jet` overlays**, fonts — stay local paths and are embedded in the `.idm` at encode time; so are deliverables and brand files.

**Two rules that always apply when generating media — details in [alpha.md](alpha.md) and *Every image becomes a video* above:**
- **An overlay's motion must live IN the footage — key the VIDEO, never a still.** The pipeline for any subject that composites over the scene (a plane, a person, a mascot, a product) is: **generate the image → `generate video "<motion>" --first-frame <its url>` → matte/key THAT VIDEO per frame → `.jet`**. The subject then flies/walks/turns inside the clip and the layer itself stays put (box = full frame, no `position` animation). **Do this even when the user never says "key it"** — it is what makes the overlay look filmed instead of pasted. ❌ **The failure to avoid:** matte a *still*, or matte a clip whose subject barely moves, then fake the motion by translating the cut-out across the screen with a couple of `position` keyframes — it reads exactly like a sticker sliding over the picture, because that is what it is. ⚠️ Image-to-video models often **hover the subject instead of moving it**, so after generating, check that it actually travelled (compare the subject's position in the first and last frames, or `strata track --point`); if it barely moved, **re-prompt the clip with the displacement stated explicitly** — never compensate by sliding the layer.
- **EVERY image in the scene gets animated — no still photos.** Any generated or supplied image that appears as a visual (background, hero shot, product, scenery, person) becomes a clip before it goes in the scene. **The rule is "no stills" — it does not prescribe HOW**; pick the mode per asset:
  - **`generate video "<motion>" --first-frame <its url>`** when the **composition is the point** — a laid-out hero shot, an approved frame, anything that must match the sketched layout. Frame 0 *is* that image, so the framing is guaranteed. (Every `generate image` prints the url.)
  - **`generate video "<the shot>" --ref-image <its url>`** when the **subject is the point and framing is free** — a character across several shots, a product, a world. It composes new angles instead of pushing into one still, which is why a series of clips of the same person looks far better this way. Costs one shot of budget (≤4 per 12s) and does **not** lock the opening frame.
  - **Straight text-to-video** when no image exists yet and nothing downstream needs that exact still — do not manufacture a PNG just to animate it.
  - **`generate fastvideo <image>`** ONLY when fast mode was explicitly asked for — it is a different, older endpoint, not a quality tier of `generate video` (which also has its own `--fast` model flag; the two are not the same thing).
  ⚠ A clip destined for a `.jet` overlay must be **ONE continuous shot, no cuts** — [video-generation.md](video-generation.md). **The only exception is a genuine icon/logo/UI element** — small flat graphics that would look wrong moving. Do **not** ask first and do **not** fall back to the still: a static photo in a motion-design piece reads as a slideshow, which is the failure this skill exists to prevent. **And once a video was generated for an asset, the scene must reference the VIDEO, never the leftover `.png`** — check every `src` before compiling. (For a **fixed** image, Ken-Burns on the still is the fallback only when image-to-video is unavailable; for a **personalized** slot it is the standard treatment — see "Images are never still" above.)
- **Reference images — keep the SAME person or the SAME art style across images (verified).** `generate image --reference <img|url>` (repeatable, local file or URL) passes reference images in the `images` array, and **the prompt refers to them by index**: the 1st `--reference` is **image 0**, the 2nd **image 1** (note: `generate video`'s `--ref-image` counts from **[Image 1]** — the two commands index differently). Two proven jobs:
  - **Same character / subject:** *"the same person as image 0, sitting at a cafe with a laptop"* → her exact face, hair and freckles are preserved in a new pose and scene. This is how a recurring person, mascot, product or brand character stays on-model across every shot — pass that reference into **each** image.
  - **Same art style:** *"in image 0's art style, draw a car"* → the reference's palette, linework and look transfer to a new subject.
  Use references **whenever the user gives an image** (a person, a character, a logo, a product, a style frame, a prior render) or needs consistency across shots. Phrasing (style-vs-copy, combining several references) is in *Reference images* above.
