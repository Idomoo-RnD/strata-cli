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

References, dialogue, animatics and editing are in
**[video-generation-advanced.md](video-generation-advanced.md)**.

---

## Contents

- [🚫 The one hard rule: frames and references are mutually exclusive](#the-one-hard-rule-frames-and-references-are-mutually-exclusive)
- [🟢 `--realistic-human` — the answer to content rejections](#--realistic-human--the-answer-to-content-rejections)
- [Media inputs are URLs](#media-inputs-are-urls)
- [Parameters](#parameters)
  - [🔊 A clip generated with `--audio` must keep its audio downstream](#a-clip-generated-with---audio-must-keep-its-audio-downstream)
- [Writing the prompt](#writing-the-prompt)
  - [1. `Style & Mood:` — one line](#1-style--mood--one-line)
  - [2. One theme, in CAPS](#2-one-theme-in-caps)
  - [3. Identity lock — state it THREE times](#3-identity-lock--state-it-three-times)
  - [4. Location — one paragraph with depth](#4-location--one-paragraph-with-depth)
  - [5. Numbered shots with `Cut.` — never timecodes](#5-numbered-shots-with-cut--never-timecodes)
  - [6. `Static Description:` — the invariants](#6-static-description--the-invariants)
  - [7. `Audio:` — always present, always specific](#7-audio--always-present-always-specific)
  - [8. Physics, not adjectives](#8-physics-not-adjectives)
  - [What to expect *(measured)*](#what-to-expect-measured)
- [Worked example — the prompt that produced the result above](#worked-example--the-prompt-that-produced-the-result-above)
- [⛔ Clips destined for a `.jet` alpha overlay — NO shots, NO cuts](#clips-destined-for-a-jet-alpha-overlay--no-shots-no-cuts)
  - [Prompt for a clean matte, not just a nice shot](#prompt-for-a-clean-matte-not-just-a-nice-shot)
- [Image-to-video — `--first-frame`](#image-to-video----first-frame)
- [Keyframe interpolation — `--first-frame` + `--last-frame`](#keyframe-interpolation----first-frame----last-frame)
- [Locking the camera — in the prompt, not the flag](#locking-the-camera--in-the-prompt-not-the-flag)
- [Chaining — clips longer than 15 s](#chaining--clips-longer-than-15-s)
- [⛔ A clip shorter than its scene is NEVER stretched — cover the gap with more shots](#a-clip-shorter-than-its-scene-is-never-stretched--cover-the-gap-with-more-shots)
- [Two different things are called "fast" — don't confuse them](#two-different-things-are-called-fast--dont-confuse-them)
- [Errors](#errors)
- [Checklist](#checklist)

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

Set it (or expect the auto-retry) whenever an input image contains **realistic humans** (any
recognisable human face), **branded content** (logos, real products, brand marks), or whenever
a call returned a content/privacy/harmful error — this flag is the first fix, not a prompt
rewrite. The rejection looks like:

```
Seedance API 400: InputImageSensitiveContentDetected.PrivacyInformation
"the input image 'content[1]' may contain real person"
```

*Measured* — the filter does **not** care that the person is AI-generated; a synthetic
portrait is rejected exactly like a photo:

| Input | Default | With the flag |
|---|---|---|
| Photoreal portrait as `--first-frame` | ❌ rejected | ✅ generates (180 s) |
| Same image, `--fast` | ❌ same error | — |
| Same image as `--ref-image` | ❌ same error | ✅ generates |
| `--first-frame` + `--last-frame`, both photoreal people | ❌ | ✅ generates, hits both keyframes |
| **Cartoon / stylized 3D character** | ✅ accepted | flag unnecessary |
| **Wide shot where the face is small** | ✅ accepted | flag unnecessary |

**Only applies to `--first-frame`, `--last-frame`, `--ref-image`.** Passing it with only
`--ref-video`, or with no media at all, is a hard error — and that is fine: *measured*, a
reference **video** full of photoreal people (including a face close-up) is accepted with no
flag at all, because the privacy pre-filter does not apply to video inputs. Products,
interiors, landscapes and face-free images are unaffected either way.

## Media inputs are URLs

`--first-frame`, `--last-frame` and every `--ref-*` take **URLs**; base64 data-URIs are
rejected. Most assets already have one — every `strata generate` command prints a hosted
`url:`, and so does `strata render`. **Use that string; never re-upload a generated asset** —
it wastes a request and leaves a duplicate permanent public copy.

`strata upload <file>` is only for an input with no URL at all: the user's own footage, or
something we rendered locally (a `strata sketch` animatic, an ffmpeg frame grab). *Measured:*
`t.idomoo.com` URLs are accepted for both image and video references. Scene assets — including
`.jet` overlays — are never uploaded; they are local paths embedded in the `.idm`. See
[assets.md](assets.md).

## Parameters

| Flag | Notes |
|---|---|
| `--duration` | 4–15 s, default 5 |
| `--ratio` | `16:9` `9:16` `1:1` `4:3` `3:4` `21:9` `adaptive`. *Measured:* `adaptive` snaps to the nearest standard ratio and crops (1376×768 → 1280×720) — it does **not** preserve an unusual source aspect. With references there is nothing to infer from, so **set it explicitly** |
| `--seed` | reproducibility; keep it fixed across a series |
| `--camera-fixed` | ⛔ **rejected by the current model on every task type** (*measured:* t2v, i2v and r2v all answer `camera_fixed … must be empty`; the CLI now refuses it before spending the request). **Lock the camera in the prompt instead** — see *Locking the camera* below |
| `--audio` | native synced audio. *Measured:* real AAC 44.1 kHz stereo. ⚠ **Anything you do to this clip afterwards must keep that track** — see below |
| `--last-frame-out <file>` | saves the last frame for chaining. **Do it now** — the URL is signed and expires in 24 h |
| `--best` | the **standard** model instead of the default fast one: slower, but it delivers every shot asked for. Pass it when the storyboard needs all its shots (`--fast` still parses and is now a no-op — fast is the default) |
| `--resolution` | **clamped to 720p — always, never more.** That cap is why fast is the default: full resolution is the only thing the standard model buys above it |

Generation takes **3–9 minutes**. Run it in the background and report the URL — and when a job has several clips, launch every independent one **at once** and build the scene while they render ([assets.md](assets.md), *Generate in waves*).

### Which model runs — fast is the default

| | Model id | When |
|---|---|---|
| **default** | `dreamina-seedance-2-0-fast-260128` | every call. ~1.5× quicker, and it refuses anything above 720p — which costs nothing, because output is clamped to **720p always** |
| `--best` | `dreamina-seedance-2-0-260128` | when the storyboard needs **every shot to land**: the fast model's one measured cost is shot count (a 5-shot ask returned 4) |
| `--model <id>` | any id the account has activated | an explicit override, only when asked for. An unactivated id fails with *"account has not activated the model"* — say so rather than retrying |

720p is the ceiling in every case, so the standard model buys nothing but shot reliability here.
`--fast` still parses and does nothing (fast is already the default). Every other measured number
on this page was taken on the standard model; shot delivery is the only figure the fast model
changes.

### 🔊 A clip generated with `--audio` must keep its audio downstream

Sound design and lip-synced dialogue are generated **into the clip** — they are not a
separate track you can re-attach. If a trim, reframe, retime or concat drops the audio, the
fix is **re-generating the whole clip (3–9 minutes)**, not re-running the edit. That is why
this is worth checking every single time.

The trap is one flag: **ffmpeg keeps audio automatically until the command contains a
`-map`** — then it keeps only what you map, and video-only mapping discards the audio
silently, exit code 0. So any `-map "[v]"` needs `-map 0:a -c:a copy` (or a filtered audio
label) beside it, and **`ffprobe` the result before using it**:

```bash
ffprobe -v error -show_entries stream=codec_type,duration -of csv=p=0 out.mp4
# want TWO lines, video and audio, with matching durations
```

Full measured table of which patterns keep audio: [video-editing.md](video-editing.md).

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

> ⚠️ **Montage verbs cut the clip even when there is no shot list.** *"Cut to"*, *"then we
> see"*, *"meanwhile"*, *"next"*, *"later"*, *"intercut"* are read as edit instructions
> **wherever they appear** — including in plain prose you never intended as an edit.
> *Measured:* a prose brief with no `Shot N:` and no `Cut.`, containing only "We see her
> unlock the door. Cut to her flipping the sign." returned **3 hard cuts in a 5 s clip**
> (scene scores 0.47 / 0.60 / 0.65). Describe **one continuous action** instead — *"she
> unlocks the door, crosses to the counter and starts the machine"*. And whenever a clip is
> meant to be a single take, **say so explicitly**: the identical prompt plus `ONE single
> continuous shot, one unbroken take, no cuts` returned **0 cuts** (max scene score 0.07).
> This is not only a `.jet` rule — it applies to **any** clip you intend as one shot.

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

A 12 s, 5-shot prompt returned **exactly 4 cuts → the 5 shots requested**, each on brief
(wide at the window → macro on HANDS → face ECU in profile → medium torso → wide push-back),
and identity held across all five. But shot lengths came back **uneven** — 1.38 / 2.21 / 1.96
/ 3.33 / 3.22 s. The model paces the edit itself; it does **not** divide the duration evenly,
so never plan on `duration / shots`. The fast model on the same prompt delivered **4 shots**.

**Budget ~5 shots per 12 s** (≤4 when references are attached) — asking for more merges or
drops them.

---

## Worked example — the prompt that produced the result above

```text
Style & Mood: naturalistic documentary portrait, cinematic, warm morning chiaroscuro -
hard low sun raking through old glass against a deep unlit interior. This video is about
STILLNESS and LIGHT.

The woman is the same person in every shot: early thirties, pale skin with visible pores
and light freckles across the nose, no makeup, dark brown shoulder-length curly hair worn
loose and slightly frizzy with fine flyaway strands catching the light, straight dark
eyebrows, calm grey-green eyes, a soft unsmiling mouth. She wears a loose olive-green
linen button-down shirt with the sleeves pushed to the elbow, tucked into dark indigo
jeans, and holds a small speckled cream ceramic mug in her right hand. Her face,
freckles, curly hair and olive linen shirt must be identical in every single shot.

Location: the corner of a quiet red-brick loft in early morning. A tall wooden sash
window with peeling white paint fills the left of the frame, hard sunlight pouring
through it and falling off fast into a deep shadowed interior of exposed brick, a wooden
shelf and large monstera and fiddle-leaf plants sinking into darkness. Dust motes drift
slowly through the beam.

Use five distinct shots with slow deliberate cuts, each changing both shot size and
camera character. Shot 1: full-body wide shot from inside the dark room looking toward
the window, she stands three-quarters to camera in the pool of light, one HAND resting
flat on the window frame, breathing slowly, linen shifting on her shoulder. Cut.
Shot 2: tight macro close-up on her HANDS only, the fingers of her right hand wrapped
around the warm ceramic mug, thumb moving slowly over the glaze, faint steam rising and
bending in the light, sunlight rimming her knuckles against black. Cut. Shot 3: EXTREME
CLOSE-UP on her FACE filling the frame in profile, the same freckles, grey-green eyes and
dark curly hair, eyes half-closed then slowly opening, a single flyaway strand drifting
across her cheek, hard side light sculpting her brow and cheekbone against a pitch-black
background - hold this shot long enough to clearly read her face. Cut. Shot 4: medium
shot on her upper BODY from a slow orbiting camera, shoulders turning gently as she lifts
the mug toward her chest, linen folds catching and losing the light, plants drifting past
behind her in soft focus. Cut. Shot 5: full-body wide again from a different side angle,
slow camera push back, she turns her HEAD to look out through the glass and settles into
stillness, the beam and drifting dust holding as the final frame settles.

Static Description: brick loft corner at early morning, single hard window key light from
the left, deep falloff to black, volumetric haze and drifting dust, shallow depth of
field; the same woman throughout - dark curly hair, freckles, grey-green eyes, olive linen
shirt, indigo jeans, speckled cream mug - identical in every single shot.

Audio: near silence - soft slow breathing, the faint creak of an old window frame, a
distant muffled city hum outside the glass, the small ceramic sound of the mug set down.
No music. No speech. Slow, unhurried pacing throughout.
```

---

## ⛔ Clips destined for a `.jet` alpha overlay — NO shots, NO cuts

When the clip will be matted (`strata matte` → `.jet`) to sit as a transparent overlay, the
shot-list approach is **wrong** and produces an unusable matte. This is a hard rule.

**Generate ONE continuous shot.** Say it explicitly — `ONE single continuous shot, no cuts.`
— and drop `Shot N:` / `Cut.` / "five distinct shots" entirely. Keep `Style & Mood:`, the
identity lock, `Static Description:` and the physical motion description; only the shot list
goes.

**Why cuts break matting.** Background removal runs **per frame with temporal smoothing** —
each frame's matte is informed by its neighbours — and a hard cut is a discontinuity it
cannot follow:

- the matte **flickers or collapses** for several frames either side of every cut;
- each shot has a different subject scale and framing, so no single edge treatment serves them
  all — halo on some shots, chewed edges on others;
- shots where the subject is **absent or reduced to a detail** (a macro on hands, a wide where
  the subject is 40 px tall) produce overlay frames that are useless in a layout;
- the composite reads as a glitch, not an edit, because the layer underneath does not cut with it.

**An overlay layer is a single continuous performance. The edit happens in the scene, around
the overlay.**

### Prompt for a clean matte, not just a nice shot

- **Separate subject from background** — an evenly-lit, plain, contrasting backdrop: a flat
  colour, a clean seamless wall, or solid green/blue if you intend to key. Never a background
  sharing the subject's colours.
- **Keep the subject whole and in frame throughout.** A subject that leaves frame or is
  cropped by the edge mattes badly at that boundary.
- **A locked-off camera, stated in the prompt** (the `--camera-fixed` flag is rejected by the
  model — see *Locking the camera*), or at most a very slow drift — camera movement changes the
  background continuously and gives the matte more to get wrong.
- **Avoid what mattes badly**: wispy or flyaway hair, smoke, steam, water spray, glass, veils,
  heavy motion blur, and backlight strong enough to silhouette. Say so in the prompt.
- **Full body or a clean waist-up**, held throughout, so the overlay is reusable.
- **No `--audio`** — an overlay's audio is discarded, so don't pay for it.
- Keep it **short** (4–6 s), and end near where it began if it needs to loop.

Then: `strata matte <clip.mp4> -o overlay.jet`. See [assets.md](assets.md) for matte/jet
mechanics and [video-layouts.md](video-layouts.md) for composing an alpha overlay.

---

## Image-to-video — `--first-frame`

*Measured:* **`--first-frame` is literal.** Video frame 0 *is* the source image — same pose,
same composition, same light. So write **Shot 1 to begin on it** ("begin exactly on the first
frame and bring it to life") rather than describing a different opening. You are animating
that frame, not being inspired by it.

⚠ **Anything outside the source frame is invented.** *Measured:* a wide push-back moved the
window to centre frame, added furniture that was never there, and rendered the character
smaller and younger. **`--first-frame` locks the character and the framing you gave it — it
does not lock the set.**

- Keep the shot list **within or tighter than** the source framing when set continuity
  matters (macro, close-up and medium all held perfectly).
- If you need a wider shot, **describe the wider room explicitly** in the prompt *and* the
  Static Description, or accept that it will be invented.

*Measured* (cartoon character, 5-shot prompt, 12 s, 720p, `--audio`): 1280×720, 24 fps,
12.05 s, 4.4 MB, generation **190 s**, 4 cuts → exactly the 5 shots requested, identity held
across all five.

## Keyframe interpolation — `--first-frame` + `--last-frame`

The most directable mode: you author both ends as images and the model only solves the motion.
Use it when you need a specific beginning **and** ending — a turn, a gesture, a reveal, or a
clip that must end on an exact frame so the next can start there.

**Generate the end frame FROM the first**, or the model spends the clip reconciling two
different worlds:

```bash
strata generate image "<the START pose>" -o a.png
strata generate image "The SAME character as image 0 … <the END pose>" --reference a.png -o b.png
```

*Measured:* `--reference` held the character exactly — same eyes, freckles, curls, wardrobe
and mug — while changing the pose from three-quarters-to-camera to profile.

**The prompt is ONE continuous move, not a shot list.** This is the one place the shot-list
structure does not apply:

1. `Style & Mood:` and the CAPS theme as usual.
2. Identity lock referencing **both** frames: *"the character in the FIRST FRAME and the LAST
   FRAME … identical throughout."*
3. **State it plainly: "ONE single continuous shot, no cuts."** *Measured:* honoured exactly.
4. Describe the motion **physically, in order** — what starts, what moves, what the hands do,
   what the light does, how it settles — naming the ends explicitly: *"She begins exactly as
   in the first frame … she ends exactly as in the last frame."*
5. `Static Description:` and `Audio:` as usual.

*Measured* (5 s, 720p): **0 cuts**, both keyframes landed (frame 0 matches the start image,
the final frame matches the end image), and it was the **fastest mode — 139 s**. The
in-between was physically sensible: head turning first, then shoulders, mug lowering into one
hand, free hand rising to the frame, curls settling.

**Notes.** Keep it **short** — 5 s is plenty for one gesture; a long duration just invents
filler between two fixed poses. The real-face rule applies to **both** images. Aspect still
snaps. And this is the cleanest way to build a controlled sequence: author frames A, B, C as
images and interpolate A→B, B→C — every clip boundary is then a frame you chose, which is
stronger than chaining, where the boundary is wherever the model happened to end.

## Locking the camera — in the prompt, not the flag

`camera_fixed` is in the API spec, but the model Idomoo routes to **rejects it on every task
type** — *measured 2026-08-22:* text-to-video, first-frame and reference-image requests all
fail with *"camera_fixed is not supported for model … must be empty"*, surfaced as an HTTP
500 so it does not even look like an input error. `strata generate video --camera-fixed` now
refuses up front with this message instead of spending a request.

**State the lock in the prompt, and say what *does* move:**

> LOCKED-OFF CAMERA: the camera is completely static on a tripod — no pan, no tilt, no zoom,
> no push, no drift, no handheld movement; the frame edges never move. Only the steam moves.

Naming the one thing that moves matters as much as the lock: a prompt that only forbids
motion invites the model to invent some. Put it in the Static Description too, so it survives
a chained continuation ([Chaining](#chaining--clips-longer-than-15-s)).

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

*Measured:* the continuation started exactly where the previous clip ended — pose, hand
position, mug, wardrobe, plants and light shaft all continuous. Every clip comes back with
identical stream parameters (`h264 / 1280×720 / yuv420p / 24 fps` + `aac / 44100 / stereo`),
which is exactly what the concat demuxer needs, so the join is a lossless stream copy.
⚠ Re-check those parameters if you ever mix resolutions or models in one sequence.

**Each clip's prompt must restate the identity lock and the Static Description** — the model
does not remember the previous call — and Shot 1 of every continuation should be written to
begin on its first frame.

⚠ **Events do not chain either — only surfaces do.** A first frame carries *appearance* (wet
asphalt, reflections, a lit sign), never *activity*. Anything that is **happening** — rain or
snow falling, a crowd moving, traffic, smoke, steam, flicker, wind, blowing fabric — has no
representation in a still frame and **stops at the seam unless the continuation prompt
re-establishes it**. *Measured:* a base clip of heavy rain with six umbrella-carrying
pedestrians, chained on `--last-frame-out`; the continuation described only the character's
action. The wet street and its reflections survived — **the rain and every umbrella
vanished**. Re-stating the ambient line brought both back from the identical first frame. So
restate the **weather and the background life** in every continuation, not just the set: it is
the Static Description's missing third column, and it fails silently mid-sequence.

⚠ **One duplicate frame at every seam** — clip N's last frame *is* clip N+1's first frame
(*measured:* mean pixel difference 3.27/255, the same image differing only by compression). At
24 fps that is a 42 ms hold and *measured* it is invisible; **prefer the plain `-c copy`
concat.** Only trim when stacking many clips (10 seams ≈ 0.4 s of accumulated stutter) — it
costs a full re-encode, because clip 2's frame 1 is not a keyframe:

```bash
ffmpeg -i clip_01.mp4 -i clip_02.mp4 -filter_complex \
  "[1:v]select='gte(n\,1)',setpts=PTS-STARTPTS[v1];[0:v][0:a][v1][1:a]concat=n=2:v=1:a=1[v][a]" \
  -map "[v]" -map "[a]" -c:v libx264 -crf 18 -preset medium -pix_fmt yuv420p \
  -c:a aac -b:a 192k -y joined.mp4
```

⚠ **Audio does not chain** — each clip gets its own track, so ambience restarts at every join.
Video continuity does not buy audio continuity. For a multi-clip piece, generate without
`--audio` and lay one continuous bed over the join.

⛔ **And past ~3 clips, never `-c copy` the audio together** — each AAC segment's encoder
priming survives the copy, so the audio grows ~20–30 ms per join while the video does not
(*measured:* 0.43 s of lag across 15 segments). Build the bed **once** as WAV and encode it a
single time: [video-editing.md](video-editing.md#join--concat).

**Chaining vs keyframes:**

| | Boundary frame is… | Use when |
|---|---|---|
| **Chaining** | whatever the model happened to end on | you want to keep going and don't care exactly where each clip lands |
| **Keyframes** | a frame **you authored** | the sequence must hit specific beats |

They compose: author the key moments as frames, chain to cover the ground between them.

## ⛔ A clip shorter than its scene is NEVER stretched — cover the gap with more shots

The scene is 17 s, the clip came back 15 s. **Do not retime the clip to fit.** Slowing
footage to fill time is the single most visible amateur tell in a finished piece: motion
goes syrupy, the model's already-slow pacing becomes glacial, and a talking or sound-designed
clip loses its audio sync. A retime is for a *deliberate* slow-motion beat the storyboard
asked for, never for arithmetic.

**Cover the gap with new footage.** Two ways, both keeping the piece alive:

1. **A companion clip of the same subject — more shots, not more seconds.** Pull a reference
   frame from the clip you have (`ffmpeg -ss <t> -i clip.mp4 -frames:v 1 ref.png` →
   `strata upload`), then generate a second clip with **2–3 new shots of the same people and
   place** — a close-up, a wide, an insert — using that frame as `--ref-image` (or as
   `--first-frame` if you want it to begin exactly there). Now the long scene is *covered*:
   cut between the original and the companion, and the 17 s reads as an edit, not a hold.
   This is the better answer almost always — a scene that is too long for one shot was
   asking for coverage anyway.
2. **Extend the take** — chain off `--last-frame-out` (above) when the scene genuinely needs
   the *same* shot to continue: a walk that must reach the door, a pour that must finish.

And the two tools that make this cheap: `strata captions` / `strata beats` tell you where
the edit points are, and the editorial layer ([editing-director.md](editing-director.md))
says *which* shots a scene needs — a scene's job, not its length, decides the coverage.

The same rule in the other direction: a clip **longer** than its scene is trimmed
([video-editing.md](video-editing.md)), never sped up.

---

## Two different things are called "fast" — don't confuse them

| | What it is |
|---|---|
| **the fast model** (`generate video`, the **default**) | the same command and all its modes, on the fast Seedance model. ~1.5× quicker, and it rejects anything above 720p outright (`400 InvalidParameter`) — irrelevant, because output is clamped to 720p always. *Measured:* it delivered 4 of 5 requested shots, so a piece that needs every shot passes `--best` |
| **`generate fastvideo <image>`** | a **different, older endpoint**. One image + a motion line → a clip |

**"Fast mode" means `generate fastvideo`**, and it is used **only when the user or a workflow
explicitly asks for it**. It is not a quality tier of `generate video` and not something to
reach for on your own initiative.

What `fastvideo` cannot do: no text-to-video (an image is required), no keyframes, no
references of any kind, no `--audio`, no `--last-frame-out`, no `--realistic-human`, and no
shot list — none of the prompt anatomy on this page applies to it. It **does** take a local
file (auto-encoded), which `generate video` does not.

Because it is a single continuous move with no cuts, a `fastvideo` clip is safe to matte into
a `.jet` overlay — but it gives you no control over the matte-friendliness of the shot, so the
source image has to carry that instead.

## Errors

A wrong `--model` fails as `500` wrapping `Seedance API 404: InvalidEndpointOrModel.NotFound`.
A wrong *parameter* also surfaces as `500` wrapping a `400`. **Always read the nested detail —
the outer status tells you nothing.** Validation errors from the CLI's own checks (frames +
references, `--realistic-human` misuse, a local path where a URL belongs) fail instantly and
cost nothing.

## Checklist

- [ ] Frame-driven **or** reference-driven — never both
- [ ] `Style & Mood:` opens it; one theme in CAPS
- [ ] Identity written physically and repeated **three** times (block, close-up, Static)
- [ ] Location has a foreground, a subject and a background that falls away
- [ ] Shots numbered with `Cut.` — no timecodes — each changing size **and** camera
- [ ] Body parts anchored in CAPS
- [ ] …unless it is a `.jet` overlay or a keyframe clip → **ONE continuous shot, no cuts**
- [ ] `Static Description:` restates set + identity; negatives stated
- [ ] `Audio:` specific, with `--audio` (omit both for a `.jet` overlay)
- [ ] Physical events, not adjectives
- [ ] ≤5 shots per 12 s (≤4 with references)
- [ ] Input has a real face, a logo or a real product → expect `--realistic-human`
- [ ] Source image already in the target aspect (`adaptive` snaps, it does not preserve)
- [ ] Shot list stays within the source framing, or the wider set is described explicitly
- [ ] `--last-frame-out` if this clip will be chained (24 h expiry)
- [ ] Run it in the background — 3–9 minutes
