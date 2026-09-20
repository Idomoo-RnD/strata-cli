# Authoring scene JSON

Use this guide when creating or changing a compact Strata scene. Examples below
are either complete scene objects or explicitly identified layer fragments.
Adapt their content and timing to the request.

## Contents

- [Scene and layer model](#scene-and-layer-model)
- [Coordinates and anchors](#coordinates-and-anchors)
- [Text](#text)
- [Animation](#animation)
- [Groups and clipping](#groups-and-clipping)
- [Shapes, masks, and mattes](#shapes-masks-and-mattes)
- [Effects](#effects)
- [Media and audio layers](#media-and-audio-layers)
- [Examples and validation](#examples-and-validation)

## Scene and layer model

A scene defines a canvas, frame rate, duration, and bottom-to-top layer list.
This complete scene has no external assets:

```json
{
  "width": 1280,
  "height": 720,
  "fps": 25,
  "duration": 3,
  "layers": [
    {
      "type": "solid",
      "name": "background",
      "color": "#152238",
      "box": [0, 0, 1280, 720]
    },
    {
      "type": "solid",
      "name": "moving_panel",
      "color": "#DBEAFE",
      "box": [440, 260, 400, 200],
      "animate": {
        "position": [
          {"t": 0, "v": [0, 60], "ease": "outCubic"},
          {"t": 0.8, "v": [0, 0]}
        ]
      }
    }
  ]
}
```

Composition dimensions are limited to 1920 per axis, including sub-compositions.
`fps` is 1–120, default 25. `duration` is seconds; the raw alternative is
`num_of_frames`. Declare a duration on every composition.
Comp `shutter_angle` uses 0–2 (default 0.5, about 180°), with `shutter_phase`
available for blur timing. A comp's `transition` semantics are undocumented;
tagging supplies that field when needed.

| Layer key | Meaning |
|---|---|
| `type` | `solid`, `text`, `image`, `video`, `audio`, `comp`, or `camera`; `media` can infer an asset type |
| `name` | Unique across the entire scene, including sub-compositions |
| `start`, `duration` | Seconds; default start is zero and default layer duration reaches the comp end |
| `first_frame`, `num_of_frames` | Raw frame-based timing alternatives |
| `box` | `[x,y,width,height]`, in the containing composition |
| `position`, `anchor` | Position and pivot; see the coordinate rules below |
| `scale` | Uniform number or `[sx,sy]` / `[sx,sy,sz]` |
| `rotation` | Z degrees or `[xDegrees,yDegrees,zDegrees]` |
| `opacity`, `visible` | Opacity 0–1 and visibility boolean |
| `blend` | Compositing mode, such as `normal`, `multiply`, `screen`, or `add` |
| `animate` | Keyframe channels |
| `effects`, `mask`, `matte` | Effects and visibility constructions |
| `motion_blur` | Visual-layer blur; the compiler enables it unless explicitly disabled |

These are shared concepts, not permission to put every key on every layer type.
The schema rejects properties a particular type does not support.

Names are also matte references and personalization keys. The compiler can rename
duplicates, which changes those keys. Resolve duplicate-name warnings in the source.

Colors accept hex or normalized RGB/RGBA arrays. Layer/text-style colors discard
hex alpha; use `opacity` for transparency. Effect and animator colors retain alpha.

## Coordinates and anchors

`box` describes the layer's natural geometry. Without `anchor`, `position` acts
as an offset: `[0,40]` moves it down 40 pixels and `[0,0]` returns it.

With an `anchor`, `position` is the **absolute composition coordinate where that
anchor lands**. Its default equals the anchor, preserving the natural position.
For a centered scale/rotation plus movement, add offsets to the anchor:

```json
{
  "type": "solid",
  "name": "centered_panel",
  "color": "#FFFFFF",
  "box": [440, 260, 400, 200],
  "anchor": [640, 360],
  "animate": {
    "position": [
      {"t": 0, "v": [640, 400], "ease": "outCubic"},
      {"t": 0.8, "v": [640, 360]}
    ],
    "scale": [{"t": 0, "v": 0.9}, {"t": 0.8, "v": 1}]
  }
}
```

This is a layer fragment. An anchored animation ending at `[0,0]` would move the
pivot to the composition's top-left corner.

For 3D depth, use `position` z and a two-coordinate anchor. Camera coordinates
have their own projection rules in [advanced.md](advanced.md#camera-and-parallax).

## Text

Text requires a real font file. A family name does not embed a font.

```json
{
  "type": "text",
  "name": "headline",
  "text": "Your message",
  "font": "./font.ttf",
  "size": 80,
  "color": "#FFFFFF",
  "box": [120, 250, 1040, 140],
  "align": "center middle",
  "tracking": 0,
  "leading": 1.2,
  "shrink": true,
  "min_size": 40
}
```

This layer fragment needs a licensed `.ttf` or `.otf` covering the exact copy.
Run `strata glyphs font.ttf "Your message"` or `strata glyphs scene.json`.
Bold and italic need the corresponding font files; boolean styling flags do not
synthesize them.

Choose horizontal alignment from `left`, `center`, and `right`. In the current
renderer, vertical `top` behaves like bottom placement; `middle` centers the text.
Place bottom-aligned text by `box_y + box_height`, or use a centered box with
`middle`. Leave space for glyph extents and descenders. A wireframe preview does
not show actual text placement.

`shrink` fits text within its box; use `min_size` and adequate room to protect
legibility. `breakline`, `ellipsis`, `leading`, and `tracking` help control layout.
Test long and short replacement text rather than assuming fit is readable.

### Styled spans

`styles` is a list of `{start,length,...overrides}` with character-based offsets:

```json
{
  "text": "One two",
  "styles": [
    {"start": 0, "length": 4, "color": "#FFFFFF"},
    {"start": 4, "length": 3, "color": "#80B4FF"}
  ]
}
```

This is a fragment of a text layer. Spans must cover the whole string contiguously,
including spaces. Overrides include `font`, `size`, `color`, `tracking`, `leading`,
and `shift`. The compiler converts character offsets to UTF-8 offsets.

Native `underline`, `strikethrough`, and `highlight` styling does not render as
expected in this engine. Construct those treatments with separate graphics when
needed. The [marker example](../examples/marker-highlight.json) uses a solid
behind a word.

### Character and word animation

Animator properties affect the selected text units. For an ordered reveal, hide
the selected part with `opacity: 0`, keep `end` at 1, and animate `start` from 0 to 1:

```json
{
  "animators": [{
    "opacity": 0,
    "position": [0, 30, 0],
    "ranges": [{
      "based_on": "words",
      "shape": "square",
      "end": 1,
      "animate": {
        "start": [
          {"t": 0, "v": 0, "ease": "outCubic"},
          {"t": 1.5, "v": 1}
        ]
      }
    }]
  }]
}
```

This fragment belongs inside a text layer. Units include `characters`,
`characters_excluding_spaces`, `words`, and `lines`. A moving selection window
is useful for a passing effect, but leaves text outside it visible. It is not
equivalent to revealing a hidden string.

Use `square` for a clean ordered reveal; tapered range shapes can partially
select the first/last unit. For discrete typing, use intermediate keys with
`hold`, as in [typewriter.json](../examples/typewriter.json).

Other animator offsets include scale, rotation, tracking, skew, and color.
`character_offset` can cycle digits; separate currency and punctuation when an
offset would alter them. `character_value` uses a codepoint with
`character_range: "full_unicode"`.
Use normalized/percentage ranges when replacement text length varies; fixed
character indices address fixed positions, not the meaning of new words.

### Hebrew, Arabic, and mixed scripts

Keep text in logical order. The engine handles bidirectional layout; pre-reversing
a string corrupts it. The documented `rtl` flag does not fix layout problems in
the tested renderer. Check font coverage, box alignment, wrapping, and mixed
numbers/Latin text. Test actual replacement scripts in personalized scenes.

## Animation

`animate` channels accept arrays of `{t,v,ease}`. `t` is seconds relative to the
layer's start; `f` is the frame-based alternative. Values hold before the first
key and after the last.

An ease shapes the segment leaving a key. If absent, the next key's ease is used;
otherwise the segment is linear. `hold` keeps the current value until the next key.

| Ease form | Example |
|---|---|
| Named curve | `"outCubic"`, `"inOutSine"` |
| Cubic Bézier | `[0.34,1.56,0.64,1]` |
| Piecewise curve | `{"points":[[0,0],[0.2,0.6],[0.5,0.65],[1,1]]}` |
| Spring | `"spring(180,12)"` |

Named families include Quad, Cubic, Quart, Quint, Sine, Expo, Circ, Back, Elastic,
and Bounce with `in`, `out`, or `inOut`. Choose a curve for the intended motion;
there is no compulsory ease or overshoot amount.

Springs run in real seconds. A segment shorter than the spring's settle can end
in a snap; heed the warning, then change time or spring parameters. Bézier y
values outside 0–1 can overshoot, and arbitrary curve points need ascending x.

Scene-level `eases` can name a curve once:

```json
{
  "eases": {
    "brandEnter": [0.2, 0.9, 0.1, 1],
    "softSpring": "spring(180,12)"
  }
}
```

This fragment belongs at scene level. A key can then use `"ease":"brandEnter"`.
Avoid accidental token cycles or shadowing.

Layer channels include position, scale, rotation, anchor, opacity, color, and
visibility. Effects, mask shapes, and animator ranges have their own channels.
Booleans and strings hold rather than interpolate. Audio-driven channels are
covered in [advanced.md](advanced.md#audio-driven-motion).

## Groups and clipping

Define a sub-composition under `comps` and instantiate it as a `comp` layer.
Move the instance to move the group. There is no general per-layer `parent` chain.

```json
{
  "width": 1280, "height": 720, "fps": 25, "duration": 3,
  "comps": {
    "panel": {
      "width": 400, "height": 160, "duration": 3,
      "layers": [
        {"type":"solid","name":"panel_fill","color":"#DBEAFE","box":[0,0,400,160]}
      ]
    }
  },
  "layers": [
    {"type":"comp","name":"panel_instance","comp":"panel","box":[440,280,400,160]}
  ]
}
```

A comp's canvas clips its contents. Use it as a stationary window around moving
content, or use a separate static track matte. A mask attached to the moving
layer travels with that layer. Nested groups are nested comps; every comp needs
duration and every contained layer needs a globally unique name.

`strata add --list` discovers reusable blocks; `strata add lower-third scene.json`
inserts a sub-comp and instance with unique names. Inspect its fonts, assets,
dimensions, and duration before use. Many blocks start from a 1280×720 layout;
adapt scale and placement to the target canvas.

## Shapes, masks, and mattes

There is no separate general shape-layer type. Use a `solid` masked to the shape.

| Mask form | Meaning |
|---|---|
| `{"rect":[x,y,w,h]}` | Rectangle |
| Rectangle plus `"radius":24` | Rounded rectangle; per-corner `[tl,tr,br,bl]` also works |
| `{"ellipse":[cx,cy,rx,ry]}` | Center and radii, not a bounding box |
| `{"path":[[x,y],...]}` | Polygon; `closed` defaults true |
| `{"path":"M... C... Z"}` | SVG path data |
| `{"shapes":[...]}` | Multiple shapes with their own blend/options |

Shape options include feather, inversion, opacity, expansion, and blend
(`none`, `add`, `subtract`, `intersect`, `lighten`, `darken`, `difference`).
Compatible shape structures can morph through `animate.shape`.

A stroked mask makes exact line work. `stroke` is pixels; `cap` is `butt` or
`round`; `trim.start`, `trim.end`, and `trim.offset` are fractions or keyframes.

```json
{
  "type": "solid",
  "name": "line",
  "color": "#FFFFFF",
  "box": [0, 0, 1280, 720],
  "mask": {
    "path": "M180 510 C400 140 800 640 1100 240",
    "stroke": 16,
    "cap": "round",
    "trim": {"end":[{"t":0,"v":0},{"t":2,"v":1}]}
  }
}
```

This is a layer fragment. Prefer this construction for exact single-color paths;
use prepared media for textured or soft multi-color artwork.

`repeat` expands a layer at compile time:

```json
{
  "repeat": {
    "count": 12,
    "step": {"position":[60,0]},
    "stagger": 0.04
  }
}
```

This fragment belongs on a layer. Count is an integer from 1 to 500. Steps add
to position, rotation, scale, opacity, or anchor per copy. `stagger` delays
animation, or the layer itself when nothing is animated. `from` changes the
starting index. Inspect expanded names and timing.

For a track matte, reference a source layer in the same comp by name:
`"matte":{"type":"alpha","source":"matte_shape"}`. Supported types are `alpha`,
`alpha_inverted`, `luma`, and `luma_inverted`. The matte source can be
`"visible":false`. A text matte still needs a valid font.

## Effects

Place effects in a layer's `effects` array. Common compact forms:

```json
[
  {"type":"blur","amount":8,"dimensions":"both","repeat_edge":false},
  {"type":"shadow","color":"#000000","opacity":0.5,"angle":120,"distance":12,"size":8},
  {"type":"glow","color":"#80B4FF","opacity":0.5,"spread":0,"size":12,"range":0.5},
  {"type":"stroke","color":"#FFFFFF","size":3,"position":"inside"},
  {"type":"overlay","color":"#3464AA","blend":"color","opacity":0.3}
]
```

The four styles combine into a layer-styles effect. Style properties can animate
inside each effect; `enabled` can also animate. Shadow `knock:false` lets shadow
show under semi-transparent content. Stroke position accepts inside/center/outside.

Raw effects use `name` instead of compact `type`, with actual schema properties.
The raw layer-style name is `styles`. Its
`blending_options:{red,green,blue,opacity}` uses booleans for color channels.
Check the schema for less common blend/effect properties.

Corner pin supports `solid` and `comp` layers. Wrap text or images in a comp
before perspective-warping them. Pin order is upper-left, upper-right,
lower-left, lower-right. See [tracking](advanced.md#tracking-a-surface-or-subject).
Do not assume inner shadow, bevel, satin, or physical lighting exists.
For an authored pin, the compact effect accepts `from` and `to` arrays of four
`[x,y]` points, `crop`, and animation paths such as `to.upper_left`.

## Media and audio layers

Visual media uses local `src` files, embedded during compile:

```json
{
  "type": "video",
  "name": "footage",
  "src": "./clip.mp4",
  "box": [0, 0, 1280, 720],
  "fit": "fill",
  "start": 1,
  "duration": 4,
  "offset_frame": 24
}
```

This fragment assumes a suitable file and source coverage. `fit:"fill"` crops
to cover; `"fit"` preserves the full media inside the box. `offset_frame` is an
integer in composition frames, not seconds. `loop:true` selects looping;
`playback_mode` also supports `cut`, `loop`, and `hold`. Choose deliberately.

Conform source/scene frame rates when needed. At matched fps, require
`trim-in frames + slot frames <= available source frames`. A source that runs out
can hold its last frame. Inspect duration and streams with ffprobe rather than
using the requested generation duration as proof.

Transparent JET files also use a `video` layer:
`{"type":"video","name":"subject","src":"./subject.jet","box":[0,0,1280,720]}`.
Preparation and audio handling are in [media.md](media.md#transparent-video-and-jet).

Audio uses `src`, `start`, `duration`, and `volume` in dB:

```json
{"type":"audio","name":"narration","src":"./voice.wav","start":1,"duration":4,"volume":0}
```

This is a layer fragment. Audio volume is not an animatable channel, and audio
has no source `offset_frame`. Prepare fades, trim-ins, and ducking in the stem.
See [audio preparation](media.md#narration-music-and-audio-preparation) for the
current ducking limitation and mix workflow.

## Examples and validation

Copy an example into the project before adapting it. Text examples expect
`font.ttf` beside the copied scene; supply an appropriate licensed font.

| Example | Mechanic |
|---|---|
| [typewriter.json](../examples/typewriter.json) | Discrete reveal with intermediate hold keys |
| [flip-reveal.json](../examples/flip-reveal.json) | Selected character rotation/reveal |
| [path-reveal.json](../examples/path-reveal.json) | Mask stroke and path trim |
| [group-reveal.json](../examples/group-reveal.json) | Move a sub-comp instance |
| [camera-parallax.json](../examples/camera-parallax.json) | Camera projection and depth |
| [marker-highlight.json](../examples/marker-highlight.json) | Separate marker graphic and text |
| [timing-comparison.svg](../examples/timing-comparison.svg) | Visual lesson about frame spacing |

These retain their existing proof scope: scene fixtures have offline compile
coverage; that does not prove their final rendered appearance. The marker
construction also has an existing rendered comparison. Inspect each adapted
output rather than treating a fixture as a finished design.

```bash
strata validate scene.json
strata compile scene.json --vasco -o scene.idm
strata preview scene.json --at 1 --grid
```

`--vasco` helps inspect translation into raw values. Unknown compact keys pass
through, then the strict raw schema can reject them. Properties such as `zIndex`,
layer `width`, or a top-level layer `radius` are not substitutes for the documented
forms. For a raw property absent here, inspect `strata schema` and test a small
construction. Runtime/render verification follows [delivery.md](delivery.md).
