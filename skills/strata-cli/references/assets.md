# Asset generation — image, image-to-video, narration, music

The CLI generates media via the Idomoo AI API (Lucas). Needs auth (`strata auth login`).
Each command saves the file locally (default `./strata_assets/`, or `-o <file>` /
`--out-dir <dir>`) and prints the local path **and** a hosted URL; add `--json` for
machine-readable output. Image/video are **async** (polled to completion); narration is sync.

**The chain:** image → animate it into a video → narration + music for the audio bed →
point the scene's `src`/`audio` at the saved files.

**Local files vs URLs — both work everywhere.** Every endpoint that takes an image accepts
either a hosted URL or a **local file path** (the CLI base64-encodes it into a data-URI; no
upload step). So `--reference ./mascot.png` and `generate video ./hero.png` just work.

---

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

## `strata generate video <image> [flags]`  — image-to-video
An MP4 clip animated from a still (async, ~1–3 min). The positional `<image>` is a hosted
URL **or a local file path** (auto-base64).

| flag | meaning |
|---|---|
| `--prompt "<motion>"` | describes the camera/movement (e.g. "slow push-in, gentle parallax") |
| `--duration <sec>` | clip length (default 5) |
| `--ratio <e.g. 9:16>` | output aspect |

Reference the result as a `video` layer; set `loop: true` to hold it for the comp's duration.
Typical flow: `generate image` → feed its printed url (or the saved file) straight into
`generate video`.

```bash
strata generate video ./hero.png --prompt "slow cinematic push-in" --duration 5 --ratio 16:9
strata generate video https://…/image.png --prompt "subtle idle bob"
```

---

## `strata generate narration "<text>" --voice <voice_id>`
TTS voiceover MP3 (sync). Returns the spoken **duration** in seconds — size the scene around it.
- `strata generate voices [--search <text>]` lists `voice_id  name · gender · accent · use-case`.
- `--voice <voice_id>` (required) · `--normalize <mode>` for text normalization.
Reference the MP3 as an `audio` layer. Pair the personalized value with the visual so it lands.

## `strata generate music "<prompt>" [--duration <sec>]`
An instrumental track (default 30s). Reference as an `audio` layer at low `volume` with
`ducking: true` so it sits under narration.

---

## Every image becomes a video — no still photos
**Any image used as a visual in the scene gets animated with `generate video` before it goes
in.** Backgrounds, hero shots, scenery, products, people — all of them. Do not ask first and
do not leave the still in: a static photo in a motion-design piece reads as a slideshow.

**The only exception is a genuine icon / logo / flat UI graphic**, where motion would look
wrong. (A slow Ken-Burns on a still is a fallback only when image-to-video is unavailable.)

⚠️ **Check every `src` before compiling.** A real failure seen in the wild: `sky.mp4` was
generated and then the scene still pointed at `sky.png`, so the background sat frozen. If a
video exists for an asset, the scene must reference the video.

---

## Alpha overlays — `.jet`, and how to get anything into it

An **MP4 has no alpha**, so a clip laid over the scene arrives as an opaque rectangle.
Idomoo's alpha format is **`.jet`** (IDMJET, YUVA420 — alpha is a real plane). Every
overlay clip is a `.jet` used as a `video` layer. **Its fps must match the source clip and
the scene**, or the overlay drifts out of time — the CLI prints the fps it used.

### Getting to `.jet` — pick the row that matches the source
| I have | command | notes |
|---|---|---|
| **Ordinary footage, no green screen** (a person on a beach, in an office…) | `strata matte clip.mp4 -o subject.jet` | **removes the background automatically** (AI video matting, runs locally on CPU). First run downloads a ~14 MB model into `~/.strata/models/`. |
| **Green/blue screen footage** | `strata jet clip.mp4 --key 0,177,64 --method chroma -o o.jet` | add `--choke 1 --feather 1` to trim a colour fringe |
| **A solid white/black background** | `strata jet clip.mp4 --key 255,255,255 -o o.jet` | `distance` method is the default |
| **A PNG sequence that already has alpha** (AE/Blender/Nuke/Resolve export, or roto) | `strata jet ./frames --fps 24 -o o.jet` | **best quality — no keying at all.** The frames' own alpha is used automatically; only pass `--key` if you actually want a colour keyed |
| **A still image I generated** | `strata generate image …` → `strata generate video <img>` → then one of the rows above | a generated still becomes a clip first; ask for a **solid green background** in the prompt if you intend to key it |

`--width N` downscales an oversized overlay — a 960×960 source rarely needs full
resolution, and it cuts the `.jet` size a lot. Any **video** input needs `ffmpeg` on PATH
(it decodes the clip); a PNG sequence needs nothing.

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
