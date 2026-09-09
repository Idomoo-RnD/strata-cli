# Effects, masks and track mattes

Blur, glow, grade and the raw effect surface; mask shapes with strokes, draw-on and repeat; and using one layer as another's matte.

Part of the scene-format reference — the index, and every other part, is in [format.md](../format.md).

## Effects

```json
"effects": [
  { "type": "blur", "amount": 8, "dimensions": "both", "repeat_edge": false,
    "animate": { "amount": [ {"t":0,"v":0}, {"t":1,"v":8} ] } },
  { "type": "shadow",  "color": "#000000cc", "opacity": 0.75, "angle": 120, "distance": 40, "spread": 0, "size": 5 },
  { "type": "glow",    "color": "#ffff00", "opacity": 0.75, "spread": 0, "size": 5, "range": 0.5 },
  { "type": "stroke",  "color": "#331a00", "size": 3, "position": "outside" },
  { "type": "overlay", "color": "#ff000080", "blend": "overlay", "opacity": 1 },
  { "type": "corner_pin",
    "from": [[0,0],[1280,0],[0,720],[1280,720]],
    "to":   [[100,50],[1180,90],[80,700],[1200,680]],
    "crop": 0,
    "animate": { "to.upper_left": [ {"t":0,"v":[100,50]}, {"t":2,"v":[0,0]} ] } }
]
```

- `shadow`/`glow`/`stroke`/`overlay` merge into one layer-styles effect per layer. Animatable channels inside them: their own keys (`color`, `opacity`, `distance`, `size`, …) — the CLI prefixes the VASCO path (`drop_shadow.color` etc.).

### The four layer styles in full — every key the engine accepts

These four are the **whole** set VASCO has (there is no inner shadow, bevel, satin or gradient
overlay). Each one takes more than the example line above shows:

| Style | VASCO section | Keys | Notes |
|---|---|---|---|
| `shadow` | `drop_shadow` | `color` `opacity` `angle` `distance` `spread` `size` `knock` | `knock` defaults **true** — the shadow is knocked out from under the layer. Set `"knock": false` when the layer is semi-transparent and the shadow should show through it |
| `glow` | `outer_glow` | `color` `opacity` `spread` `size` `range` | `range` (0–1) moves the falloff: low = tight halo, high = soft bloom |
| `stroke` | `stroke` | `color` `opacity` `size` `position` | `position` is `outside` (default) · `center` · `inside`. `inside` keeps the layer's silhouette exactly — the right choice on a masked shape or a text layer that must not grow |
| `overlay` | `overlay` | `color` `opacity` `blend` | **`blend` takes 24 modes**, not just `overlay` — see below |

**`overlay.blend` — the full set.** A colour overlay is the cheapest way to tint, grade, knock
back or duotone a layer without touching the asset, and the mode is what decides which:

```
normal · multiply · screen · overlay · softlight · hardlight · vividlight · linearlight
pinlight · hardmix · darken · lighten · colorburn · colordodge · linearburn · lineardodge
difference · exclusion · subtract · divide · hue · saturation · color · luminosity
```

Practical picks: `multiply` to deepen and tint shadows · `screen` / `lineardodge` to lift and
glow · `softlight` for a gentle grade that keeps the highlights · `color` to recolour while
keeping the original luminance (the honest duotone) · `luminosity` to keep the colour and take
the brightness.

**Every style has an `enabled` flag, and it animates.** The compiler sets `enabled: true` for
you; animating `<style>.enabled` switches a style on or off mid-shot (a stroke that appears only
while a card is selected, a glow that fires on the beat) without a second layer:

```json
{ "type": "glow", "color": "#7cf", "size": 22,
  "animate": { "enabled": [ {"t":0,"v":false}, {"t":1.2,"v":true} ],
               "size":    [ {"t":1.2,"v":8}, {"t":1.5,"v":22,"ease":"outExpo"} ] } }
```

`blending_options` also carries an `opacity` alongside its three channel booleans — a second
opacity that multiplies with the layer's own, useful when the layer's `opacity` is already
carrying an animation you do not want to disturb.
- **Channel masks (VERIFIED):** the same layer-styles effect carries `blending_options`
  `{ red, green, blue, opacity }` — R/G/B are **booleans** that switch a colour channel off.
  A white solid with `{"red":true,"green":false,"blue":false}` renders pure red. Use it as a
  raw effect: `{ "name": "styles", "blending_options": { "red": true, "green": false, "blue": false } }`
  (the raw name must be exactly `styles`). Three copies of one layer, each with one channel on
  and `blend: "add"`, offset a few px, make a true chromatic-aberration split that recombines to
  white where they overlap — recipes.md "RGB split".
- Corner-pin pins order: `[upper_left, upper_right, lower_left, lower_right]` (or `{ul, ur, ll, lr}`).
- ⚠️ **Corner pin only applies to `solid` and `comp` layers** (verified by render). On a `text` layer it is
  silently ignored (the text renders flat); on an `image` layer the layer disappears entirely. **To perspective-
  warp text or an image, put it in a sub-comp and corner-pin the `comp` instance** — the same pre-comp trick as
  After Effects. Every corner is independently animatable (`to.upper_left`, `to.upper_right`, `to.lower_left`,
  `to.lower_right`), so a per-frame planar track can drive it to lock content onto a moving surface.
- Raw VASCO effects pass through when given a `name` instead of `type` (e.g. `{"name": "blur", "blurriness": 5, "dimensions": "both", "repeat_edge_pixels": false}`), still with `animate` support.

## Masks

```json
"mask": { "rect": [0, 500, 1280, 140], "feather": 12 }                  // single shape — feather renders (measured: 60 → a ~120 px ramp, 255 to 0, at 1280×720)
"mask": { "rect": [88, 516, 322, 74], "radius": 37 }                    // rounded rect
"mask": { "rect": [88, 516, 322, 74], "radius": 999 }                   // pill (radius clamps to h/2)
"mask": { "rect": [0, 0, 400, 300], "radius": [24, 24, 0, 0] }          // per-corner [tl,tr,br,bl]
"mask": { "shapes": [                                                    // multi-shape
  { "ellipse": [640, 360, 200, 120], "feather": [10, 10], "inverted": false,
    "opacity": 1, "expansion": 0, "blend": "add",
    "animate": { "shape": [ {"t":0,"v":{"ellipse":[640,360,60,60]}},
                            {"t":2,"v":{"ellipse":[640,360,200,120]},"ease":"inOutCubic"} ] } },
  { "path": [[100,100],[400,100],[250,350]], "closed": true },
  { "shape": [ {"type":"move_to","values":[0,0]}, {"type":"cubic_to","values":[10,0,20,10,20,20]} ] }
] }
```

Shapes: `rect [x,y,w,h]` (+ optional `radius`: a number, or `[tl,tr,br,bl]`; each clamps to half the shorter side, so a big number gives a pill — this is how rounded cards, pills and CTA buttons are made) · `ellipse [cx,cy,rx,ry]` · `path [[x,y],...]` (`closed` defaults true) — or the same `path` key as an **SVG `d` string** (`M L H V C S Q T A Z` and their relative forms; arcs included) · `shape` = raw VASCO commands (`move_to`/`line_to` 2 values, `quadratic_to` 4, `cubic_to` 6). Mask blend modes: `none add subtract intersect lighten darken difference`. Shape keyframes interpolate (morph) when both ends have the same structure.

### Strokes and draw-on — `stroke` + `trim`

There is **no shape layer** in VASCO (layers are only `solid · text · media · audio · camera ·
composition`), so a vector element is a **solid masked to the shape**, and a *stroke* is a solid
masked by the **outline of a path**. `stroke` on a mask does that outlining; `trim` cuts the path
to a fraction of its length, and a keyframed `trim` is a draw-on:

```json
{ "type": "solid", "color": "#F4B23F", "box": [0,0,1080,1080],
  "mask": { "path": "M120 700 C360 300 760 940 980 470", "stroke": 14, "cap": "round",
            "trim": { "end": [ {"t":1.2,"v":0,"ease":"out"}, {"t":3.0,"v":1} ] } } }
```

`stroke` is the width in px · `cap` is `butt` (default) or `round` · `trim` takes `start`, `end`
and `offset` (fractions 0–1), each a number or a keyframe list. `offset` slides the drawn window
along the path and **wraps on a closed path**, which is how a ring dash orbits. A closed path
drawn all the way round is built as a **ring** — two closed loops, no caps — because an
out-and-back outline crosses itself at the seam and the fill rule punches a hole there
(*measured:* a dark notch at 12 o'clock on a 10 px ring until the ring case was added).

Because the trim is baked per frame from the path itself, the stroke follows the curve exactly;
it is not an interpolation between two sampled outlines. Everything a draw-on needs is therefore
in the scene: no asset, live colour (so it personalises), no fps coupling, and the authored
coordinates render where they were written. **This is the route for any solid-colour line work** —
underlines, progress rings, signatures, route lines, logo draw-ons, animated diagrams.
`.jet` remains the route for soft edges, gradients, textured strokes and particles
([traps.md](../traps.md), [assets.md](../assets.md)).

`strata preview` shows this without a render: it expands `repeat`, puts each layer through its
transform at that moment, and draws a **mask as its own outline** — a draw-on appears as the line
drawn so far (the legend says `stroke 14px 50%`), not as the full-frame box its solid sits on.

*Measured, same 5 s 1080² scene both ways* — three strokes plus a 24-tick array as masks vs as
`strata path` overlays: **1,368 KB `.idm` against 4,620 KB**, the curve drawn at its authored
span (120→980 authored, 116→984 measured) against **200→896** for the `.jet` (which rescales the
artwork to the canvas), a hard mask edge against DCT softening and a grey fringe, and 2 assets
against 5. Render time was the same within a few seconds.

### Repeat — one object, a step, a stagger

`repeat` on any layer expands at **compile time**, before validation and preview, so the scene
that renders is the layers you can count:

```json
{ "type": "solid", "name": "tick", "color": "#39424F", "box": [538,168,4,18], "anchor": [540,540],
  "repeat": { "count": 24, "step": { "rotation": 15 }, "stagger": 0.03 },
  "animate": { "scale": [ {"t":0.15,"v":[0,0],"ease":"out"}, {"t":0.45,"v":[1,1]} ] } }
```

`count` 1–500 (an integer; anything else is refused rather than authoring 40,000 layers) ·
`step` on `position` `rotation` `scale` `opacity` `anchor`, added `k` times to copy `k` (and added
to that channel's keyframe *values* when it is animated) · `stagger` seconds, which delays each
copy's **animation** — with nothing animated it delays the layer itself · `from` shifts the
starting index. Copies are named `tick_01 … tick_24`, so `--data` can still address one.
Rotation steps orbit the layer's `anchor`, which is what makes radial arrays, tick scales,
equaliser bars and dot fields one object instead of two dozen that drift apart on the next
revision.

## Track mattes

The matte source must be a layer (usually `"visible": false`) in the same comp; reference it by name:

```json
{ "type": "text", "name": "matte-text", "text": "MATTE", "visible": false, ... },
{ "type": "image", "src": "./photo.jpg", "matte": { "type": "alpha", "source": "matte-text" } }
```

Types: `alpha alpha_inverted luma luma_inverted`.
