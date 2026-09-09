# Generating assets rather than asking for them

Part of [commands.md](../commands.md). Offer the CLI's media tools, but choose the construction
that serves the brief. Reuse approved files; do not replace exact brand assets, data plots or
intentional flat illustrations with speculative AI imagery.

## I generate assets — I don't make the user supply everything

| Command | What it makes / when to choose it | Detail |
|---|---|---|
| `generate image` | photographed/illustrated stills; references accept local files or URLs | [images](../shoot/images.md) |
| `edit image` | one scoped change to an existing image; preserve the original and inspect drift | [images](../shoot/images.md) |
| `generate video` | subject/material/camera motion; frames and references are separate modes | [video generation](../video-generation.md) |
| `generate fastvideo` | older fast image-animation route, only when explicitly chosen | [clips](../shoot/clips.md) |
| `generate avatar` | talking presenter from still + voice; decide its trade-off | [avatar](../shoot/avatar.md) |
| `generate narration` / `voices` | TTS voice and voice discovery | [sound](../shoot/sound.md) |
| `generate music` | music or prompted SFX; prepare the mix for the destination | [music](../shoot/music.md) |
| `sketch` | local grey-box camera animatic for a later reference-driven clip | [sketch](../shoot/sketch.md) |
| `path` | SVG draw-on alpha overlay; scene mask trim is preferable for exact solid-color linework | [masks](../recipes/masks.md) |
| `chart` | animated bars/donut/line from numeric input; geometry is baked when emitted | [personalization](../engine/personalization.md) |
| `retime` | deliberate speed ramp, not a way to disguise missing source coverage | [video editing](../shoot/video-editing.md) |
| `grade` | match, LUT or look on existing footage | command help; [video editing](../shoot/video-editing.md) |
| `matte` / `jet` | remove/key backgrounds and encode alpha footage | [alpha](../shoot/alpha.md) |
| `upload` | publish an approved non-sensitive URL-only input when no usable URL exists | [upload policy](../shoot/upload.md) |

Run command help for flags. [all-commands.md](all-commands.md) lists runtime, input/output and
side effects. Generation spends budget and sends inputs to an AI service; establish permission
and prototype scope before a wave. Existing generated URLs should be reused, not re-uploaded.
Scene assets stay local for embedding. **Customer photos and personalized data are not uploaded
to the public permanent store.** Direct upload and caption re-hosting require informed consent
for non-sensitive material and `--allow-public-upload`; that flag does not waive privacy policy.

## Choose motion, not a universal media rule

- **Photographed motion:** a person walking, talking or a product turning needs motion in the
  footage. Key/matte that video if transparent compositing is needed, then inspect alignment.
- **Graphic motion:** illustration, a cut-out collage, an icon or product still can be animated as
  scene layers. A deliberate locked photograph is valid when the direction earns it.
- **Reference-driven footage:** use an approved first frame when framing is fixed; references
  when subject identity is fixed but angles may change. Preserve identity and check the result.
- **Personalized media:** a replaceable image slot can keep its content-agnostic layer animation.
  Changing chart geometry requires per-row emission or an exact replacement image, not a label swap.

For generated footage, inspect clip length, audio and actual movement before assigning the slot.
If the approved shot requires travel and the result hovers, re-prompt or change the plan explicitly;
do not silently substitute sticker-like translation. Conversely, do not buy a clip for an asset
whose correct treatment is still or vector-based. The [design contract](../make/design-contract.md)
settles intent; the engine references settle syntax.
