# Type recipes

Reveals, per-character animation, counters, masks on text, and the patterns that make copy arrive well.

Part of the recipe library — the index, and every other part, is in [recipes.md](../recipes.md).

**Blocks below are layer fragments unless stated otherwise.** They need a scene wrapper, duration,
unique names and actual font/assets. [Runnable scenes](../examples/runnable-scenes.md) are complete
fixtures checked offline. Renderer-sensitive reveal proofs are explicitly pending, not newly verified.

## 1. Text

### Tracking-in (letters spread → converge)
```json
{ "type": "text", "text": "DESIGN", "font": "./font.ttf", "size": 130,
  "box": [0,250,1280,180], "align": "center middle", "anchor": [640,340],
  "color": "#f2eee3",
  "animators": [{ "tracking": 50, "tracking_type": "before_and_after",
    "ranges": [{ "shape": "square", "animate": { "amount": [{"t":0,"v":1,"ease":"outExpo"},{"t":1.0,"v":0}] } }] }],
  "animate": { "opacity": [{"t":0,"v":0},{"t":0.4,"v":1,"ease":"outCubic"}] } }
```

### Rich multi-style headline (per-span colour + word entrance)
Per-span `color`/`size`/`tracking` plus a word-by-word rise-and-fade. **Spans must cover every character including spaces** (extend each over its trailing space) or the gaps vanish. Bold/italic need a real variant font in the span's `font`; `underline`/`strikethrough`/`highlight` don't render.
```json
{ "type": "text", "name": "headline", "text": "Rich VASCO Text",
  "font": "./font.ttf", "size": 120, "color": "#ffffff",
  "box": [120,120,1040,150], "align": "left middle", "tracking": 1,
  "styles": [
    { "start": 0,  "length": 5, "color": "#ff5a5f", "font": "./font-bold.ttf" },
    { "start": 5,  "length": 6, "color": "#ffd166", "tracking": 6 },
    { "start": 11, "length": 4, "color": "#4cc9f0" }
  ],
  "animators": [{ "opacity": 0, "position": [0,60,0],
    "ranges": [{ "based_on": "words", "shape": "square", "end": 1,
      "animate": { "start": [{"t":0,"v":0},{"t":1.6,"v":1,"ease":"outCubic"}] } }] }] }
```
*(`end` is pinned at 1 — every word not yet reached stays selected, so hidden. Animating `end` alongside `start` makes a travelling window, and the words outside it show from frame one: [format.md](../format.md), Per-character animators.)*
*(Fake underline: a thin `solid` bar under the box — only when the brief asks ([anti-slop.md](../craft/anti-slop.md)). Fake highlight: a `solid` behind the text layer.)*

### Typewriter with a caret that follows the text

A typewriter needs one selection step per character, not just two endpoints. This fragment reveals
`TYPE` at 0.4 s intervals; `square` and pinned `end:1` keep unreleased characters selected/hidden.
The complete [typewriter fixture](../examples/runnable-scenes.md#typewriter) is schema/baker-tested.

```json
{ "type":"text", "name":"typed_title", "text":"TYPE", "font":"./font.ttf", "size":120,
  "box":[180,240,920,180], "align":"left middle",
  "animators":[{ "opacity":0, "ranges":[{ "based_on":"characters", "shape":"square", "end":1,
    "animate":{ "start":[
      {"t":0,"v":0,"ease":"hold"}, {"t":0.4,"v":0.25,"ease":"hold"},
      {"t":0.8,"v":0.5,"ease":"hold"}, {"t":1.2,"v":0.75,"ease":"hold"}, {"t":1.6,"v":1}
    ] } }] }] }
```

For other copy, generate `start = k / characterCount` at each typing time. A caret is an optional
separate solid stepped at those same times using **actual font glyph advances**, or an explicitly
monospaced layout with known cell width. Do not copy approximate Arial widths for an arbitrary font.
The fixture deliberately omits a guessed caret; prove real glyph placement with a rendered frame.

### Line-by-line stagger
Use `shape: "square"` and an explicit `end: 1` (see the ⚠️ under "Per-word bounce-in").
```json
{ "type": "text", "text": "CREATE\nPERSONALIZE\nDELIVER", "font": "./font.ttf", "size": 92,
  "box": [0,160,1280,400], "align": "center middle", "leading": 1.15,
  "animators": [{ "opacity": 0, "position": [0,46,0],
    "ranges": [{ "based_on": "lines", "shape": "square",
      "animate": { "start": [{"t":0,"v":0,"ease":"outCubic"},{"t":1.6,"v":1}], "end": [{"t":0,"v":1}] } }] }] }
```

### Per-word bounce-in
Use `shape: "square"` and pin `end` at 1 so the whole string is hidden at t=0; `outBack` on `start` gives the bounce.
```json
{ "type": "text", "text": "one video each", "font": "./font.ttf", "size": 96,
  "box": [0,290,1280,150], "align": "center middle",
  "animators": [{ "opacity": 0, "position": [0,70,0],
    "ranges": [{ "based_on": "words", "shape": "square",
      "animate": { "start": [{"t":0,"v":0,"ease":"outBack"},{"t":1.7,"v":1}], "end": [{"t":0,"v":1}] } }] }] }
```
> ⚠️ **Do not use `shape: "smooth"` (or `round`/`triangle`) for a "hidden at t=0, reveal in order" effect** — verified bug. A tapered shape softens the selection at *both* window edges, so pinning `end` at 1 parks the trailing taper on the **last** unit: it starts partly visible, goes fully hidden as the window narrows onto it, then reappears (visible → gone → visible, a glitch); the **first** unit sits in the leading taper, visible on frame 1, so it never animates in. Widening the window does **not** fix it — the taper scales with it. `square` has hard edges: the string is cleanly hidden at t=0 and releases unit-by-unit (with soft partial-brightness frames at each boundary, so the cascade isn't mechanical). Tapered shapes are for *continuous* effects (waves, colour sweeps) where the window stays **off** the string ends.

### Wavy / travelling lift
```json
{ "type": "text", "text": "WAVE MOTION", "font": "./font.ttf", "size": 110, "color": "#b794ff",
  "box": [0,290,1280,160], "align": "center middle",
  "animators": [{ "position": [0,-34,0],
    "ranges": [{ "based_on": "characters", "shape": "round", "units": "percentage",
      "animate": { "start": [{"t":0,"v":0,"ease":"inOutSine"},{"t":2,"v":1},{"t":4,"v":0}],
                   "end":   [{"t":0,"v":0.2,"ease":"inOutSine"},{"t":2,"v":1.2},{"t":4,"v":0.2}] } }] }] }
```

### RGB-split glitch (VERIFIED with channel masks)
Three additive copies of the **same white text**, each showing **one colour channel** via
`blending_options`, jittered on hold steps. Where they overlap they recombine to the
original colour, so the split reads as lens aberration, not three coloured words.
```json
{ "type": "text", "text": "GLITCH", "font": "./font.ttf", "size": 150, "color": "#ffffff",
  "box": [0,250,1280,200], "align": "center middle", "blend": "add", "anchor": [640,350],
  "effects": [{ "name": "styles", "blending_options": { "red": true, "green": false, "blue": false } }],
  "animate": { "position": [{"t":0,"v":[646,350],"ease":"hold"},{"t":0.2,"v":[634,350]},{"t":0.5,"v":[648,350]},{"t":0.9,"v":[638,350]}] } }
```
*(Copy 2: `green` only, copy 3: `blue` only, each with its own jitter; one can stay still.)*
The older recolouring version (three copies tinted `#ff0040` / `#00ff90` / `#4080ff`) works
but never recombines to white:
```json
{ "type": "text", "text": "GLITCH", "font": "./font.ttf", "size": 150, "color": "#ff0040",
  "box": [0,250,1280,200], "blend": "add", "anchor": [640,350],
  "animate": { "position": [{"t":0,"v":[646,350],"ease":"hold"},{"t":0.2,"v":[634,350]},{"t":0.5,"v":[648,350]},{"t":0.9,"v":[638,350]}] } }
```

### Per-letter 3D flip-in

Corrected to use the established square/pinned selection pattern. The [complete fixture](../examples/runnable-scenes.md#flip-reveal)
passes offline compilation and initial selection checks; actual 3D glyph appearance still needs
first/middle/last cloud proof. Do not call this correction newly render-verified.
```json
{ "type": "text", "text": "ROTATE", "font": "./font.ttf", "size": 140, "box": [0,250,1280,190],
  "align": "center middle", "anchor": [640,345], "motion_blur": true,
  "animators": [{ "opacity": 0, "rotation": [0,90,0],
    "ranges": [{ "based_on": "characters", "shape": "square", "end": 1,
      "animate": { "start": [{"t":0,"v":0,"ease":"outCubic"},{"t":1.6,"v":1}] } }] }] }
```

### Colour sweep across letters
```json
{ "type": "text", "text": "SPECTRUM", "font": "./font.ttf", "size": 120, "color": "#3a4668",
  "box": [0,280,1280,170], "align": "center middle",
  "animators": [{ "color": "#22d3ee",
    "ranges": [{ "based_on": "characters", "shape": "round", "units": "percentage",
      "animate": { "start": [{"t":0,"v":-0.2,"ease":"linear"},{"t":3.6,"v":0.9}],
                   "end":   [{"t":0,"v":0,"ease":"linear"},{"t":3.6,"v":1.1}] } }] }] }
```

### Marker highlight sweep
```json
{ "type": "solid", "name": "mark", "color": "#fde047", "box": [430,320,420,92], "anchor": [430,366], "opacity": 0.9,
  "animate": { "scale": [{"t":0.3,"v":[0,1],"ease":"outExpo"},{"t":1.0,"v":[1,1]}] } }
```
*(Place the dark text layer on top.)*


### Seamless loop — first frame equals last frame, invisibly

For backgrounds, stickers and web embeds that repeat. Three rules, all load-bearing
(*measured: a 4 s scene concatenated 3× with invisible joins*):

1. **Every keyframed value returns to its start** — closed paths: `A → B → A`, with the
   SAME easing family on the way out and back (`inOutSine` both ways puts velocity ≈ 0 at
   the seam, so the join cannot pop).
2. **Rotations complete full turns** — `0 → 360` reads as continuous across the cut.
3. **Nothing keys off absolute time** — no element may still be mid-entrance at the end.

```json
"animate": { "position": [ {"t":0,"v":[0,0],"ease":"inOutSine"},
                            {"t":2,"v":[180,140],"ease":"inOutSine"},
                            {"t":4,"v":[0,0]} ] }
```

Prove it before shipping: render once, `ffmpeg -f concat` three copies, watch the joins.
For a generated (AI) loop: keyframe interpolation with the SAME image as `--first-frame`
and `--last-frame` closes the loop at the model level ([video-generation.md](../video-generation.md)).

### Rule wipe — a divider drawing on between two regions
A rule that *separates* (a header from a body, two columns) may draw on. A rule under a lone title is decoration — [anti-slop.md](../craft/anti-slop.md).
```json
{ "type": "solid", "name": "rule", "color": "#d9dde3", "box": [410,408,460,2], "anchor": [410,409],
  "animate": { "opacity": [{"t":0.5,"v":0},{"t":0.6,"v":1}],
               "scale":   [{"t":0.6,"v":[0,1],"ease":"outExpo"},{"t":1.3,"v":[1,1]}] } }
```

### Textured text — gradient / stripes / gold shine (VERIFIED)
Fill type with ANY texture via a track matte: an invisible `text` layer is the alpha
matte, a texture layer shows only through the glyphs. **Oversize the texture past the
frame** so drifting never exposes an edge, and **animate the texture, not the
word** — motion inside still letters is the designed look, and it is what keeps a locked
logotype alive through a long hold ([craft.md](../craft.md), *How much moves*). Animating the
*matte* (entrance scale/position) animates the visible glyph shapes.
⚠️ **Over a photographic plate, a band running level across every glyph gives the trick away** — it
proves the fill is a rectangle behind a mask, not light on a surface. Drift the texture along the
plate's key direction, generate it with that lighting in it, and add a `shadow` layer style pointing
the same way.

**Where textures come from — pick by kind:**
- **Organic / photographic** (gold foil, marble, brushed metal, fire, silk):
  **`strata generate image`** — prompt for a full-frame texture with no subject,
  e.g. `strata generate image "seamless crumpled gold foil texture, full-frame, no
  objects, even lighting" --aspect 16:9`. Brand palettes via `--colors`.
- **Geometric / exact** (linear gradients, stripes, checker, halftone dots): a tiny
  generator script (computed pixels — generative-fx.md has the PNG pattern).

```json
// 1) gradient fill, drifting
{ "type": "text",  "name": "grad_matte", "text": "GRADIENT", "font": "./bold.ttf", "size": 210,
  "box": [0,210,1280,280], "align": "center middle", "visible": false,
  "animate": { "scale": [{"t":0,"v":0.82,"ease":"outBack"},{"t":0.6,"v":1}] } },
{ "type": "image", "name": "grad_fill", "src": "./tex_gradient.png",
  "box": [-160,-90,1600,900], "fit": "fill", "matte": { "type": "alpha", "source": "grad_matte" },
  "animate": { "position": [{"t":0,"v":[0,0],"ease":"inOutSine"},{"t":4,"v":[-120,-70]}] } }
```
```json
// 2) stripes scrolling INSIDE the letters (oversized tileable stripe texture)
{ "type": "image", "name": "zebra_fill", "src": "./tex_stripes.png",
  "box": [-640,-360,2560,1440], "fit": "fill", "matte": { "type": "alpha", "source": "zebra_matte" },
  "animate": { "position": [{"t":0,"v":[0,0]},{"t":4,"v":[320,320]}] } }
```
```json
// 3) gold shine sweep — TWO mattes of the same word (unique names): one for the
// static gold fill, one for a rotated blurred white band sweeping through
{ "type": "solid", "name": "shine_band", "color": "#ffffff",
  "box": [0,-150,150,1020], "rotation": 14, "opacity": 0.9,
  "effects": [ { "type": "blur", "amount": 14, "dimensions": "both" } ],
  "matte": { "type": "alpha", "source": "shine_matte" },
  "animate": { "position": [{"t":0.8,"v":[-350,0],"ease":"inOutCubic"},{"t":1.7,"v":[1500,0]}] } }
```
Multi-pass is the general trick: **one invisible text copy per fill pass** (base texture +
moving highlight + scan-lines + …) — blur/rotation/blend all compose with a matte. A
`video` layer through the same matte = footage-in-letters (video-layouts.md §3).

---
