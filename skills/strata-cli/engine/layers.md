# Layers — common keys, solid, media, audio

What every layer shares, and the three layer types with no geometry of their own to explain.

Part of the scene-format reference — the index, and every other part, is in [format.md](../format.md).

## Layers (common)

> ### ⚠️ Layer names must be unique across the WHOLE scene
> The exporter keys every layer by name, and the name is also the **personalization key**
> the API replaces content by. Two rules follow, and the second is the one that bites:
>
> 1. **Uniqueness is global, not per-comp.** `card_a` and `card_b` each containing a layer
>    called `label` **is a collision** — the check spans every composition.
> 2. **Duplicates don't crash — they get RENAMED.** The compiler auto-uniquifies
>    (`label` → `label_2`) and prints `⚠ renamed N duplicate layer name(s)`. The render then
>    succeeds, so nothing looks wrong — but the placeholder key an integration must send is
>    now `label_2`, not `label`. A personalization backend built against the name you *meant*
>    breaks, and the reason is invisible in the finished video.
>
> **So: never ignore that ⚠ rename warning** — fix the names in the scene instead. Prefix by
> owner (`card1_label`, `card2_label`, `hero_title`, `cta_label`) rather than reusing a generic
> word in two places. To see the keys the API actually receives, render once with
> `--emit-timeline out.json` ([personalization.md](personalization.md)) and read the `key`
> fields — if a `_2` shows up there, you had a collision.

| key | meaning |
|---|---|
| `type` | `text` `image` `video` `solid` `audio` `comp` `camera` (`media` also accepted, type sniffed from extension) |
| `name` | layer name — **must be unique GLOBALLY, across every comp**, not just within one (matte references and personalization keys are both by name). See the box below: duplicates do not fail loudly, they get **renamed**, which silently changes your API keys. |
| `start` / `duration` | seconds → `first_frame` / `num_of_frames` (frame-exact keys also accepted). Default: starts at 0, runs to comp end |
| `box` | `[x, y, w, h]` → bounds. Default: full comp. (visual layers only) |
| `position` | `[x,y]` or `[x,y,z]` — where the anchor lands (comp coords); defaults to `anchor`, so it's a plain offset when no anchor is set |
| `scale` | number (uniform) or `[sx,sy]` / `[sx,sy,sz]` |
| `rotation` | degrees (Z) or `[xDeg,yDeg,zDeg]` |
| `anchor` | `[x,y]` or `[x,y,z]` — scale/rotation pivot, in comp coords (typically the layer's visual center). Baked into the matrix: `T(position)·R·S·T(−anchor)` |
| `opacity` | 0..1 |
| `blend` | blend mode: `normal add subtract multiply divide screen darken lighten difference exclusion overlay hardmix colordodge colorburn lineardodge linearburn linearlight vividlight pinlight hardlight softlight luminosity hue saturation color` |
| `fit` | `"fit"` / `"fill"` or `{x, y, scale, scale_type}` — content alignment in box (media/solid/comp) |
| `motion_blur` | **compiler sets `true`** on every visual layer (text/solid/media/comp) — set `"motion_blur": false` to opt out (rarely right). Smooths animated motion; no cost on static layers. **⚠ Not applied to `camera` layers:** the schema default there is `false` and the compiler passes the camera's value through only when set, so an animated camera needs an explicit `"motion_blur": true` or the whole move renders crisp (`validate` warns) |
| `animate` | tween channels, see below |
| `effects` | inline effect list, see below |
| `mask` | inline mask, see below |
| `matte` | track matte, see below |

Position/scale/rotation compose to the VASCO 4×4 `transform` as `T(position)·R·S·T(−anchor)`. Scaling/rotation pivot on `anchor`.

> ⚠️ **`anchor` + `position` — the #1 transform bug.** Once you set an `anchor`, `position` is the **absolute comp coordinate where that anchor lands — not an offset.** It defaults to the anchor, so an anchored layer with no `position` rests exactly in place. If you set an anchor and then animate `position` toward `[0,0]` (offset-style), you drag the pivot to the top-left corner and the whole layer flies up there.
>
> To scale/rotate around the center **and** move it (rise, slide, drift), express every position keyframe as **anchor + offset**, with the *resting* keyframe equal to the anchor:
> ```json
> // grow in place at comp center [960,540] while rising 40px into position
> { "type": "text", "text": "Hello", "font": "./f.ttf", "box": [0,440,1920,200],
>   "align": "center middle", "anchor": [960, 540],
>   "animate": {
>     "scale":    [ {"t":0,"v":0.9,"ease":"outExpo"}, {"t":0.6,"v":1} ],
>     "position": [ {"t":0,"v":[960,580],"ease":"outCubic"}, {"t":0.7,"v":[960,540]} ]
>   } }
> ```
> - ❌ `"anchor":[960,540]` + `"position":[[0,40]→[0,0]]` → snaps to top-left.
> - ✅ `"anchor":[960,540]` + `"position":[[960,580]→[960,540]]` → rises into place, centered.
> - **No pivot needed?** Omit `anchor` entirely and `position` becomes a plain offset from the layer's natural spot (e.g. `[0,40]→[0,0]` for a simple fade-rise). Only reach for an anchor when you also scale or rotate.

## Solid

```json
{ "type": "solid", "color": "#10204a", "box": [0,500,1280,140], "opacity": 0.6 }
```

## Image / Video (media)

```json
{ "type": "image", "src": "./photo.jpg", "box": [0,0,1280,720], "fit": "fill" }
{ "type": "video", "src": "./clip.mp4", "loop": true, "offset_frame": 0 }
```

`loop: true|false` → `playback_mode` loop/cut (or pass `playback_mode`: `cut loop hold`). Extensions sniffed: png/jpg/jpeg/webp/bmp/gif/tif → image; mp4/mov/avi/webm/mkv/m4v → video.

`offset_frame` (integer, default 0) is the **trim-in**: the layer's media starts that many frames into its source, counted in **comp frames** at the comp's `fps` (25 by default). It is passed through verbatim — no seconds-to-frames conversion — so write frames, not seconds (2 s at 25 fps = `offset_frame: 50`). The schema puts it on media, solid, text and composition layers, never on audio (below). It shortens what a clip can cover: **`offset_frame + slot frames ≤ clip frames`**, or the shot freezes on its last frame — the arithmetic and the 24 fps clip against a 25 fps comp are in [traps.md](../traps.md).

**Don't loop a clip to fill time.** A clip that restarts mid-scene reads as a glitchy GIF — the visible jump back to frame one looks cheap. Prefer to size the scene to the clip, or when one clip ends **cut/transition to a different shot** rather than replaying the same one. Use `playback_mode: "hold"` to freeze on the last frame instead of looping, and reserve `loop: true` for genuinely seamless ambient textures (subtle particles, gradients).

## Audio

```json
{ "type": "audio", "src": "./music.mp3", "volume": -6, "ducking": true, "start": 0, "duration": 10 }
```

`volume` in dB (0 = unity); `ducking` → `sidechain_compression` (auto-lower under voice); `start` places
the clip in time — that is how SFX are spotted (music.md, *sound design pass*).

- ⚠ **`volume` is NOT animatable** — the schema allows only `transform` under an audio layer's
  `animations`, so `animate: { volume: … }` fails validation. Fades and swells are done in
  ffmpeg before import (`afade`), see video-editing.md.
- **No trim-in.** Audio layers have no `offset_frame`; to start a track at 0:12, cut it with
  ffmpeg first.
- `duration_referrer` (boolean) exists in the schema and is undocumented by the engine; it is
  not used by this CLI.
