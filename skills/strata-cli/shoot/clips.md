# Generating video clips

The clip commands and the older image-to-video path, with the reference pages for prompting them alongside.

Part of the asset-generation reference — the index, and every other part, is in [assets.md](../assets.md).

## `strata generate video "<prompt>" [flags]`
An MP4 clip (async, **3–9 min**). **One command, five modes**, chosen by which inputs you
give it: prompt only = text-to-video · `--first-frame` = image-to-video · `+ --last-frame` =
keyframe interpolation · `--ref-*` = reference-driven · a source clip in `--ref-video` =
editing/extension.

🚫 **`--first-frame`/`--last-frame` and `--ref-image`/`--ref-video`/`--ref-audio` are MUTUALLY
EXCLUSIVE** — the CLI rejects the combination before spending anything. Everything measured
about the model — 24 fps output against a 25 fps scene default, the 720p clamp, the privacy
filter, brand marks, never stretched — is one table:
[video-generation.md](../video-generation.md#what-generate-video-actually-does--measured).

| flag | meaning |
|---|---|
| `--first-frame <url>` `--last-frame <url>` | exact opening / closing frame |
| `--ref-image <url>` (repeatable) | character, product, world or style to keep consistent |
| `--ref-video <url>` | copy a camera plan or cut rhythm (see `strata sketch`) |
| `--ref-audio <url>` | a voice / audio bed to sync to |
| `--duration <4..15>` | clip length (default 5); longer needs chaining |
| `--ratio <16:9\|9:16\|1:1\|4:3\|3:4\|21:9\|adaptive>` | output aspect |
| `--seed <N>` | reproducibility. (`--camera-fixed` is rejected by the model on every task type — lock the camera in the prompt: [video-generation.md](clip-frames.md#locking-the-camera--in-the-prompt-not-the-flag)) |
| `--audio` | native synced audio (real AAC 44.1 kHz stereo) |
| `--last-frame-out <file>` | save the last frame for chaining — its URL expires in 24 h |
| `--realistic-human` | required for real faces, logos and real products; **applied automatically** on a content rejection |
| `--best` · `--model <id>` | the **standard** model instead of the default fast one (slower, but delivers every shot) · an explicit model id. Default is `dreamina-seedance-2-0-fast-260128`; `--best` is `dreamina-seedance-2-0-260128` — [video-generation.md](../video-generation.md) |
| `--resolution` | **clamped to 720p** — 720p is always the max |

Reference the result as a `video` layer — and **ask for a clip at least as long as the slot it fills, plus about a second, then trim.** A layer that outlives its media freezes on its last frame (`playback_mode: "hold"`; `loop` visibly restarts, which is worse) and `validate` cannot see it — the fix belongs here, at generation time ([video-generation.md](../video-generation.md)).

```bash
strata generate image "hero shot" -o hero.png          # prints url:
strata generate video "slow cinematic push-in, dust in the light" \
  --first-frame <that url> --duration 8 --audio -o hero.mp4
```

**Prompting is the whole game here** — a one-line prompt wastes a 3–9 minute render. The
shot-list structure, the identity lock, the `.jet` no-cuts rule, keyframes and chaining are in
[video-generation.md](../video-generation.md); references, dialogue, animatics and editing in
[video-generation-advanced.md](../video-generation-advanced.md).

## `strata generate fastvideo <image> [flags]`  — the OLD image-to-video path
Quick and cheap (~1–3 min), and **only for when fast mode is explicitly asked for**. A
different, older endpoint — **not** a quality tier of `generate video`, which already runs the
fast Seedance model by default.

Takes a hosted URL **or a local file path** (auto-base64), plus `--prompt "<motion>"`,
`--duration`, `--ratio`. That is the whole surface: **an image is required** (no
text-to-video), and there are no keyframes, no references, no `--audio`, no
`--last-frame-out`, no `--realistic-human` and no shot list. None of the prompt craft in
[video-generation.md](../video-generation.md) applies — it produces one continuous move, which
does at least make it safe to matte into a `.jet` overlay.

```bash
strata generate fastvideo ./hero.png --prompt "slow cinematic push-in" --duration 5
```
