# Editing existing video — cut, join, crop, retime with ffmpeg

Sometimes the user doesn't want a designed piece: they hand over an MP4 and say *"trim the
first 3 seconds"*, *"make this 9:16"*, *"stitch these together"*, *"drop the audio"*. That
is a **mechanical edit**, and the tool is **ffmpeg** — not a scene.

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

## The two rules that prevent most damage

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
**Different codecs/sizes/fps** → normalise in one filter graph (this is the usual case for
clips from different sources):
```bash
ffmpeg -i a.mp4 -i b.mp4 -filter_complex \
 "[0:v]scale=1920:1080:force_original_aspect_ratio=decrease,pad=1920:1080:-1:-1,fps=25,setsar=1[v0]; \
  [1:v]scale=1920:1080:force_original_aspect_ratio=decrease,pad=1920:1080:-1:-1,fps=25,setsar=1[v1]; \
  [v0][v1]concat=n=2:v=1:a=0[v]" -map "[v]" -c:v libx264 -crf 18 -pix_fmt yuv420p out.mp4
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
# letterbox on brand colour instead of blur
ffmpeg -i in.mp4 -vf "scale=1080:-1,pad=1080:1920:0:(oh-ih)/2:0x0E1220" -c:v libx264 -crf 18 out.mp4
```
**Pick by content:** a centred subject → crop. Wide action or on-screen text → blur-fill or
brand letterbox, or the important half leaves the frame. **Check a frame afterwards** — a
crop that decapitates the subject is the classic failure.

## Speed & retime
```bash
ffmpeg -i in.mp4 -filter_complex "[0:v]setpts=0.5*PTS[v];[0:a]atempo=2.0[a]" -map "[v]" -map "[a]" out.mp4  # 2× faster
ffmpeg -i in.mp4 -filter:v "setpts=2.0*PTS" -an out.mp4        # 2× slower, silent
ffmpeg -i in.mp4 -vf "fps=25" -c:v libx264 -crf 18 out.mp4     # conform fps
```
`atempo` only handles 0.5–2.0 per instance — chain it for more. Slowing below ~0.5×
without interpolation stutters; there's no frame interpolation here (a RIFE-class tool
would be needed).

## Audio
```bash
ffmpeg -i in.mp4 -vn -acodec libmp3lame -q:a 2 audio.mp3       # extract (feeds `strata beats`)
ffmpeg -i in.mp4 -an -c:v copy silent.mp4                      # strip audio
ffmpeg -i v.mp4 -i a.mp3 -c:v copy -map 0:v -map 1:a -shortest out.mp4   # replace audio
ffmpeg -i in.mp4 -af "volume=0.3" -c:v copy out.mp4            # duck under a VO
ffmpeg -i in.mp4 -af "afade=t=in:d=0.5,afade=t=out:st=9.5:d=0.5" -c:v copy out.mp4
```

## Fades, stills, loops
```bash
ffmpeg -i in.mp4 -vf "fade=t=in:d=0.4,fade=t=out:st=5.6:d=0.4" -c:v libx264 -crf 18 out.mp4
ffmpeg -ss 2 -i in.mp4 -frames:v 1 frame.png                   # one frame
ffmpeg -i in.mp4 -vf "select='eq(n\,0)+eq(n\,60)+eq(n\,120)',scale=320:-1,tile=3x1" \
       -vsync 0 -frames:v 1 strip.png                          # a contact strip to LOOK at
ffmpeg -stream_loop 3 -i in.mp4 -c copy looped.mp4             # repeat 4× total
# seamless palindrome loop (forward + reverse) — no visible jump
ffmpeg -i in.mp4 -filter_complex "[0:v]split[a][b];[b]reverse[r];[a][r]concat=n=2:v=1[v]" -map "[v]" -an loop.mp4
```
An **honest loop** is either a palindrome or a clip whose first and last frames already
match — repeating an arbitrary clip shows a hard jump and reads cheap.

## Probe first, verify after — every time
```bash
ffprobe -v error -select_streams v:0 \
  -show_entries stream=width,height,r_frame_rate,nb_frames,duration,pix_fmt \
  -of default=noprint_wrappers=1 in.mp4
```
Then **look at the result**: pull a contact strip (above) and check it. An edit that
"succeeded" with exit code 0 can still be cropped wrong, stretched, or silent. Re-probe the
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
- Keep `-crf` 16–20 for intermediates; the cloud render re-encodes anyway.

## Recipes for common asks
| The user says | Do |
|---|---|
| "cut the first/last N seconds" | `-ss` / `-t`, re-encode for exactness |
| "just the good bit, 12s–20s" | `-ss 12 -to 20` |
| "put these three together" | normalise + `concat` (differing sources) |
| "make it vertical for TikTok" | crop or blur-fill reframe, then check a frame |
| "speed it up / slow-mo" | `setpts` (+`atempo` for audio) |
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
