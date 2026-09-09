# Parameters

Every flag, what it does to the result, and the defaults worth overriding.

Part of the video-generation reference — the index, and every other part, is in [video-generation.md](../video-generation.md).

## Parameters

| Flag | Notes |
|---|---|
| `--duration` | 4–15 s, default 5 |
| `--ratio` | `16:9` `9:16` `1:1` `4:3` `3:4` `21:9` `adaptive`. `adaptive` snaps to the nearest standard ratio and crops (*What it actually does*, above) — it does **not** preserve an unusual source aspect; with references there is nothing to infer from, so **set it explicitly** |
| `--seed` | reproducibility; keep it fixed across a series |
| `--camera-fixed` | ⛔ **rejected by the current model on every task type** (*measured:* t2v, i2v and r2v all answer `camera_fixed … must be empty`; the CLI now refuses it before spending the request). **Lock the camera in the prompt instead** — see *Locking the camera* below |
| `--audio` | native synced audio. *Measured:* real AAC 44.1 kHz stereo. ⚠ **Anything you do to this clip afterwards must keep that track** — see below |
| `--last-frame-out <file>` | saves the last frame for chaining. **Do it now** — the URL is signed and expires in 24 h |
| `--best` | the **standard** model instead of the default fast one: slower, but it delivers every shot asked for. Pass it when the storyboard needs all its shots (`--fast` still parses and is now a no-op — fast is the default) |
| `--resolution` | **clamped to 720p — always, never more** (*What it actually does*, above). That cap is why fast is the default: full resolution is the only thing the standard model buys above it |

Generation takes **3–9 minutes**. Run it in the background and report the URL; when a job has several clips, launch every independent one **at once** and build the scene while they render ([assets.md](../assets.md), *Generate in waves*).

### Which model runs — fast is the default

| | Model id | When |
|---|---|---|
| **default** | `dreamina-seedance-2-0-fast-260128` | every call. ~1.5× quicker; its refusal of anything above 720p costs nothing, because output is clamped to **720p always** |
| `--best` | `dreamina-seedance-2-0-260128` | when the storyboard needs **every shot to land**: the fast model's one measured cost is shot count (a 5-shot ask returned 4) |
| `--model <id>` | any id the account has activated | an explicit override, only when asked for. An unactivated id fails with *"account has not activated the model"* — say so rather than retrying |

Every other measured number on this page was taken on the standard model; shot delivery is the
only figure the fast model changes.

### 🏷 Generated footage arrives wearing brand marks, and a negative prompt does not stop it

Asking for a mark's absence keeps the object in the model's attention, so apparel, footwear, cars
and laptops come back carrying logos and stripes regardless. **Check every clip at full
resolution** — a 320 px contact-sheet tile hides a logo completely, and that is how one reaches a
cut — and **re-frame rather than lengthen the negative list**: put the mark out of shot (the side
panel, the chest, the tongue of the shoe) or move the camera. A competitor's mark is worse than no
mark at all, whoever the piece is for.

### 🔊 A clip generated with `--audio` must keep its audio downstream

Sound design and lip-synced dialogue are generated **into the clip** — they are not a
separate track you can re-attach. If a trim, reframe, retime or concat drops the audio, the
fix is **re-generating the whole clip (3–9 minutes)**, not re-running the edit — so check
every single time.

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
