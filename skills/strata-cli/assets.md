# Asset generation — image, video, narration, music

The CLI generates media via the Idomoo AI API (Lucas). Needs auth (`strata auth login`).
Each command saves the file locally (default `./strata_assets/`, or `-o <file>` /
`--out-dir <dir>`) and prints the local path **and** a hosted URL; add `--json` for
machine-readable output. Image/video are **async** (polled to completion); narration is sync.

**The chain:** image → `generate video --first-frame` (or `--ref-image`) → narration + music for
the audio bed → point the scene's `src`/`audio` at the saved files.

**Local files vs URLs.** `generate image --reference` and `generate fastvideo` accept either
a hosted URL or a **local file path** (the CLI base64-encodes it into a data-URI; no upload
step). **`generate video` is the exception: its `--first-frame` / `--last-frame` / `--ref-*`
inputs are URLs only** — every `generate` command prints one, so chain off that; a genuinely
local input needs `strata upload` first (the CLI says so rather than publishing it silently).

---

## Contents

- [Source resolution vs canvas — read before choosing the comp size (MEASURED)](shoot/what-to-generate.md#source-resolution-vs-canvas--read-before-choosing-the-comp-size-measured)
- [Drawn or generated? — a physical thing is an image, a shape is for simple geometry](shoot/what-to-generate.md#drawn-or-generated--a-physical-thing-is-an-image-a-shape-is-for-simple-geometry)
- [`strata generate image "<prompt>" [flags]`](shoot/images.md#strata-generate-image-prompt-flags)
  - [Reference images — art style, characters, composition (the important part)](shoot/images.md#reference-images--art-style-characters-composition-the-important-part)
- [`strata edit image <img|url> "<what changes>"` — an image that is 90 % right is EDITED, not re-rolled](shoot/images.md#strata-edit-image-imgurl-what-changes--an-image-that-is-90--right-is-edited-not-re-rolled)
- [`strata generate video "<prompt>" [flags]`](shoot/clips.md#strata-generate-video-prompt-flags)
- [`strata generate fastvideo <image> [flags]`  — the OLD image-to-video path](shoot/clips.md#strata-generate-fastvideo-image-flags---the-old-image-to-video-path)
- [`strata generate narration "<text>" --voice <voice_id>`](shoot/sound.md#strata-generate-narration-text---voice-voice_id)
- [`strata generate music "<prompt>" [--duration <sec>]`](shoot/sound.md#strata-generate-music-prompt---duration-sec)
- [`strata upload <file>` — ONLY for generation inputs with no URL](shoot/upload.md#strata-upload-file--only-for-generation-inputs-with-no-url)
  - [🔴 CRITICAL — the whole rule in one line](shoot/upload.md#-critical--the-whole-rule-in-one-line)
  - [🛑 The two-condition test](shoot/upload.md#-the-two-condition-test)
  - [Most generation inputs already have a URL](shoot/upload.md#most-generation-inputs-already-have-a-url)
  - [🛑 TEMPORARY assets only — never persistent ones](shoot/upload.md#-temporary-assets-only--never-persistent-ones)
  - [When a URL is actually required](shoot/upload.md#when-a-url-is-actually-required)
  - [⚠ Public and permanent — which is why it is for temp use only](shoot/upload.md#-public-and-permanent--which-is-why-it-is-for-temp-use-only)
  - [The extension must match the bytes](shoot/upload.md#the-extension-must-match-the-bytes)
- [Generate in WAVES, not in sequence — parallel is the default](shoot/waves.md#generate-in-waves-not-in-sequence--parallel-is-the-default)
- [Every image becomes a video — no still photos](shoot/waves.md#every-image-becomes-a-video--no-still-photos)
- [Alpha overlays — `.jet`, and how to get anything into it](shoot/alpha.md#alpha-overlays--jet-and-how-to-get-anything-into-it)
  - [Same-source occlusion — text behind a subject with NO alpha at all](shoot/alpha.md#same-source-occlusion--text-behind-a-subject-with-no-alpha-at-all)
  - [Getting to `.jet` — pick the row that matches the source](shoot/alpha.md#getting-to-jet--pick-the-row-that-matches-the-source)
  - [`matte` is slow — halve the width first (MEASURED)](shoot/alpha.md#matte-is-slow--halve-the-width-first-measured)
  - [Quality — `.jet` is lossy; the default is Idomoo's own reference setting](shoot/alpha.md#quality--jet-is-lossy-the-default-is-idomoos-own-reference-setting)
  - [⚠️ Key the VIDEO, not a still — the motion belongs in the clip](shoot/alpha.md#️-key-the-video-not-a-still--the-motion-belongs-in-the-clip)
  - [Text BEHIND the subject — the SAME clip, used twice](shoot/alpha.md#text-behind-the-subject--the-same-clip-used-twice)

The standing constraint applies to every command here: upload ONLY assets we created that have no URL, and only as a generation input. Nothing private, ever.
