# Choosing a generation mode

Which mode fits the shot in hand, and the things the fast path cannot do at all.

Part of the reference/dialogue/shot-control guide — the index, and every other part, is in [video-generation-advanced.md](../video-generation-advanced.md).

## Choosing a mode

| You have… | Use |
|---|---|
| only a description | text-to-video |
| the exact opening frame | `--first-frame` |
| both ends authored as images | `--first-frame` + `--last-frame` |
| a character/product/world to keep consistent | `--ref-image` |
| a camera plan or a clip to imitate | `--ref-video` (+ `strata sketch`) |
| a voice or a line to speak | `--ref-audio` + `--audio` |
| a person delivering a script to camera | `generate avatar` |
| a clip needing a global change | editing — **last resort** |
| a clip needing more length | **chain**, not extension |
| speed matters more than control | `generate fastvideo` — fast mode only |

## What `generate fastvideo` cannot do

`strata generate fastvideo <image>` is the OLD image-to-video path, used **only when fast mode
is explicitly asked for**. Nothing on this page applies to it: no references, no
`[Image N]` / `<Video_1>` citations, no keyframes, no `--audio`, no `--last-frame-out`, no
`--realistic-human`, no shot list — just one image plus a short motion line. It does accept a
**local file** (auto-encoded), unlike `generate video`, and being a single continuous move
with no cuts it is safe for a `.jet` overlay clip ([video-generation.md](../video-generation.md)).
