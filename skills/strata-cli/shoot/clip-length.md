# Length — chaining, and the slot it must fill

Getting past the per-clip ceiling, and the rule that a clip is never stretched to fit its slot.

Part of the video-generation reference — the index, and every other part, is in [video-generation.md](../video-generation.md).

## Chaining — clips longer than 15 s

`--duration` caps at 15. To go longer: generate, save the last frame, upload it, make it the
next clip's first frame.

```bash
strata generate video "<shot 1>" --duration 12 --last-frame-out lf.png -o clip1.mp4
strata upload lf.png --allow-public-upload # only after informed consent for this non-sensitive frame
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
restate the **weather and the background life** in every continuation, not just the set — the
Static Description's missing third column, and it fails silently mid-sequence.

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

⚠ **Audio does not chain** — each clip gets its own track, so ambience restarts at every join;
video continuity does not buy audio continuity. For a multi-clip piece, generate without
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

## ⛔ A clip must be ≥ its scene slot — a short one freezes, and is NEVER stretched

**First, the rule that prevents it: ask for a clip at least as long as the slot it fills, plus
about a second of margin, and trim the excess.** Footage is cheap to trim and impossible to
lengthen honestly. A `video` layer whose media runs out holds its last frame for the remainder
(`loop` jumps back to frame one instead, which reads worse); the scene is structurally fine, so
nothing in `validate` catches it. When a slot is 6 s, ask the model for 8.

If it happens anyway: the scene is 17 s, the clip came back 15 s. **Do not retime the clip to
fit.** Slowing
footage to fill time is the most visible amateur tell in a finished piece: motion
goes syrupy, the model's already-slow pacing becomes glacial, and a talking or sound-designed
clip loses its audio sync. A retime is for a *deliberate* slow-motion beat the storyboard
asked for, never for arithmetic.

**Cover the gap with new footage.** Two ways:

1. **A companion clip of the same subject — more shots, not more seconds.** Pull a reference
   frame from the clip you have (`ffmpeg -ss <t> -i clip.mp4 -frames:v 1 ref.png` →
   `strata upload`), then generate a second clip with **2–3 new shots of the same people and
   place** — a close-up, a wide, an insert — using that frame as `--ref-image` (or as
   `--first-frame` if you want it to begin exactly there). Now the long scene is *covered*:
   cut between the original and the companion, and the 17 s reads as an edit, not a hold.
   Almost always the better answer: a scene too long for one shot was asking for coverage.
2. **Extend the take** — chain off `--last-frame-out` (above) when the scene genuinely needs
   the *same* shot to continue: a walk that must reach the door, a pour that must finish.

Two tools make this cheap: `strata captions` / `strata beats` tell you where
the edit points are, and the editorial layer ([editing-director.md](../direct/editing-director.md))
says *which* shots a scene needs — a scene's job, not its length, decides the coverage.

The same rule in the other direction: a clip **longer** than its scene is trimmed
([video-editing.md](video-editing.md)), never sped up.

---
