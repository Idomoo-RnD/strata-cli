# Generating video — `strata generate video`

AI video generation (Seedance). **One command, five modes**, chosen by which inputs you
give it — never by a mode flag:

| Inputs | Mode |
|---|---|
| prompt only | **text-to-video** |
| `--first-frame` | **image-to-video** — animates that exact frame |
| `--first-frame` + `--last-frame` | **keyframe interpolation** — you author both ends |
| `--ref-image` / `--ref-video` / `--ref-audio` | **reference-driven** — your character, world, camera plan, voice |
| a source clip in `--ref-video` | **editing / extension** ([advanced](video-generation-advanced.md)) |

```bash
strata generate video "<prompt>" [--first-frame <url>] [--last-frame <url>]
   [--ref-image <url>]... [--ref-video <url>]... [--ref-audio <url>]...
   [--duration 4..15] [--ratio 16:9] [--seed N] [--camera-fixed] [--audio]
   [--last-frame-out <file>] [--realistic-human] [--fast] [-o out.mp4]
```

Everything else — references, dialogue, shot plans, editing — is in
**[video-generation-advanced.md](video-generation-advanced.md)**.

---

## 🚫 The one hard rule: frames and references are mutually exclusive

**`--first-frame` / `--last-frame` can NEVER be combined with `--ref-image`, `--ref-video`
or `--ref-audio`** — not one of them, in any combination. The CLI blocks it before spending
anything; the API returns a `422`.

So decide before you write the prompt:

| | Gives you |
|---|---|
| **Frame-driven** (`--first-frame` / `--last-frame`) | exact control of the opening and/or closing frame |
| **Reference-driven** (`--ref-*`) | your character, world, camera plan or voice — but no exact frame |

Need both? Generate the reference-driven clip first, then **chain** off its last frame
(below) — the chained clip is frame-driven and inherits the look the references established.

## 🟢 `--realistic-human` — the answer to content rejections

Registers the input images in the Vault Asset Library before dispatch so photorealistic
people pass the privacy pre-filter; the registered assets are deleted when the task ends.

**The CLI applies it automatically** when a call is rejected on content grounds and the
inputs qualify — it retries once and tells you (`realistic_human was applied`). Pass
`--realistic-human` explicitly when you already know you need it.

Set it (or expect the auto-retry) whenever an input image contains:

- **realistic humans** — any recognisable human face. *Measured:* the filter does **not**
  care that the person is AI-generated; a synthetic portrait is rejected exactly like a photo.
- **branded content** — company logos, real products, recognisable brand marks.
- **anything that returned a content/privacy/harmful error** — this flag is the first fix,
  not a prompt rewrite.

*Measured*, one variable changed and nothing else:

| | Result |
|---|---|
| Photoreal portrait as `--first-frame`, no flag | ❌ `InputImageSensitiveContentDetected.PrivacyInformation` |
| Identical call **with** the flag | ✅ generated in 180 s |

**Only applies to `--first-frame`, `--last-frame`, `--ref-image`.** Passing it with only
`--ref-video`, or with no media at all, is a hard error — and that is fine: *measured*, a
reference **video** full of photoreal people (including a face close-up) is accepted with no
flag at all, because the privacy pre-filter does not apply to video inputs.

## Media inputs are URLs

`--first-frame`, `--last-frame` and every `--ref-*` take **URLs**; base64 data-URIs are
rejected. Most assets already have one — every `strata generate` command prints a hosted
`url:`, and so does `strata render`. **Use that string; never re-upload a generated asset.**

`strata upload <file>` is only for an input with no URL at all: the user's own footage, or
something we rendered locally (a `strata sketch` animatic, an ffmpeg frame grab). See
[assets.md](assets.md).

## Parameters

| Flag | Notes |
|---|---|
| `--duration` | 4–15 s, default 5 |
| `--ratio` | `16:9` `9:16` `1:1` `4:3` `3:4` `21:9` `adaptive`. *Measured:* `adaptive` snaps to the nearest standard ratio and crops — it does **not** preserve an unusual source aspect |
| `--seed` | reproducibility; keep it fixed across a series |
| `--camera-fixed` | locks the camera — useful when VASCO will do its own camera work |
| `--audio` | native synced audio. *Measured:* real AAC 44.1 kHz stereo |
| `--last-frame-out <file>` | saves the last frame for chaining. **Do it now** — the URL is signed and expires in 24 h |
| `--fast` | the fast model: ~1.5× quicker, but *measured* it dropped a shot (4 delivered of 5). Use when shot count does not matter |
| `--resolution` | **clamped to 720p** by the CLI. The standard model will happily return 1080p if asked, so the discipline lives here |

Generation takes **3–9 minutes**. Run it in the background and report the URL.

---

## Writing the prompt

A prompt is a **shot list with an identity lock**, not a sentence. All of these sections,
in this order:

### 1. `Style & Mood:` — one line
Genre, tone and lighting together: *"naturalistic documentary portrait, cinematic, warm
morning chiaroscuro — hard low sun raking through old glass against a deep unlit interior."*

### 2. One theme, in CAPS
*"This video is about STILLNESS and LIGHT."* One idea disciplines the motion; two produce mush.

### 3. Identity lock — state it THREE times
Physical detail, never adjectives:

> early thirties, pale skin with visible pores and light freckles across the nose, no makeup,
> dark brown shoulder-length curly hair worn loose and slightly frizzy, straight dark
> eyebrows, calm grey-green eyes. Loose olive-green linen shirt, sleeves pushed to the elbow,
> dark indigo jeans, small speckled cream mug in her right hand.

Then repeat the invariants **inside the close-up shot** and **again in the Static
Description**. Three statements. Never assume the model remembers.

### 4. Location — one paragraph with depth
A foreground plane, the subject, and a background that falls away. Name the light source and
where it dies. Depth is what makes camera moves read as real.

### 5. Numbered shots with `Cut.` — never timecodes

> ⛔ **Skip this entirely for a clip destined to be matted into a `.jet` overlay, and for
> keyframe interpolation** — both are ONE continuous shot. See the `.jet` rule below.

`Shot 1: … Cut. Shot 2: …`, and two rules:

- **Double contrast** — every shot changes **both** shot size and camera character: wide
  low-angle → macro → face ECU → orbiting medium → wide push-back.
- **Anchor body parts in CAPS** — "close-up on her HANDS only", "her FACE filling the frame".
  Without this the framing drifts to generic mid-shots.

Add a hold on the face shot: *"hold this shot long enough to clearly read her face."*

### 6. `Static Description:` — the invariants
Set + light + identity that must hold in **every** shot. This is the line that stops wardrobe
and room mutating between cuts. End it with the negatives you need ("no text on screen").

### 7. `Audio:` — always present, always specific
Even for near-silence: *"soft slow breathing, the faint creak of an old window frame, a
distant city hum. No music. No speech."* Needs `--audio`.

### 8. Physics, not adjectives
"dust motes drift in the beam", "thumb moving slowly over the glaze", "linen folds catching
and losing the light". Concrete physical events are what the model can animate. "Beautiful"
is not.

### What to expect *(measured)*

A 12 s, 5-shot prompt returned **exactly 4 cuts**, each shot on brief. But shot lengths came
back **uneven** — 1.4 / 2.2 / 2.0 / 3.3 / 3.2 s. The model paces the edit itself; it does
**not** divide the duration evenly. **Budget ~5 shots per 12 s** (≤4 when references are
attached) — asking for more merges or drops them.

---

## ⛔ Clips destined for a `.jet` alpha overlay — NO shots, NO cuts

When the clip will be matted (`strata matte` → `.jet`) to sit as a transparent overlay, the
shot-list approach is **wrong** and produces an unusable matte.

**Generate ONE continuous shot.** Say it explicitly — `ONE single continuous shot, no cuts.`
— and drop `Shot N:` / `Cut.` entirely. Keep everything else.

**Why:** matting runs per frame with temporal smoothing, so a hard cut is a discontinuity it
cannot follow. The matte flickers or collapses either side of every cut; each shot's different
subject scale means no single edge treatment works; and shots where the subject is absent or
reduced to a macro detail produce useless overlay frames. **An overlay layer is a single
continuous performance — the edit happens in the scene, around the overlay.**

Also prompt for a clean matte: an evenly-lit plain contrasting backdrop, the subject whole and
never leaving frame, `--camera-fixed` (or a very slow drift), and avoid what mattes badly —
wispy hair, steam, glass, heavy motion blur, silhouetting backlight. Skip `--audio` (an
overlay's audio is discarded) and keep it short.

---

## Image-to-video — `--first-frame`

*Measured:* **`--first-frame` is literal.** Video frame 0 *is* the source image — same pose,
same composition, same light. So write **Shot 1 to begin on it** ("begin exactly on the first
frame and bring it to life") rather than describing a different opening.

⚠ **Anything outside the source frame is invented.** *Measured:* a wide push-back moved the
window to centre frame, added furniture that was never there, and rendered the character
smaller and younger. `--first-frame` locks the character and the framing you gave it — it does
**not** lock the set. Keep the shot list within or tighter than the source framing, or
describe the wider room explicitly.

## Keyframe interpolation — `--first-frame` + `--last-frame`

The most directable mode: you author both ends as images and the model only solves the motion.
*Measured:* **0 cuts, both keyframes landed**, and it was the fastest mode (139 s).

**Generate the end frame FROM the first**, or the model spends the clip reconciling two
different worlds:

```bash
strata generate image "<the START pose>" -o a.png
strata generate image "The SAME character as image 0 … <the END pose>" --reference a.png -o b.png
```

**The prompt is ONE continuous move, not a shot list.** Drop `Shot N:`/`Cut.`; state *"ONE
single continuous shot, no cuts"*; describe the motion physically in order; and name the ends
explicitly — *"She begins exactly as in the first frame … she ends exactly as in the last
frame."* Keep it short (≈5 s) — a long duration just invents filler between two fixed poses.

## Chaining — clips longer than 15 s

`--duration` caps at 15. To go longer: generate, save the last frame, upload it, make it the
next clip's first frame.

```bash
strata generate video "<shot 1>" --duration 12 --last-frame-out lf.png -o clip1.mp4
strata upload lf.png                      # -> https://t.idomoo.com/<uuid>.png
strata generate video "<shot 2>" --first-frame <that url> --duration 12 -o clip2.mp4
printf "file '%s'\nfile '%s'\n" clip1.mp4 clip2.mp4 > c.txt
ffmpeg -f concat -safe 0 -i c.txt -c copy -y joined.mp4
```

*Measured:* the continuation started exactly where the previous clip ended — same room,
wardrobe, character, camera. Every clip comes back with identical stream parameters
(`h264 / 1280×720 / yuv420p / 24 fps` + `aac / 44100 / stereo`), so the join is a lossless
stream copy.

⚠ **One duplicate frame at every seam** — clip N's last frame *is* clip N+1's first frame
(*measured:* mean pixel difference 3.27/255). At 24 fps that is a 42 ms hold and is invisible;
prefer the plain `-c copy` concat. Only trim if you are stacking many clips.

⚠ **Audio does not chain** — each clip gets its own track, so ambience restarts at every join.
For a multi-clip piece, generate without `--audio` and lay one continuous bed over the join.

**Chaining vs keyframes:** chaining's boundary is whatever the model landed on; keyframes let
you *author* the boundary. Keyframes give more control, chaining is cheaper. They compose.

---

## `--fast` and the old command

- **`--fast`** switches to the fast model: ~1.5× quicker, and it rejects anything above 720p
  outright. *Measured:* it delivered 4 of 5 requested shots. Use it when shot count does not
  matter.
- **`strata generate fastvideo <image>`** is the OLD image-to-video endpoint, kept for speed.
  **Use it only when the user or a workflow explicitly asks for fast mode.** It takes an image
  (local file or URL) and animates it — no text-to-video, no keyframes, no references, no
  audio, no editing. Everything else should go through `generate video`.

## Errors

A wrong `--model` fails as `500` wrapping `Seedance API 404: InvalidEndpointOrModel.NotFound`.
A wrong *parameter* also surfaces as `500` wrapping a `400`. **Always read the nested detail —
the outer status tells you nothing.** Validation errors from the CLI's own checks (frames +
references, `--realistic-human` misuse) fail instantly and cost nothing.

## Checklist

- [ ] Frame-driven **or** reference-driven — never both
- [ ] `Style & Mood:` opens it; one theme in CAPS
- [ ] Identity written physically and repeated **three** times
- [ ] Shots numbered with `Cut.`, each changing size **and** camera; body parts in CAPS
- [ ] …unless it is a `.jet` overlay or a keyframe clip → **ONE continuous shot, no cuts**
- [ ] `Static Description:` restates set + identity; negatives stated
- [ ] `Audio:` specific, with `--audio`
- [ ] ≤5 shots per 12 s (≤4 with references)
- [ ] Input has a real face, a logo or a real product → expect `--realistic-human`
- [ ] `--last-frame-out` if this clip will be chained (24 h expiry)
- [ ] Run it in the background — 3–9 minutes
