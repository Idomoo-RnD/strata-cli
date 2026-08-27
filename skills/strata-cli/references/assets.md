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
  - [🔴 CRITICAL — the whole rule in one line](#critical--the-whole-rule-in-one-line)
  - [🛑 The two-condition test](#the-two-condition-test)
  - [Most generation inputs already have a URL](#most-generation-inputs-already-have-a-url)
  - [🛑 TEMPORARY assets only — never persistent ones](#temporary-assets-only--never-persistent-ones)
  - [When a URL is actually required](#when-a-url-is-actually-required)
  - [⚠ Public and permanent — which is why it is for temp use only](#public-and-permanent--which-is-why-it-is-for-temp-use-only)
  - [The extension must match the bytes](#the-extension-must-match-the-bytes)
- [Generate in WAVES, not in sequence — parallel is the default](#generate-in-waves-not-in-sequence--parallel-is-the-default)
- [Every image becomes a video — no still photos](#every-image-becomes-a-video--no-still-photos)
- [Alpha overlays — `.jet`, and how to get anything into it](#alpha-overlays--jet-and-how-to-get-anything-into-it)
  - [Same-source occlusion — text behind a subject with NO alpha at all](#same-source-occlusion--text-behind-a-subject-with-no-alpha-at-all)
  - [Getting to `.jet` — pick the row that matches the source](#getting-to-jet--pick-the-row-that-matches-the-source)
  - [`matte` is slow — halve the width first (MEASURED)](#matte-is-slow--halve-the-width-first-measured)
  - [Quality — `.jet` is lossy; the default is Idomoo's own reference setting](#quality--jet-is-lossy-the-default-is-idomoos-own-reference-setting)
  - [⚠️ Key the VIDEO, not a still — the motion belongs in the clip](#key-the-video-not-a-still--the-motion-belongs-in-the-clip)
  - [Text BEHIND the subject — the SAME clip, used twice](#text-behind-the-subject--the-same-clip-used-twice)

## Source resolution vs canvas — read before choosing the comp size (MEASURED)

| asset | native size |
|---|---|
| `generate image` | **1376×768** (16:9; other aspects the same area) |
| `generate video` / `fastvideo` | **1280×720** — the CLI clamps to 720p |
| `generate avatar` | 1280×720 @ 25 fps |

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

### 🔴 CRITICAL — the whole rule in one line

> **Upload only an asset we created ourselves that has no URL, and use the resulting URL
> only as an input to `generate` (image/video/avatar) — nothing else.** (`strata captions`
> uploads for itself when it must; that is the one other consumer, and it is inside the CLI.)

Both halves are binding:

- **What may be uploaded:** a file **we produced locally** (or one the user supplied) that
  **has no URL**. Never a generated asset — those already have one.
- **What the URL may be used for:** **a generation reference input only.** It goes into
  `--reference`, `first_frame`/`last_frame`, or `reference_images`/`reference_videos`/
  `reference_audio`. It does **not** go into a scene, a `src`, a deliverable, a brand file,
  or anything shown to the user as a link.

If you cannot name the `generate` call the URL is for, do not upload.

### 🛑 The two-condition test

Upload only when **both** are true:

1. the file is a **generation INPUT** — something handed to the AI API, which takes URLs:
   `generate avatar`'s image, and the video API's `first_frame`, `last_frame`,
   `reference_images`, `reference_videos`, `reference_audio`; **and**
2. it **has no URL** already.

If it is not a generation input, it does not need a URL at all. **Scene assets are local
paths** — images, MP4s, **`.jet` overlays**, fonts. The encoder reads their bytes at encode
time and embeds them in the `.idm`, so a `.jet` from `matte`/`jet` is never uploaded; it is
just a `src`.

### Most generation inputs already have a URL

**Everything `strata generate` produces is already hosted and prints its URL.** Read it off
the command's output and pass that string on. Uploading a generated file is always a
mistake: it costs an extra request, and it creates a second, permanent, public copy of
something that was already served.

```
✅ saved C:\…\presenter.png
   url: https://s3.us-east-1.amazonaws.com/assets-temp.idomoo.ai/images/…png   <-- USE THIS
```

| Asset | Already has a URL? | `upload`? |
|---|---|---|
| `generate image` (incl. `--reference` output) | ✅ prints `url:` | **no** |
| `generate video` | ✅ prints `url:` | **no** |
| `generate narration` / `generate music` | ✅ prints `url:` | **no** |
| `generate avatar` | ✅ prints `url:` | **no** |
| A rendered MP4 from `strata render` | ✅ prints `video:` / `poster:` | **no** |
| A file **the user gave us**, used as a reference or a first/last frame | ❌ | **yes** |
| Something **we built locally** and are using as a generation reference — a rendered animatic, an ffmpeg frame grab, a texture drawn by a script | ❌ | **yes** |
| A `.jet`, or any other **scene asset** (`src`) | n/a — embedded in the `.idm` | **never** |

The split is by **command**, not by luck — it is not "upload if the URL was missing":

| Returns a URL | Writes local files ONLY — never a URL |
|---|---|
| `generate image` · `video` · `avatar` · `narration` · `music` | `jet` · `matte` · `preview` · `compile` · `track` |
| `render` (`video:` + `poster:`) | anything from ffmpeg or a throwaway script |

Most of the right-hand column needs **no** URL, because its output is a scene asset that
gets embedded (`jet`, `matte`, `compile`) or is local-only by nature (`preview`, `track`).
They appear here only because *if* one of their outputs is later used as a generation
reference, it will need uploading first.

So the rule is: **capture the `url:` line when an asset is generated.** If a `generate`
command did not print one, something failed — investigate, do not paper over it with an
upload. Reach for `upload` only in the two cases that legitimately have no URL: **a file the
user supplied**, or **something produced by a local-only command** (a `.jet` from `matte`, an
ffmpeg frame grab or cut, a generated texture, a rendered animatic). Upload it once and reuse
that URL.

### 🛑 TEMPORARY assets only — never persistent ones

`upload` is a **transient handoff**: the one job is getting a local file into an API call
that will only accept a URL. It is **not** asset storage, not a CDN, and not where a
project's files live.

**Never upload:**

- **Scene assets — including every `.jet`.** A scene's `src` points at a **local file on
  disk**; the encoder reads the bytes at encode time and embeds them in the `.idm`. A `.jet`
  alpha overlay, an MP4 background, a PNG, a font: all local, all embedded. Uploading them
  and pointing `src` at a URL is wrong and gains nothing.
- **Deliverables** — finished MP4s, posters, anything the user is meant to keep. Those live
  in the project folder (and rendered videos are already hosted by `render`).
- **Brand assets** — a logo, a `.brand/` file, a font. Those belong in the repo.
- **Anything long-lived or referenced later.** Treat every uploaded URL as throwaway: good
  for this API call, not something to build on.

The irony is the point: the store itself is permanent and undeletable (below), so the
discipline has to be yours. **Temporary use, permanent consequence** — upload the minimum,
once, and only to feed a call that demands a URL.

### When a URL is actually required

Some endpoints take **only** a URL and reject base64 data-URIs — `generate avatar`'s image
is the one in this CLI today, and **`generate video`'s media inputs** (`--first-frame`,
`--last-frame`, `--ref-*`) are the other. `generate image --reference` and `generate
fastvideo` do **not** need a URL at all — they accept a local path and encode it themselves,
so never upload for their sake. And never upload for `generate video` either when the input
came from a `generate`/`render` command: that already printed a `url:` — use it.

```bash
strata upload footage_the_client_sent.mp4
# ✅ https://t.idomoo.com/9e289b70-…-7a1ed49e102f.mp4
#    1.30 MB · sniffed as video/mp4 · serving video/mp4
```

### ⚠ Public and permanent — which is why it is for temp use only

The endpoint is unauthenticated, the object is `public-read`, and there is **no expiry and
no delete**. Anything uploaded is world-readable forever — there is no way to take it back.
Every needless upload is a permanent public artefact, so the bar is: *this call will not
work without it.* **Never upload anything private,
personal or client-confidential** — and never a viewer's personalized data. Say so when
offering it; do not upload a user's file without asking.

### The extension must match the bytes

The host sniffs the real type from the file's first 261 bytes and **rejects a request whose
extension disagrees — with a bare `404` and an empty body** (measured: `.png` holding JPEG
bytes → 404; `.mov` holding MP4 bytes → 404; `.jpg` and `.jpeg` are interchangeable).

The CLI handles this: it sniffs the magic number locally and uploads under the type the
bytes actually are, telling you when it does:

```
⚠ named .png but the bytes are jpg — uploaded as .jpg
```

This matters because `generate image` sometimes returns JPEG bytes for a `-o …png` — so a
"png" on disk may not be one. The served `Content-Type` always follows the bytes.

The command also **verifies the URL serves** before handing it back (`serving image/png`),
because the documented failure mode is a successful upload whose URL then 404s.

---

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

An **MP4 has no alpha**, so a clip laid over the scene arrives as an opaque rectangle.
Idomoo's alpha format is **`.jet`** (IDMJET, YUVA420 — alpha is a real plane). Every
overlay clip is a `.jet` used as a `video` layer. **Its fps must match the source clip and
the scene**, or the overlay drifts out of time — the CLI prints the fps it used.

### Same-source occlusion — text behind a subject with NO alpha at all

When the only thing you need is **text passing behind a subject that stays on its own
plate**, you do not need a matte, a key, or a `.jet`. Use the clip **twice**:

```
video  "plate"    src: hero.mp4   box: [0,0,W,H]     <- the clip, untouched
text   "headline"                                    <- sits BETWEEN them
video  "front"    src: hero.mp4   box: [0,0,W,H]     <- the SAME clip, masked to the subject
```

Both video layers are full-frame on the same box, neither gets position animation, and the
front copy carries a **rough geometric mask** in the shape of the subject (a rect plus a
cylinder/ellipse is usually enough).

**Why it is more robust than any key:** outside the mask, both layers are the *identical*
pixels — so a wrong mask edge is invisible. The edge only matters where the text actually
passes behind the subject. That inverts the usual advice: **a generous, sloppy mask beats a
tight one**, and it costs nothing to oversize it.

Use it when the subject is hard to key (frosted glass, warm-on-warm, a gold cap against a
brown seamless) or when `matte` refuses because there is no person in frame. Its one limit:
**the subject can never leave its own plate** — for that you need real alpha, so generate on
a green backdrop instead.

If the subject moves or turns, animate the mask loosely (a few keyframes) or drive it with
`strata track --point`. Precision is wasted here; coverage is not.

### Getting to `.jet` — pick the row that matches the source
| I have | command | notes |
|---|---|---|
| **A PERSON in ordinary footage, no green screen** (on a beach, in an office…) — also works on cartoon/stylized characters | `strata matte clip.mp4 -o subject.jet` | **removes the background automatically** (AI video matting, runs locally on CPU). ⚠ **People only** — see the row below. First run downloads a ~14 MB model into `~/.strata/models/`. |
| **A PRODUCT, object, logo or landscape** | ❌ **not `matte`** — re-generate the clip on a green backdrop and use `--method chroma`, or key a uniform background with `--key`, or use same-source occlusion (below) | `matte` runs Robust Video Matting, trained on **people**. On a product it finds no subject and the CLI now stops with `no subject found`. *Measured coverage:* cartoon character 46.8%, person close-up 23.9%, person in wide shots 8.9%, **perfume bottle 0.00%** |
| **Green/blue screen footage** | `strata jet clip.mp4 --key 0,177,64 --method chroma -o o.jet` | add `--choke 1 --feather 1` to trim a colour fringe |
| **A solid white/black background** | `strata jet clip.mp4 --key 255,255,255 -o o.jet` | `distance` method is the default |
| **A PNG sequence that already has alpha** (AE/Blender/Nuke/Resolve export, or roto) | `strata jet ./frames --fps 24 -o o.jet` | **best quality — no keying at all.** The frames' own alpha is used automatically; only pass `--key` if you actually want a colour keyed |
| **A still image I generated** | `strata generate image …` → `strata generate video "<motion>" --first-frame <its url>` (**ONE continuous shot, no cuts** — see [video-generation.md](video-generation.md)) → then one of the rows above | a generated still becomes a clip first; ask for a **solid green background** in the prompt if you intend to key it |

`--width N` downscales an oversized overlay — a 960×960 source rarely needs full
resolution, and it cuts the `.jet` size a lot. Any **video** input needs `ffmpeg` on PATH
(it decodes the clip); a PNG sequence needs nothing.

### `matte` is slow — halve the width first (MEASURED)
AI matting is the slowest thing in the toolchain: it runs a neural net on **every frame**
on the CPU. On a 144-frame 1280×720 clip:

| run | time | `.jet` size |
|---|---|---|
| full 1280 wide | **2m42s** | 34.5 MB |
| `--width 640` | **24.7s** | 10.6 MB |

**6.6× faster and 3× smaller** — so unless the subject fills the frame at full res, pass
`--width 640` (or 720). An overlay is composited over a busy scene and usually scaled
down anyway, so the resolution is rarely doing any work. This is the reliable lever: it
shrinks every stage (decode → net → jet encode) and behaves the same on any machine.

`--threads N|auto` also exists (default **1**). Measured on a 32-core box it burns ~8× the
CPU for an erratic wall-clock win — sometimes a loss — so it is opt-in, not default;
`auto` scales to the CPU count, and anything unusable falls back to 1 thread rather than
failing. Reach for `--width` before `--threads`.
**Tell the user roughly how long a matte will take** before starting a long one, and run
it in the background rather than blocking.

### Quality — `.jet` is lossy; the default is Idomoo's own reference setting
`--quality draft|good|high|max` (default **draft**). Despite the name, `draft` is
**not** a compromise: `[8,8,4,4]` is exactly what Idomoo's reference encoders hardcode,
so it is what every `.jet` from the AE plugin has always used.

| preset | quant factors | size (144f 720p matte) |
|---|---|---|
| **draft** (default) | 8,8,4,4 | **34.5 MB** |
| good | 4,4,2,2 | ~50 MB |
| high | 2,2,1,1 | ~65 MB |
| max | 1,1,1,1 | 81.0 MB |

**Verified side by side in a jet viewer:** `draft` vs `max` is indistinguishable —
RGB PSNR 37.5 dB, **alpha PSNR 53.0 dB**, mean edge-alpha error 3.96/255. Crucially the
residual alpha in "empty" regions is **7/255 at both settings**, so finer quantisation
never buys cleaner transparency — only RGB precision inside the subject. `max` costs
**2.4× the bytes** for that.

So: **leave the default alone.** Step up to `high`/`max` only when a specific clip
actually shows artefacts (fine hard-edged alpha — confetti, thin type, vector shapes — is
the plausible case; soft/photographic subjects are not). If a `.jet` is too big, reduce
`--width` first: halving resolution quarters the data. (`max` is the ceiling regardless:
the format caps coefficients at ±1022 and a flat DCT's DC term reaches ~4080, so the base
matrix's ÷4 is what makes them fit — going finer corrupts the file.)

### ⚠️ Key the VIDEO, not a still — the motion belongs in the clip
Always matte/key the **moving clip**, so the subject's motion is carried by the `.jet`
itself and the layer stays still (`box` = full frame, no `position` animation). Do this
**even when the user never asks for keying** — it is what separates a filmed-looking
overlay from a sticker.

**The failure mode, seen in a real project:** a plane was matted from a clip whose subject
barely moved (centre drifted ~35px over 80 frames), and the "flight" was then faked by
translating the cut-out `position: [-260,380] -> [1540,265]`. On screen that reads as a
paper plane sliding across the picture — no parallax, no perspective change, no banking.

Compare the beach example above: the woman's walk is *inside* the matted clip, the layer
never moves, and it looks filmed.

**Image-to-video models frequently hover the subject rather than translate it.** After
generating, verify it actually travelled — compare the subject between the first and last
frames, or run `strata track --point x,y` and read the reported travel. If it barely moved,
**re-prompt the clip** stating the displacement explicitly ("crosses from the left edge to
the right edge, camera static"); never compensate by animating the layer.

### Text BEHIND the subject — the SAME clip, used twice

**This is the single easiest thing to get wrong, so the rule is absolute: the background
plate and the matted overlay must be THE SAME CLIP.** The subject is cut out of the very
frames it is sitting in, so it lines up perfectly and the text slides between the two
copies.

```json
{ "width":1280, "height":720, "fps":24, "layers": [
  { "type":"video", "name":"plate",   "src":"./clip.mp4",     "box":[0,0,1280,720], "fit":"fill" },
  { "type":"text",  "name":"headline","text":"SUMMER", "size":210, "box":[0,230,1280,240], "align":"center middle" },
  { "type":"video", "name":"subject", "src":"./clip.jet",     "box":[0,0,1280,720] }
] }
```
Read it as three layers, bottom to top:
1. **`clip.mp4`** — the untouched clip, full frame. The background.
2. **the text** — anything that should appear *behind* the subject.
3. **`clip.jet`** — `strata matte clip.mp4 -o clip.jet`, i.e. **that same clip** with its
   background removed. The subject, back on top of itself.

Both video layers are **full frame at the same box**, and the `.jet` layer gets **no
`position` animation** — the subject's movement is already inside it.

❌ **The failure to avoid: matting clip A and laying it over clip B.** That is not this
effect — it is just an overlay of one video on another, the subject will not line up with
anything behind it, and the "behind" illusion never happens. If the two `src` values are
different clips, it is wrong. Same clip in, same clip matted, text in between.

*(Layering an unrelated subject over a different background — a mascot, a logo sting, a
product cut-out — is a perfectly good separate technique. It just is not "text behind the
subject", and should not be confused with it.)*

**Judgement:** matting is excellent on clear subjects, decent edges, and is temporally
stable (it's a video model, so edges don't crawl). For hero shots with fine flyaway hair or
heavy motion blur, a proper roto in After Effects / Resolve still wins — ask for a **PNG
sequence with alpha** and use the PNG row above.
