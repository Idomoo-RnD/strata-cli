# Block library — reusable sub-comps (reuse-first)

Drop-in **sub-composition templates** for the parts every video needs. ⚠ **Coordinates here assume a 1280×720 comp** — for the 1920×1080 grid in layouts.md multiply every box and size by **1.5**. Each block is a VASCO sub-comp in the compact format with **unique layer names**; paste it into your scene's `comps`, then add a `comp` layer that instantiates it (with its own `box`, `start`, `duration`, and entrance animation). Or let the CLI wire it for you:

```bash
strata add lower-third scene.json      # injects the block's sub-comp + a comp-instance stub
strata add --list                      # list available blocks
```

`strata add` uniquifies the block's comp/layer names against the scene, so you can add the same block many times safely. Tune colours, text, and box after adding. Blocks pair with the recipes in [recipes.md](recipes.md) for entrances/exits.

> Personalization: rename a block's text/media layers to meaningful, unique keys (`hero_title`, `cta_label`) so they're API-replaceable. See [personalization.md](personalization.md).

> ⚠️ **Adding the same block twice by hand is the #1 source of duplicate layer names.** Layer
> names must be unique **across every comp**, so two pasted cards both containing `label`
> collide — and the compiler silently renames one to `label_2`, changing its personalization
> key. Prefix the inner layers per instance (`card1_label`, `card2_label`) as you paste, or use
> `strata add`, which uniquifies for you. Never ignore a `⚠ renamed … duplicate layer name(s)`
> warning (format.md, Layers).

## Contents

- [Available blocks](#available-blocks)
- [Using a block well](#using-a-block-well)

## Available blocks

### lower-third
A name/title strip for the bottom-left. Solid bar + two text lines. **Ships without a side accent bar** — an 8 px colour bar on the edge of a lower-third is the single most recognisable agent tell ([anti-slop.md](anti-slop.md)). If the **brand's** motion system has one, add it in the brand colour: `{ "type": "solid", "name": "lt_accent", "color": "<brand>", "box": [0, 40, 8, 92] }` before `lt_name`.
```json
"comps": { "lower_third": { "width": 760, "height": 150, "duration": 5, "layers": [
  { "type": "solid", "name": "lt_bar", "color": "#101418", "box": [0, 40, 620, 92], "opacity": 0.92 },
  { "type": "text", "name": "lt_name",  "text": "Jane Cooper", "font": "./font-bold.ttf", "size": 40, "color": "#ffffff", "box": [28, 48, 580, 46], "align": "left middle" },
  { "type": "text", "name": "lt_title", "text": "Head of Design", "font": "./font.ttf", "size": 26, "color": "#9aa3bf", "box": [28, 92, 580, 36], "align": "left middle" }
] } }
```
Instance (note `position` is a **delta from the box**, not absolute — rise 60px → settle): `{ "type":"comp","comp":"lower_third","name":"lt_inst","box":[120,530,760,150],"start":1,"duration":4,"animate":{"position":[{"t":0,"v":[0,60],"ease":"outCubic"},{"t":0.5,"v":[0,0]}],"opacity":[{"t":0,"v":0},{"t":0.4,"v":1},{"t":3.6,"v":1},{"t":4,"v":0}]} }`

### extruded-title
3D type faked as 11 z-stepped text copies (darker to the rear) under the scene camera —
the engine has no extruded text; this stack, measured, reads as real depth once the camera
moves. **Needs a `camera` layer in the scene** (park it at `-focal` for true size —
[format.md](format.md)) and a push or lateral drift so the depth shows. ⚠ The measured
trap this block encodes: **anchor is 2D — depth goes on `position` z**; a 3-element anchor
silently drops z and the stack renders flat. Tune text, colours and `size` after adding;
more copies with a smaller step kill the banding on the letter returns.
```bash
strata add extruded-title scene.json
```

### stat-card
A big number + label on a card. Animate the number with a count-up (see recipes). **Ships without the short accent rule** between label and value — it separates nothing ([anti-slop.md](anti-slop.md)); hierarchy comes from the 120/28 size ratio. Brand wants one: `{ "type": "solid", "name": "sc_rule", "color": "<brand>", "box": [40, 96, 64, 6] }` after `sc_bg`.
```json
"comps": { "stat_card": { "width": 420, "height": 300, "duration": 5, "layers": [
  { "type": "solid", "name": "sc_bg", "color": "#0e1230", "box": [0, 0, 420, 300] },
  { "type": "text", "name": "sc_value", "text": "98%", "font": "./font-bold.ttf", "size": 120, "color": "#ffffff", "box": [40, 110, 340, 130], "align": "left middle" },
  { "type": "text", "name": "sc_label", "text": "Customer satisfaction", "font": "./font.ttf", "size": 28, "color": "#9aa3bf", "box": [40, 40, 340, 40], "align": "left middle" }
] } }
```

### end-card (CTA)
Logo/title + CTA for the final hold.
```json
"comps": { "end_card": { "width": 1280, "height": 720, "duration": 4, "layers": [
  { "type": "solid", "name": "ec_bg", "color": "#0e1230", "box": [0, 0, 1280, 720] },
  { "type": "text", "name": "ec_title", "text": "Acme", "font": "./font-bold.ttf", "size": 140, "color": "#ffffff", "box": [0, 240, 1280, 170], "align": "center middle" },
  { "type": "text", "name": "ec_cta", "text": "Get started today", "font": "./font.ttf", "size": 44, "color": "#4cc9f0", "box": [0, 430, 1280, 70], "align": "center middle" }
] } }
```

### logo-sting
A logo image that scales/fades in over a solid; hold then settle.
```json
"comps": { "logo_sting": { "width": 1280, "height": 720, "duration": 3, "layers": [
  { "type": "solid", "name": "ls_bg", "color": "#ffffff", "box": [0, 0, 1280, 720] },
  { "type": "image", "name": "ls_logo", "src": "./logo.png", "box": [440, 260, 400, 200], "fit": "fit", "anchor": [640, 360],
    "animate": { "scale": [ {"t":0,"v":0.7,"ease":"outBack"},{"t":0.6,"v":1} ], "opacity": [ {"t":0,"v":0},{"t":0.3,"v":1} ] } }
] } }
```

### device-frame
A phone/screen frame to hold a product shot (image or video) — colour panels + a media slot. The panels are a **placeholder for the device, not the device**: a phone the viewer is meant to see is a generated image keyed to alpha, with this block's media slot as its screen ([assets.md](assets.md), *Drawn or generated?*). Use the bare panels only for an abstract or diagrammatic frame.
```json
"comps": { "device_frame": { "width": 520, "height": 900, "duration": 6, "layers": [
  { "type": "solid", "name": "df_body", "color": "#111418", "box": [0, 0, 520, 900] },
  { "type": "solid", "name": "df_screen", "color": "#000000", "box": [24, 60, 472, 780] },
  { "type": "image", "name": "df_shot", "src": "./screen.png", "box": [24, 60, 472, 780], "fit": "fill" }
] } }
```
(Swap `df_shot` to a `video` layer for a live screen recording; `loop:false` and size the scene to the clip — don't loop.)

### search-bar
A rounded input bar with a caret (good for "type a query" shots).
```json
"comps": { "search_bar": { "width": 900, "height": 96, "duration": 6, "layers": [
  { "type": "solid", "name": "sb_pill", "color": "#ffffff", "box": [0, 0, 900, 96], "effects": [ {"type":"shadow","color":"#00000028","distance":6,"size":20} ] },
  { "type": "text", "name": "sb_query", "text": "how do I start?", "font": "./font.ttf", "size": 42, "color": "#202124", "box": [40, 0, 760, 96], "align": "left middle" }
] } }
```

### quote-card
A pull-quote with attribution. The quote uses `"left middle"` in a box sized to its two
lines — **never `"… top"`**: vertical `top` is ignored and the text drops to the box bottom
(verified by render; see format.md).
```json
"comps": { "quote_card": { "width": 1000, "height": 420, "duration": 5, "layers": [
  { "type": "solid", "name": "qc_bg", "color": "#0e1230", "box": [0, 0, 1000, 420] },
  { "type": "text", "name": "qc_quote", "text": "“This changed how we ship.”", "font": "./font-bold.ttf", "size": 56, "color": "#ffffff", "box": [60, 70, 880, 160], "align": "left middle" },
  { "type": "text", "name": "qc_attr", "text": "— A. Customer, Acme", "font": "./font.ttf", "size": 30, "color": "#9aa3bf", "box": [60, 320, 880, 44], "align": "left middle" }
] } }
```

## Using a block well
- Give the instance a deliberate **entrance** (rise+fade, scale-in) and **exit**; hold long enough to read ([craft.md](craft.md), "Rhythm & continuity").
- Reskin to brand: swap colours, fonts, and the accent. Keep ≤2 typefaces.
- Stack blocks for layouts (a `device-frame` beside a `stat-card`); see [blueprints.md](blueprints.md) for whole-video assemblies.
