# Data-visualisation recipes

Bars, lines, counters and gauges built so their values can still be swapped per viewer.

Part of the recipe library — the index, and every other part, is in [recipes.md](../recipes.md).

## 7. Data viz

The snippets below are **layer fragments**, not complete scenes. Supply wrapper, duration,
unique names and real assets/fonts. [Runnable examples](../examples/runnable-scenes.md) show the
complete pattern. Choose the [chart strategy](../engine/personalization.md#chart-strategy--choose-before-authoring)
first: geometry is baked at emission; changing geometry needs per-row scenes or exact chart-image
replacement. A label swap alone cannot change a shape. Use deterministic plotting for numeric
images, never image synthesis to calculate values. Reveals must not imply data different from the
final chart.

### Count-up number — NATIVE, and safe for personalization (VERIFIED by render)
The text animator's **`character_offset`** shifts every selected digit by N, **wrapping
modulo 10** — so animating it **`0 → 10·k`** spins each digit k full turns and lands
**exactly on the layer's own text** — personalization-safe, because the roll ends on
whatever value the API substitutes. Measured: `2400` with offset 0→20 showed
`8066`, `1399`, … and ended on `2400`.
```json
{ "type": "text", "name": "kpi_value", "text": "2400", "font": "./font-bold.ttf", "size": 160, "color": "#fff",
  "box": [40,110,700,200], "align": "left middle",
  "animators": [
    { "character_offset": 0, "character_range": "case_and_digits",
      "ranges": [{ "shape": "square", "units": "index", "start": 0, "end": 3 }],
      "animate": { "character_offset": [{"t":0,"v":0,"ease":"outCubic"},{"t":1.6,"v":20}] } },
    { "character_offset": 0, "character_range": "case_and_digits",
      "ranges": [{ "shape": "square", "units": "index", "start": 3, "end": 4 }],
      "animate": { "character_offset": [{"t":0,"v":0,"ease":"outCubic"},{"t":1.6,"v":50}] } }
  ] }
```
The second animator makes the **last digit spin faster** (5 turns vs 2) — the odometer feel.
Rules, each one measured:
- **The final keyframe must be an exact multiple of 10**, or it lands on the wrong number.
- **Digits only in the animated layer.** `case_and_digits` does **not** protect symbols here:
  `$1,280` rolled through `=6_735`, `{6%35` and ended on `"1*280`. Put currency, commas and
  units in a **separate** text layer.
- **Never go negative** — a negative offset does not wrap, the glyph simply disappears.
- `linear` / `outCubic` read as a roll; `outExpo` hits the end value in a few frames and then
  sits — fine for a "snap to the number", but not a count.
- **`character_value`** (with `character_range: full_unicode`) replaces each selected glyph
  with a codepoint — animate 48→57 and an `A` becomes `0`…`9`: the scramble / decode reveal.
  Run it over the string, then let the real text show.
For a rolling **strip** (digits physically sliding) build the nested-comp odometer in
video-styles.md; for a plain numeric count this animator is simpler and cheaper.

### Bar chart (mask wipe L→R)
The bar solid is full width; a rect mask grows its width from 0 to full.
```json
{ "type": "solid", "name": "bar_a", "color": "#4cc9f0", "box": [120,300,700,48],
  "mask": { "rect": [120,300,0,48], "animate": { "shape": [ {"t":0,"v":{"rect":[120,300,0,48]}}, {"t":1,"v":{"rect":[120,300,560,48]},"ease":"outCubic"} ] } } }
```
*(560/700 = 80%. Stagger several bars with offset `start`; label each with a text layer.)*

### Progress ring (expanding wedge)

Do not rotate a complete ring to depict a percentage: rotation does not change its filled amount.
For an animated data arc, use `strata chart donut --value <percent> --box x,y,w,h`, or a stroked
closed path with `trim.end` stopping at the required fraction ([masks](masks.md)). The arc amount
is baked; if the value changes per viewer, follow the chart strategy above. The
[path fixture](../examples/runnable-scenes.md#path-reveal) demonstrates the native trim construction.

### Stat bar / percentage fill
A track solid + a fill solid whose width is masked open.
```json
{ "type": "solid", "name": "track", "color": "#1c2030", "box": [120,420,700,18] },
{ "type": "solid", "name": "fill",  "color": "#34a853", "box": [120,420,700,18],
  "mask": { "rect": [120,420,0,18], "animate": { "shape": [ {"t":0,"v":{"rect":[120,420,0,18]}}, {"t":0.9,"v":{"rect":[120,420,476,18]},"ease":"outQuint"} ] } } }
```

### Line-chart draw (path mask sweep)
Sweep a rectangular mask across a pre-drawn line image/solid path.
```json
{ "type": "image", "name": "line_plot", "src": "./line.png", "box": [120,140,1040,440], "fit": "fit",
  "mask": { "rect": [120,140,0,440], "animate": { "shape": [ {"t":0,"v":{"rect":[120,140,0,440]}}, {"t":1.6,"v":{"rect":[120,140,1040,440]},"ease":"inOutSine"} ] } } }
```

### Parallax depth (3D) — MEASURED
Give layers different z and drift the camera. **Far = POSITIVE z** (smaller), **near = negative
z** (bigger); camera at `−focal` so z=0 is true size. Sizes follow `scale = focal/(z − z_cam)`
(format.md, Camera) — so the bg at z=+400 with the camera at −623 renders at 623/1023 = 0.61×
and must be oversized by 1/0.61 ≈ 1.65× to fill the frame.
```json
{ "type": "camera", "name": "cam", "fov": 60, "motion_blur": true, "animate": { "position": [ {"t":0,"v":[640,360,-623],"ease":"inOutSine"}, {"t":4,"v":[700,360,-560]} ] } },
{ "type": "image", "name": "bg_far",  "src": "./bg.jpg",  "box": [-420,-240,2120,1200], "is_3d": true, "position": [0,0,400] },
{ "type": "image", "name": "mid_card", "src": "./card.png","box": [340,180,600,360], "is_3d": true, "position": [0,0,120] },
{ "type": "text",  "name": "fg_title", "text": "Depth", "font": "./font-bold.ttf", "size": 120, "color": "#fff", "box": [0,300,1280,160], "align": "center middle", "is_3d": true, "position": [0,0,0] }
```
*(No anchors here, so `position` x,y stay `0` and the box places each layer; depth lives in z.
Near layers drift more than far ones as the camera moves — that differential is the parallax.
Draw order is still layer order, not z.)*

---
