# Sub-compositions and the camera

Nesting a comp inside a comp — the only way to group, clip or parent — and the 3D camera, which is an exact pinhole.

Part of the scene-format reference — the index, and every other part, is in [format.md](../format.md).

## Sub-compositions

Define under scene `comps`, instantiate with a comp layer; reuse freely:

```json
"layers": [ { "type": "comp", "comp": "card", "box": [340,160,600,400],
              "animate": { "rotation": [{"t":0,"v":-8},{"t":3,"v":8,"ease":"inOutSine"}] } } ],
"comps":  { "card": { "width": 600, "height": 400, "duration": 3, "layers": [ ... ] } }
```

If a sub-comp contains a comp layer referencing another sub-comp, declare the referenced one **earlier** in `comps`.

**A sub-comp IS a group — that is how parenting works here.** There is no `parent` property on a
layer: the comp layer is the handle, and its `position`, `scale`, `rotation`, `anchor`, `opacity`
and timing carry everything inside. So "these layers move as one" means putting them in a comp and
animating the comp layer **once**; a null object is that comp layer, and it needs nothing else to
exist. *Measured by render:* a four-part callout (panel, rule, two text layers) inside one
`300×90` comp, with a single `position` animation on the comp layer, travelled 440 px right and
180 px up with every part still in register — one animation, not four.

Two consequences worth stating: **hand-syncing the same keyframes onto several layers is always
the wrong answer** (the copies drift apart on the next revision, and a re-track or a retime means
editing all of them — [traps.md](../traps.md)), and **a per-layer parent chain does not exist** — no
child inheriting only rotation, no parent-of-a-parent. Nest comps instead: a comp inside a comp is
a group inside a group, which covers the cases a chain would.

⚠️ **Set `duration` on EVERY sub-comp.** A comp with no `duration` used to default to **100 frames
(4s at the default 25fps)** regardless of how long the scene is — so in a 10s scene its content simply
**stopped part-way through and the comp went blank**, with no warning and no compile error.
Verified by render. Give each comp the duration it must cover (usually the scene's).
**This is now a compile ERROR** naming the comp, so it cannot reach a render again: the silent
default was the whole defect, and a rule that has to be remembered is not the same as one that
cannot be broken.

💡 **A comp canvas CLIPS its content — that is the only true window.** A `mask` travels with
its layer, so a mask can never act as a fixed window over a moving layer. To reveal part of
something that moves (a rolling odometer strip, a sliding strip of thumbnails), put the moving
layer **inside a comp the size of the window** and place that comp — the comp edge does the
clipping. (A static track-matte layer also works.) ⚠️ **The max 1920/axis applies to sub-comps
too** — a `1920×2800` strip comp is rejected by the schema, so this only works while the moving
strip stays under 1920 px on its long axis. For anything longer use the static track-matte, or
cycle a short strip (reset and re-fill it) instead of building the full length.

⚠️ **Unique layer names — duplicate names across sub-comps used to crash the render (error 3000 — a generic code, [traps.md](../traps.md#error-3000-is-a-generic-exporter-code)).** The exporter keys layers (especially text placeholders) by name **globally**. Two layers sharing a name in different sub-comps — e.g. a `card` sub-comp reused with its text layer named `label` each time — collide, and the render used to fail 3000 (while compiling/validating fine locally). The compiler **auto-uniquifies** duplicates at compile time (`label`→`label_2`, …) and prints what it renamed, so scenes render; still, author distinct, meaningful names so personalization keys stay predictable.

## Camera

```json
{ "type": "camera", "fov": 70, "position": [640,360,-800],
  "animate": { "position": [ {"t":0,"v":[640,360,-800]}, {"t":3,"v":[640,360,-600],"ease":"inOutQuad"} ] } }
```

Only affects layers with `"is_3d": true` (passthrough key). `zoom` is an animatable channel.

⚠️ **`position` on the camera is an ABSOLUTE comp coordinate, and z is NEGATIVE.** For a
1920×1080 comp the camera lives at `[960, 540, -z]` — the comp **centre**, pulled back. A
common wrong guess is `[0,0,z]` (treating it as an offset like a layer's), which parks the
camera at the **top-left corner** and renders the scene black or wildly off-centre.

**Depth goes on `position` z with a 2D `anchor` — a 3-element anchor silently drops the z.**
*Measured (2026-08-24):* eleven text copies given `anchor:[x,y,z]` all rendered at z=0 — a
perfectly flat stack. The same layers with `anchor:[x,y]` and `position:[x,y,z]` rendered
at their depths (the extruded-title block relies on this). The anchor is a 2D pivot; the
third coordinate belongs to the position target.

### The camera is an exact pinhole — here is the calibration (MEASURED)

Rendered 160 px squares at several z, fov and camera distances; every size matched this to
two decimals:

```
focal  = (comp_height / 2) / tan(fov / 2)        # fov is the VERTICAL field of view
scale  = focal / (z_layer - z_camera)            # on-screen size ÷ box size
```

| comp height | fov 60 | fov 70 | fov 90 (schema default) |
|---|---|---|---|
| 720  | focal **623** | 514 | 360 |
| 1080 | focal **935** | 771 | 540 |

Four consequences, each of which the docs used to get wrong:

1. **A layer at `z = 0` renders at its box size ONLY when the camera sits at `z = -focal`.**
   Camera at `[960,540,-1200]` with the default fov 90 (focal 540) renders every z=0 layer at
   **0.45×** — that is why "3D scenes come out small / off-centre". Put the camera at
   `-focal` for your fov, or set `fov` to match the distance you want: `fov = 2·atan(h/2 / d)`.
2. **Negative z is TOWARD the camera and makes a layer BIGGER; positive z is away and
   smaller.** Measured at fov 60, camera −800: z=0 → 0.78×, z=−200 → 1.03×, z=−400 → 1.55×;
   camera −623: z=0 → 1.00×, z=+400 → 0.61×. So a **far background goes at POSITIVE z** and
   must be oversized by `(z_bg − z_cam)/focal`; a foreground element that should loom goes at
   negative z. (Layers at or behind the camera plane are culled.)
3. **Draw order is still the layer order**, not z — a "far" layer listed last still paints on
   top. Keep the stack bottom-first as usual.
4. **The same projection moves a layer's POSITION, not just its size** — the consequence that
   costs a render. A layer is scaled about the camera axis (the comp centre), so its distance
   from that centre scales too: `screen_x = cx + (box_x − cx) · scale`, same for y. Two things
   follow, both measured on real scenes: an off-screen layer at positive z can be **pulled back
   into frame** (a band parked at `y = −1000` at z=+600, camera −935, sits at y ≈ −460 — visible
   for over a second), and a layer at negative z is **pushed outward** and can leave the frame it
   was laid out in. Compute the projected box before trusting a `preview` wireframe, which draws
   the authored box and not the projected one — or `snapshot --at` the moment in question.

A push-in is the camera's z moving toward the layers; the scale of each layer follows the
formula frame by frame, so near layers grow faster than far ones — that differential is the
parallax.

`strata preview` draws 2D boxes only and cannot show any of this. For 3D framing, compute the
size with the formula and confirm with a `snapshot`.

⚠️ **3D layer + `anchor` + `position`.** Setting an `anchor` makes **every** position
keyframe the absolute point where that anchor lands — including in 3D. So keep x,y equal to the
anchor and vary only z (`[anchorX, anchorY, z]`). Writing `[0,0,z]` while an anchor is set drags
the layer's anchor to comp coordinate (0,0) — *verified by render: the layer left the frame
entirely*, while the same layer with `[640,360,-400]` sat centred. Need a plain z-offset
instead? **Omit the anchor**, and then `position: [0,0,z]` is the correct depth offset.
