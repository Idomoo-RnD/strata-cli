# Alpha overlays — `.jet`, `matte`, chroma key and same-source occlusion

Anything composited OVER another layer (a plane, a mascot, a product cut-out, a logo sting, a
person) needs alpha, and the only video format the engine composites with alpha is `.jet`. This
file is how to get any source into one — `strata jet` from frames or a keyed clip, `strata matte`
for AI background removal (people and characters only) — plus the same-clip occlusion trick that
needs no alpha at all, the quality settings, and the traps (`matte` is slow, `.jet` is lossy,
key the VIDEO not a still). The decision *whether* a layer needs alpha is in
[assets.md](assets.md); the layer syntax is in [format.md](format.md).

## Contents

- [Alpha overlays — `.jet`, and how to get anything into it](#alpha-overlays--jet-and-how-to-get-anything-into-it)
  - [Same-source occlusion — text behind a subject with NO alpha at all](#same-source-occlusion--text-behind-a-subject-with-no-alpha-at-all)
  - [Getting to `.jet` — pick the row that matches the source](#getting-to-jet--pick-the-row-that-matches-the-source)
  - [`matte` is slow — halve the width first (MEASURED)](#matte-is-slow--halve-the-width-first-measured)
  - [Quality — `.jet` is lossy; the default is Idomoo's own reference setting](#quality--jet-is-lossy-the-default-is-idomoos-own-reference-setting)
  - [⚠️ Key the VIDEO, not a still — the motion belongs in the clip](#key-the-video-not-a-still--the-motion-belongs-in-the-clip)
  - [Text BEHIND the subject — the SAME clip, used twice](#text-behind-the-subject--the-same-clip-used-twice)

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
