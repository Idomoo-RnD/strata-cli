# Animation — the tween engine

Keyframes, per-segment easing including custom curves and springs, and channels driven straight from an audio envelope.

Part of the scene-format reference — the index, and every other part, is in [format.md](../format.md).

## Tween engine (`animate`)

Each channel is a keyframe array; the CLI bakes per-frame values at comp fps over the layer's duration.

```json
"animate": { "<channel>": [ { "t": 0.5, "v": <value>, "ease": "outCubic" }, ... ] }
```

- `t` = seconds (or `f` = frames), **relative to the layer's start**.
- `ease` shapes the segment *leaving* that keyframe; if omitted, the next keyframe's ease applies; else linear. `hold` freezes until the next keyframe.
- Easings: `linear`, `hold`, `in|out|inOut` + `Quad Cubic Quart Quint Sine Expo Circ Back Elastic Bounce` (any of `outCubic` / `ease-out-cubic` / `easeOutCubic` spellings), or cubic-bezier `[x1,y1,x2,y2]`.
- Before the first / after the last keyframe the value clamps.

### Custom easing — four forms, and a name for each

A preset is a starting point, not the ceiling. Any keyframe's `ease` takes:

| form | example | what only this can do |
|---|---|---|
| a preset name | `"outCubic"` | the 33 named curves above |
| a **cubic-bezier** | `[0.34, 1.56, 0.64, 1]` | any two-handle curve, and **y outside 0–1 is real overshoot or anticipation** — *measured through the baker:* `[0.34,1.56,0.64,1]` peaks at **1.087**, `[0.5,-0.6,0.5,1.6]` dips to **−0.088** before it moves |
| an **arbitrary curve** | `{ "points": [[0,0],[0.18,0.62],[0.45,0.66],[1,1]] }` | a move that **hesitates** — two handles cannot express it. *Measured:* 0.635 at a quarter of the segment, still 0.675 at half, then clean to 1. Points are `[x,y]` with x ascending; interpolation is monotone, so it never wobbles between the points you gave |
| a **spring** | `"spring(180, 12)"` — stiffness, damping, optional mass | a decaying oscillation with a real settle, which no bezier can express. *Measured:* `spring(180,12)` peaks at 1.197 and settles at **0.92 s**; `spring(90,6)` peaks at 1.344 and settles at **1.88 s** |

⚠ **A spring's length is a RESULT, not an input.** It is sampled in real seconds over the
segment between its keyframe and the next, so a spring that needs longer than that segment gets
**cut off and snapped** to the keyframe value. *Measured:* `spring(90, 6)` in a 0.60 s segment is
**6.9 % short of target when the segment ends** and **±12.0 % of its swing never plays**.
`compile` and `validate` print exactly that, naming the ease — read it, then lengthen the segment,
raise the damping, or raise the stiffness. A spring in a long enough segment says nothing.

**`eases` — name a curve once, per scene.** A scene-level map resolves by name wherever an ease is
written, so a brand's motion is shared by name instead of by copied numbers ([brand.md](../brand/brand.md)):

```json
{ "width": 1080, "height": 1080, "fps": 25, "duration": 7,
  "eases": {
    "brandIn":  [0.2, 0.9, 0.1, 1],
    "hesitate": { "points": [[0,0],[0.18,0.62],[0.45,0.66],[1,1]] },
    "pop":      "spring(180, 12)"
  },
  "layers": [
    { "type": "solid", "name": "chip", "box": [0,0,40,40], "anchor": [20,20],
      "animate": { "position": [ {"t":0.5,"v":[170,360],"ease":"brandIn"}, {"t":2.1,"v":[900,360]} ] } }
  ] }
```

A token may hold any of the four forms, or another token's name. A token **shadows a preset of the
same name**, so a scene can redefine `out` for itself; a cycle is named rather than hanging. `eases`
is compiler sugar and never reaches the VASCO document.

### Audio-driven channels — a property that follows the music

A channel may be an **object instead of a keyframe list**, and the compiler bakes one value per
frame from an audio envelope. This is the answer to "make it react to the track" — and the only
way, since audio `volume` itself cannot be keyframed (see *Audio* below).

```json
"animate": { "scale": { "audio": "./bed.beats.json", "band": "low",
                        "range": [[1,1],[1,14]], "attack": 0.02, "release": 0.18,
                        "gate": 0.12, "gain": 1.4, "offset": 0 } }
```

| key | meaning |
|---|---|
| `audio` | a `.beats.json` from `strata beats <audio> --fps <comp fps> --bands 12` (**preferred** — `compile` then needs no ffmpeg), or an audio file, which is decoded on the spot and does need ffmpeg |
| `band` | `0…N-1`, or `"low"` / `"mid"` / `"high"` (the lower, middle, upper third of the bands, averaged), or `"rms"` / omitted for overall level, or **`"auto"`** on a layer with `repeat` — each copy reads its own index, which is how one layer becomes a spectrum analyser |
| `range` | `[atSilence, atFull]` — numbers, or vectors like `[[1,1],[1,14]]` so `position` and `scale` work. Default `[0,1]` |
| `attack` · `release` | seconds; asymmetric follower, fast rise and slow fall. Defaults `0.02` / `0.18` — *measured* across 12 bands, they cut mean frame-to-frame change from **0.1006 to 0.0588 (42 % less jitter)** without softening the hits. Set both to `0` for the raw envelope |
| `gate` | ignore below this level, so silence sits still at `range[0]` |
| `gain` | multiply before clamping — lift a quiet band to the top of its range |
| `offset` | seconds; shift the reaction earlier (negative) or later |

Two behaviours worth knowing. **The envelope is read at COMP time, not layer time** — a layer
entering at 4 s reacts to what the track is doing at 4 s, because the music does not restart for
it. And **bands are already normalised per band** by `strata beats`, so a treble band reaches the
top of its range as readily as the kick does ([generative-fx.md](../craft/generative-fx.md), §3, has the
measurement that forced this).

A twelve-bar analyser, whole:

```json
{ "type": "solid", "name": "bar", "color": "#4FB6C4", "box": [150,700,40,200], "anchor": [170,900],
  "repeat": { "count": 12, "step": { "position": [68,0] } },
  "animate": { "scale": { "audio": "./bed.beats.json", "band": "auto", "range": [[1,0.004],[1,1]] } } }
```

⚠ With an `anchor` set, `position` is the absolute point the anchor lands on, so `repeat`'s
position step counts **from the anchor** — that is handled, but it is why an anchored array's
`step` values are offsets from the anchor rather than from the box.

Channels on layers:

| channel | value | notes |
|---|---|---|
| `position` `scale` `rotation` `anchor` | as the static keys | baked together into one matrix animation; unanimated ones take their static value |
| `opacity` | 0..1 | |
| `color` | hex or `[r,g,b]` | solids / text tint |
| `visible` | bool | holds between keys |
| `zoom` | number | camera |
| *anything else* | raw VASCO value | passed to that channel name verbatim |

`animate` also works inside effects, mask shapes, and text animators/ranges (channels listed in those sections). Vectors, colors, and even mask shapes interpolate; booleans/strings hold.
