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
   [--last-frame-out <file>] [--realistic-human] [--best] [-o out.mp4]
```

References, dialogue, animatics and editing are in
**[video-generation-advanced.md](video-generation-advanced.md)**.

## What `generate video` actually does — measured

The contract Seedance delivers, with the workaround beside each line. The sections below hold
the detail; this table is what to plan on.

| Measured | Workaround |
|---|---|
| **Clips return at 24 fps** — every clip is `h264 / 1280×720 / yuv420p / 24 fps` (+ `aac / 44100 / stereo` with `--audio`), while the scene default is **25** (`"fps": 25`, [format.md](format.md), *Scene*) | for a footage-led piece set the comp `fps` to 24 so clip frames map 1:1; at 25 the encoder resamples (24 into 25 repeats about one frame a second), acceptable for a graphics-led piece with a clip in a slot. A `.jet` from a clip is built `--fps 24` and must match the comp ([traps.md](traps.md)) |
| **720p clamp** — output is 1280×720 always; the fast model refuses anything above (`400 InvalidParameter`), the standard model delivers no more | author full-bleed footage at 1280×720 / 720×1280; in a 1920 comp keep generated media in a slot ≤ native size ([assets.md](assets.md), *Source resolution*) |
| **Frames and references are mutually exclusive** — `--first-frame`/`--last-frame` with any `--ref-*` is blocked by the CLI; the API answers `422` | reference-driven first, then chain off its last frame when an exact frame is needed |
| **A privacy pre-filter rejects photoreal faces — AI-generated ones too** (`InputImageSensitiveContentDetected.PrivacyInformation`); logos and real products trip it as well; video inputs are exempt | `--realistic-human` — the CLI retries once with it automatically and says `realistic_human was applied` |
| **Brand marks appear despite negative prompts** — apparel, footwear, cars and laptops come back with logos and stripes | re-frame the mark out of shot; check every clip at full resolution, never on a 320 px tile |
| **A clip is never stretched to its slot** — media that runs out holds its last frame (`hold`) or restarts (`loop`); `validate` cannot see it | ask for the slot + ~1 s and trim; a short clip is covered with more shots or chained, never retimed (*A clip must be ≥ its slot*, below; the `offset_frame` arithmetic is in [traps.md](traps.md)) |
| **4–15 s, and the model paces the cut itself** — 5 shots in 12 s came back 1.38 / 2.21 / 1.96 / 3.33 / 3.22 s; the fast model delivered 4 of 5 | budget ~5 shots per 12 s (≤4 with references); `--best` when every shot must land; chain past 15 s |
| **3–9 minutes per clip** | generate in waves, in the background — every clip with no unmet dependency at once — and build the scene meanwhile ([assets.md](assets.md), *Generate in waves*) |
| `adaptive` ratio snaps to the nearest standard ratio and crops (1376×768 → 1280×720); `--camera-fixed` is rejected on every task type | set `--ratio` explicitly; lock the camera in the prompt |

---

## Contents

- [What `generate video` actually does — measured](#what-generate-video-actually-does--measured)
- [🚫 The one hard rule: frames and references are mutually exclusive](shoot/clip-rules.md#-the-one-hard-rule-frames-and-references-are-mutually-exclusive)
- [🟢 `--realistic-human` — the answer to content rejections](shoot/clip-rules.md#---realistic-human--the-answer-to-content-rejections)
- [Media inputs are URLs](shoot/clip-rules.md#media-inputs-are-urls)
- [Parameters](shoot/clip-params.md#parameters)
  - [🔊 A clip generated with `--audio` must keep its audio downstream](shoot/clip-params.md#-a-clip-generated-with---audio-must-keep-its-audio-downstream)
- [Writing the prompt](shoot/clip-prompting.md#writing-the-prompt)
  - [1. `Style & Mood:` — one line](shoot/clip-prompting.md#1-style--mood--one-line)
  - [2. One theme, in CAPS](shoot/clip-prompting.md#2-one-theme-in-caps)
  - [3. Identity lock — state it THREE times](shoot/clip-prompting.md#3-identity-lock--state-it-three-times)
  - [4. Location — one paragraph with depth](shoot/clip-prompting.md#4-location--one-paragraph-with-depth)
  - [5. Numbered shots with `Cut.` — never timecodes](shoot/clip-prompting.md#5-numbered-shots-with-cut--never-timecodes)
  - [6. `Static Description:` — the invariants](shoot/clip-prompting.md#6-static-description--the-invariants)
  - [7. `Audio:` — always present, always specific](shoot/clip-prompting.md#7-audio--always-present-always-specific)
  - [8. Physics, not adjectives](shoot/clip-prompting.md#8-physics-not-adjectives)
  - [What to expect *(measured)*](shoot/clip-prompting.md#what-to-expect-measured)
- [Worked example — the prompt that produced the result above](shoot/clip-prompting.md#worked-example--the-prompt-that-produced-the-result-above)
- [⛔ Clips destined for a `.jet` alpha overlay — NO shots, NO cuts](shoot/clips-for-alpha.md#-clips-destined-for-a-jet-alpha-overlay--no-shots-no-cuts)
  - [Prompt for a clean matte, not just a nice shot](shoot/clips-for-alpha.md#prompt-for-a-clean-matte-not-just-a-nice-shot)
- [Image-to-video — `--first-frame`](shoot/clip-frames.md#image-to-video----first-frame)
- [Keyframe interpolation — `--first-frame` + `--last-frame`](shoot/clip-frames.md#keyframe-interpolation----first-frame----last-frame)
- [Locking the camera — in the prompt, not the flag](shoot/clip-frames.md#locking-the-camera--in-the-prompt-not-the-flag)
- [Chaining — clips longer than 15 s](shoot/clip-length.md#chaining--clips-longer-than-15-s)
- [⛔ A clip must be ≥ its scene slot — a short one freezes, and is NEVER stretched](shoot/clip-length.md#-a-clip-must-be--its-scene-slot--a-short-one-freezes-and-is-never-stretched)
- [Two different things are called "fast" — don't confuse them](shoot/clip-modes.md#two-different-things-are-called-fast--dont-confuse-them)
- [Errors](shoot/clip-modes.md#errors)
- [Checklist](shoot/clip-modes.md#checklist)

Resolution is clamped to 720p and a clip costs minutes, so every page here exists to get the clip right on the first roll rather than the third.
