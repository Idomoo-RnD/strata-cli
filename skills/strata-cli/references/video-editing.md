# Editing existing video — cut, join, crop, retime with ffmpeg

Sometimes the user doesn't want a designed piece: they hand over an MP4 and say *"trim the
first 3 seconds"*, *"make this 9:16"*, *"stitch these together"*, *"drop the audio"*. That
is a **mechanical edit**, and the tool is **ffmpeg** — not a scene.

This file is the **mechanical** half. The **editorial** half — which cut, why, and in what order,
when the user hands over footage and says *"make this better"* or *"cut it down for social"* —
is [editing-director.md](editing-director.md); read it first, then come back here for the commands.

**The split that decides everything:**
- **Mechanical** (trim/join/crop/speed/audio/format) → **ffmpeg**, output an MP4, done.
- **Design** (titles, graphics, transitions, kinetic type, logo, data) → **a strata scene**.
- **Both** → edit with ffmpeg *first*, then use the cleaned clip as a `video` layer.

❗ **I don't burn graphics in with ffmpeg** when the piece is a designed video. `drawtext`
has no easing, no per-character animation, no masks, no motion blur — it looks like a
watermark. Text belongs in the scene. (Exception: a quick throwaway label the user
explicitly asks to burn into a raw clip.)

⚠️ **ffmpeg must be on PATH** — same dependency as `jet`/`matte`/`track`/`beats`. If it's
missing, say so instead of failing cryptically.

---

## Contents

- [The three rules that prevent most damage](#the-three-rules-that-prevent-most-damage)
- [Trim & cut](#trim--cut)
- [Join / concat](#join--concat)
- [Reframe — 16:9 → 9:16 (and back)](#reframe--169--916-and-back)
- [Speed & retime](#speed--retime)
- [Audio](#audio)
- [Grade & match — the look is built here, not in the scene](#grade--match--the-look-is-built-here-not-in-the-scene)
- [Fades, stills, loops](#fades-stills-loops)
- [Colour — `strata grade`](#colour--strata-grade)
- [Probe first, verify after — every time](#probe-first-verify-after--every-time)
- [Output rules for anything going into a scene or Idomoo](#output-rules-for-anything-going-into-a-scene-or-idomoo)
- [Recipes for common asks](#recipes-for-common-asks)
- [Rules](#rules)

## The three rules that prevent most damage

**1. Copy when you can, re-encode when you must.** `-c copy` is instant and lossless but
can only cut on **keyframes**, so the cut may land up to a GOP early (~1–2s). Frame-exact
cuts need a re-encode.
```bash
ffmpeg -ss 3 -i in.mp4 -c copy out.mp4                     # fast, keyframe-aligned
ffmpeg -ss 3 -i in.mp4 -c:v libx264 -crf 18 -c:a aac out.mp4   # frame-exact
```
**2. One pass, not five.** Every re-encode loses quality. Chain filters into a single
command rather than trimming → cropping → scaling as three files. If an intermediate is
unavoidable, use `-crf 16` so generation loss stays invisible.

**3. 🔊 KEEP THE AUDIO. The first `-map` you write silently drops it.**
This is the most repeated mistake in this whole file, and it costs the most: an AI clip's
audio — lip-synced dialogue, sound design, room tone — **cannot be re-cut back in**. Losing
it means re-generating the clip (3–9 minutes), not re-running the trim.

*Measured* on a Seedance clip with an AAC track, running the exact commands in this file:

| pattern | audio |
|---|---|
| `-c copy` · `-c:v libx264` (with or without `-c:a`) | ✅ kept |
| `-vf …` (crop, scale, pad, fade, fps) | ✅ kept |
| **`-filter_complex …` with NO `-map`** | ✅ kept |
| **anything with `-map`** | ❌ **GONE** |
| `-an` | ❌ gone (that is its job) |

ffmpeg auto-selects the best audio stream **until you write a single `-map`** — at that point
automatic selection switches off for every stream type, and video-only mapping throws the
audio away without a word. So **whenever a command contains `-map`, it needs an audio map too**:

```bash
-map "[v]" -map 0:a -c:a copy          # filtered video, original audio passed through
-map "[v]" -map "[aout]"               # both filtered (see the palindrome recipe)
```

**Verify, do not assume** — exit code 0 tells you nothing:
```bash
ffprobe -v error -show_entries stream=codec_type,duration -of csv=p=0 out.mp4
```
Two lines (`video,…` and `audio,…`) with **matching durations**. One line means the audio is
gone; mismatched durations mean it will drift. *Measured:* a correct 3 s trim reports
`video,3.000000  audio,3.000000`.

## Trim & cut
```bash
# from 00:00:03 for 5 seconds  (-ss BEFORE -i = fast seek; -t = duration)
ffmpeg -ss 3 -i in.mp4 -t 5 -c:v libx264 -crf 18 -c:a aac out.mp4
# absolute range 3s → 8s (-to is an END time, not a duration)
ffmpeg -ss 3 -i in.mp4 -to 8 -c:v libx264 -crf 18 -c:a aac out.mp4
# drop the last 2s (needs the duration first — see Probe below)
ffmpeg -i in.mp4 -t $(echo "$DUR - 2" | bc) -c:v libx264 -crf 18 out.mp4
```
`-ss` **before** `-i` seeks fast (keyframe-accurate); **after** `-i` decodes and is
frame-accurate but slow. With a re-encode, before-`-i` is accurate enough and much faster.

## Join / concat
**Same codec, size and fps** → the concat demuxer, no re-encode:
```bash
printf "file 'a.mp4'\nfile 'b.mp4'\n" > list.txt
ffmpeg -f concat -safe 0 -i list.txt -c copy out.mp4
```

> ⛔ **Never stitch more than ~3 AAC segments this way — the audio drifts against the video.**
> Every AAC segment carries **encoder priming** (~20–30 ms of padding) that a stream copy keeps
> as real samples, so the audio track grows by that much **per join** while the video does not.
> It is silent, it is progressive, and it only becomes audible late in the piece.
> *Measured:* 15 concatenated segments gave **30.93 s of audio against 30.50 s of video —
> 0.43 s of lag by the end**. The two-clip example below shows the same defect in miniature.
>
> **For any multi-segment piece, build the audio ONCE, not per segment.** Either assemble the
> full track from the sources as WAV and encode it a single time —
>
> ```bash
> # decode each segment's audio to raw PCM, join, encode once, mux against the video
> ffmpeg -f concat -safe 0 -i list.txt -vn -c:a pcm_s16le -ar 44100 -ac 2 full.wav
> ffmpeg -f concat -safe 0 -i list.txt -an -c:v copy full_v.mp4
> ffmpeg -i full_v.mp4 -i full.wav -c:v copy -c:a aac -b:a 192k -shortest out.mp4
> ```
>
> — or use the **concat filter** (below), which decodes and re-encodes in one pass and so has
> no per-segment padding to accumulate. Check the result every time:
> `ffprobe -v error -show_entries stream=codec_type,duration -of csv=p=0 out.mp4` — **the two
> durations must match**. A WAV target is exact: 30.5 s at 48 kHz is 1,464,000 samples, and you
> can assert it.

**Different codecs/sizes/fps** → normalise in one filter graph (this is the usual case for
clips from different sources):
```bash
# ⚠ a=0 + -map "[v]" DROPS THE AUDIO OF BOTH CLIPS. Only use this for silent sources.
ffmpeg -i a.mp4 -i b.mp4 -filter_complex \
 "[0:v]scale=1920:1080:force_original_aspect_ratio=decrease,pad=1920:1080:-1:-1,fps=25,setsar=1[v0]; \
  [1:v]scale=1920:1080:force_original_aspect_ratio=decrease,pad=1920:1080:-1:-1,fps=25,setsar=1[v1]; \
  [v0][v1]concat=n=2:v=1:a=0[v]" -map "[v]" -c:v libx264 -crf 18 -pix_fmt yuv420p out.mp4
```
**If either clip has audio — which any AI clip generated with `--audio` does — normalise the
audio too and concat with `a=1`.** Verified on two Seedance clips: 4.04s + 5.04s -> 9.12s
video / 9.13s audio. ⚠ Read those numbers carefully: the inputs sum to **9.08 s**, so even
here the output is long and the **audio is 10 ms longer than the video**. At two segments that
is inaudible; it is the same per-join padding that reaches 0.43 s by fifteen (box above).
Always compare the two durations rather than trusting the sum.
```bash
ffmpeg -i a.mp4 -i b.mp4 -filter_complex \
 "[0:v]scale=1920:1080:force_original_aspect_ratio=decrease,pad=1920:1080:-1:-1,fps=25,setsar=1[v0]; \
  [1:v]scale=1920:1080:force_original_aspect_ratio=decrease,pad=1920:1080:-1:-1,fps=25,setsar=1[v1]; \
  [0:a]aformat=sample_rates=44100:channel_layouts=stereo[a0]; \
  [1:a]aformat=sample_rates=44100:channel_layouts=stereo[a1]; \
  [v0][a0][v1][a1]concat=n=2:v=1:a=1[v][a]" -map "[v]" -map "[a]" \
 -c:v libx264 -crf 18 -pix_fmt yuv420p -c:a aac -b:a 192k out.mp4
```
Mismatched `fps`/`setsar` is the #1 cause of a concat that plays at the wrong speed or
stretches.

## Reframe — 16:9 → 9:16 (and back)
```bash
# centre crop to vertical (loses the sides)
ffmpeg -i in.mp4 -vf "crop=ih*9/16:ih,scale=1080:1920" -c:v libx264 -crf 18 out.mp4
# fit whole frame + blurred fill (keeps everything, reads intentional)
ffmpeg -i in.mp4 -filter_complex \
 "[0:v]scale=1080:1920:force_original_aspect_ratio=increase,crop=1080:1920,gblur=sigma=30[bg]; \
  [0:v]scale=1080:-1[fg];[bg][fg]overlay=(W-w)/2:(H-h)/2" -c:v libx264 -crf 18 out.mp4
# ^ keeps audio ONLY because there is no -map. Add one (e.g. to stack two inputs) and you
#   must add -map 0:a with it.
# letterbox on brand colour instead of blur
ffmpeg -i in.mp4 -vf "scale=1080:-1,pad=1080:1920:0:(oh-ih)/2:0x0E1220" -c:v libx264 -crf 18 out.mp4
```
**Pick by content:** a centred subject → crop. Wide action or on-screen text → blur-fill or
brand letterbox, or the important half leaves the frame. **Check a frame afterwards** — a
crop that decapitates the subject is the classic failure.

## Speed & retime

**A keyframed ramp is a built-in — do not hand-build it.** `strata retime <clip> --ramp
"0:1.0, 2.0:0.25, 3.2:1.0"` splits at the keyframes, motion-interpolates any segment below
0.5× (the difference between slow motion and a slideshow), tempo-adjusts the audio through
the ramp (or `--mute`), and asserts the output duration. Hand-built ramps hit the `-t` trap
and the `-map` audio drop every time — that is why the command exists. A ramp is an
**impact beat**: real speed into the hit, slow through it, back out.

⛔ **A retime is for a slow-motion beat the storyboard asked for — never to make a short clip
fit a long scene.** A 15 s clip in a 17 s scene is covered with **more shots of the same subject**
(a companion clip from a reference frame) or **extended** off its last frame, not stretched:
[video-generation.md](video-generation.md#a-clip-shorter-than-its-scene-is-never-stretched--cover-the-gap-with-more-shots).
Stretched footage is the most visible amateur tell in a finished piece.

```bash
ffmpeg -i in.mp4 -filter_complex "[0:v]setpts=0.5*PTS[v];[0:a]atempo=2.0[a]" -map "[v]" -map "[a]" out.mp4  # 2× faster
ffmpeg -i in.mp4 -filter:v "setpts=2.0*PTS" -an out.mp4        # 2× slower, silent
ffmpeg -i in.mp4 -vf "fps=25" -c:v libx264 -crf 18 out.mp4     # conform fps
```
`atempo` only handles 0.5–2.0 per instance — chain it for more. Slowing below ~0.5×
without interpolation stutters; there's no frame interpolation here (a RIFE-class tool
would be needed).

⚠ **Never combine `-t` with a `setpts` stretch — `-t` cuts the OUTPUT, after the stretch.**
You ask for a slow-down to fill *N* seconds, `-t` then truncates the stretched result back to
its old length and you are short. *Measured:* an end-card retime that should have produced
**105 frames returned 79**. Drop `-t` entirely and let the filter define the length — add
`fps=24` so the output has real frames at the scene's rate rather than stretched timestamps:

```bash
# 60 frames -> 105 frames at 24 fps (2.5 s to 4.375 s): factor = 105/60 = 1.75
ffmpeg -i card.mp4 -vf "setpts=1.75*PTS,fps=24" -c:v libx264 -crf 18 -pix_fmt yuv420p -y out.mp4
ffprobe -v error -count_frames -select_streams v -show_entries stream=nb_read_frames \
        -of csv=p=0 out.mp4        # assert the frame count you asked for
```

Compute the factor from **frames you want ÷ frames you have**, then verify the count — a
retime that silently lands short desyncs everything downstream of it.

## Audio
```bash
ffmpeg -i in.mp4 -vn -acodec libmp3lame -q:a 2 audio.mp3       # extract (feeds `strata beats`)
ffmpeg -i in.mp4 -an -c:v copy silent.mp4                      # strip audio
ffmpeg -i v.mp4 -i a.mp3 -c:v copy -map 0:v -map 1:a -shortest out.mp4   # replace audio
ffmpeg -i in.mp4 -af "volume=0.3" -c:v copy out.mp4            # duck under a VO
ffmpeg -i in.mp4 -af "afade=t=in:d=0.5,afade=t=out:st=9.5:d=0.5" -c:v copy out.mp4
```

## Grade & match — the look is built here, not in the scene
The engine has **no colour correction** (its only effects are blur, layer styles and corner
pin), so exposure matching and any grade happen in ffmpeg **before** the clip becomes a layer.
All verified, all keep the audio (`-c:a copy`):
```bash
# lift / contrast / saturation — the everyday match between two clips
ffmpeg -i in.mp4 -vf "eq=contrast=1.08:brightness=0.02:saturation=0.85" -c:v libx264 -crf 18 -c:a copy out.mp4
# teal–orange push: shadows toward blue, mids toward warm
ffmpeg -i in.mp4 -vf "colorbalance=rs=-0.05:bs=0.08:rm=-0.03:bm=0.05" -c:v libx264 -crf 18 -c:a copy out.mp4
# filmic curve presets: lighter | darker | increase_contrast | vintage | cross_process | negative
ffmpeg -i in.mp4 -vf "curves=preset=vintage" -c:v libx264 -crf 18 -c:a copy out.mp4
# desaturate + gamma (the "muted premium" look), then a LUT if the brand has one
ffmpeg -i in.mp4 -vf "hue=s=0.7,eq=gamma=0.95" -c:v libx264 -crf 18 -c:a copy out.mp4
ffmpeg -i in.mp4 -vf "lut3d=brand.cube" -c:v libx264 -crf 18 -c:a copy out.mp4
```
**Matching two clips for a split or grid:** probe both (`signalstats` or just a frame of each
side by side), bring the darker one up with `eq=brightness` first, then saturation, then a
single shared `curves`/LUT on both so they share a "film stock". Grade **once**, in the same
pass as the trim/reframe (rule 2). In the scene, the remaining look tools are overlay solids
with blend modes, a vignette mask, and a grain `.jet` — motion-design.md, finish pass.

## Fades, stills, loops
```bash
ffmpeg -i in.mp4 -vf "fade=t=in:d=0.4,fade=t=out:st=5.6:d=0.4" -c:v libx264 -crf 18 out.mp4
ffmpeg -ss 2 -i in.mp4 -frames:v 1 frame.png                   # one frame
ffmpeg -i in.mp4 -vf "select='eq(n\,0)+eq(n\,60)+eq(n\,120)',scale=320:-1,tile=3x1" \
       -vsync 0 -frames:v 1 strip.png                          # a contact strip to LOOK at
ffmpeg -stream_loop 3 -i in.mp4 -c copy looped.mp4             # repeat 4× total
# seamless palindrome loop (forward + reverse) — no visible jump. SILENT: -an
ffmpeg -i in.mp4 -filter_complex "[0:v]split[a][b];[b]reverse[r];[a][r]concat=n=2:v=1[v]" -map "[v]" -an loop.mp4
# same, KEEPING the audio (reverse it too, or the loop desyncs) — verified 8.08s v / 8.13s a
ffmpeg -i in.mp4 -filter_complex \
 "[0:v]split[a][b];[b]reverse[r];[a][r]concat=n=2:v=1[v]; \
  [0:a]asplit[c][d];[d]areverse[ar];[c][ar]concat=n=2:v=0:a=1[aout]" \
 -map "[v]" -map "[aout]" loop.mp4
```
An **honest loop** is either a palindrome or a clip whose first and last frames already
match — repeating an arbitrary clip shows a hard jump and reads cheap.

## Colour — `strata grade`

Clips from different `generate` calls do not match each other, and none match a brand.
Grade them instead of hiding it with grain:

```bash
strata grade clip_b.mp4 --match clip_a.mp4 -o clip_b_graded.mp4   # histogram-match to a reference
strata grade clip.mp4 --lut brand.cube                            # the brand's LUT, if it has one
strata grade clip.mp4 --look warm-film                            # restrained named looks
```

**`--match` is the coverage-rule companion:** a companion clip generated from a reference
frame rarely matches the original's colour; matching it before the edit makes the cut read
as one shoot (*measured on the two golden-hour/cool-morning street clips — the join
disappears*). A grade is a look; it does not count as an atmospheric effect
([anti-slop.md](anti-slop.md)).

## Probe first, verify after — every time
```bash
ffprobe -v error -select_streams v:0 \
  -show_entries stream=width,height,r_frame_rate,nb_frames,duration,pix_fmt \
  -of default=noprint_wrappers=1 in.mp4
```
```bash
# and ALWAYS confirm the audio survived — one line per stream, durations should match
ffprobe -v error -show_entries stream=codec_type,duration -of csv=p=0 out.mp4
```
Then **look at the result**: pull a contact strip (above) and check it. An edit that
"succeeded" with exit code 0 can still be cropped wrong, stretched, or **silent**. Re-probe the
output to confirm dimensions/fps/duration are what was asked for.

## Output rules for anything going into a scene or Idomoo
- **H.264 in MP4, `-pix_fmt yuv420p`** — the API takes `mp4/jpg/png` media only.
- **Even dimensions** — odd width/height fails H.264. Force it:
  `-vf "scale=trunc(iw/2)*2:trunc(ih/2)*2"`.
- **Match the scene's fps** (`fps=<scene fps>`) — a mismatch drifts audio sync and makes
  beat-locked motion land late.
- `-movflags +faststart` for anything played over the web.
- **MP4 has no alpha.** A cut-out that composites over other layers must become a **`.jet`**
  (`strata matte` / `strata jet`) — see [assets.md](assets.md).
- **Audio still present?** `ffprobe` the output before handing it on — see rule 3. A clip
  whose dialogue or sound design was dropped has to be **re-generated**, not re-trimmed.
- Keep `-crf` 16–20 for intermediates; the cloud render re-encodes anyway.

## Recipes for common asks
| The user says | Do |
|---|---|
| "cut the first/last N seconds" | `-ss` / `-t`, re-encode for exactness |
| "just the good bit, 12s–20s" | `-ss 12 -to 20` |
| "put these three together" | normalise + `concat` (differing sources) |
| "make it vertical for TikTok" | crop or blur-fill reframe, then check a frame |
| "speed it up / slow-mo" (flat, whole clip) | `setpts` (+`atempo` for audio) |
| "slow through the impact" (a ramp) | **`strata retime --ramp`** — never hand-build it |
| "these clips don't match" / "make it cinematic" | **`strata grade --match ref` / `--lut brand.cube` / `--look`** |
| "the clip is shorter than the scene" | **not a retime** — generate a companion clip or extend it ([video-generation.md](video-generation.md)) |
| "remove the sound" / "add this music" | `-an` / map a new audio stream |
| "grab me a thumbnail" | `-ss <t> -frames:v 1` |
| "loop it" | palindrome loop, or `-stream_loop` if the ends match |
| "add a title / logo / captions" | **not ffmpeg** — a strata scene (or `.jet` overlay) |
| "cut it to the music" | `strata beats` for onsets, then cut at those timestamps ([generative-fx.md](generative-fx.md)) |

## Rules
- **Probe before, verify after** — and actually look at a frame.
- **Never overwrite the user's source.** Write a new file (`clip_v1.mp4`, `clip_trim.mp4`);
  the original is often irreplaceable.
- **One pass** where possible; avoid stacking re-encodes.
- **State what you did** — the exact ffmpeg command, so the user can adjust or repeat it.
- **Mechanical vs design:** if the ask is really "make this look good", stop and offer a
  scene instead of piling on filters.
