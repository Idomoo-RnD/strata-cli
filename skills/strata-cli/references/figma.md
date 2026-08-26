# Figma → strata — importing designs accurately

A Figma frame is a **static, web-sized, absolutely-positioned tree**. A strata scene is a
**video-sized, timed, name-keyed document**. Almost every import bug comes from that
mismatch. Work one frame at a time and go in this order.

What to pull from Figma (via the Figma MCP — exact tool names vary by server):
- the **node tree / metadata** for the selected frame (names, types, absolute bounding
  boxes, fills, text styles, effects, visibility),
- a **screenshot/image** of the frame — this is the ground truth you verify against,
- **variables / design tokens** (palette, type scale) — reuse them as the scene's system.

---

## Contents

- [1. Decide the canvas FIRST (before converting anything)](#1-decide-the-canvas-first-before-converting-anything)
- [2. Convert coordinates](#2-convert-coordinates)
- [3. Node → layer mapping](#3-node--layer-mapping)
- [4. Text — the most error-prone mapping](#4-text--the-most-error-prone-mapping)
- [5. Colour & opacity](#5-colour--opacity)
- [6. Images & vectors must be exported](#6-images--vectors-must-be-exported)
- [7. Z-order](#7-z-order)
- [8. Names — this one breaks renders](#8-names--this-one-breaks-renders)
- [9. Effects](#9-effects)
- [10. Reuse the design tokens](#10-reuse-the-design-tokens)
- [11. Verify against the source — the loop that makes this accurate](#11-verify-against-the-source--the-loop-that-makes-this-accurate)
- [12. A faithful import is still a slideshow — add motion](#12-a-faithful-import-is-still-a-slideshow--add-motion)

## 1. Decide the canvas FIRST (before converting anything)
Figma designs are usually web-sized (1440×1024, 375×812); video is 1920×1080 or 1080×1920.

- **Same aspect →** uniform **scale-to-fit**. One factor `S = videoWidth / figmaFrameWidth`,
  applied to *everything*: x, y, w, h, font size, corner radius, stroke, effect radius/offset.
- **Different aspect →** do **not** stretch. Re-compose onto the strata grid
  ([layouts.md](layouts.md)) keeping the design's palette, type scale, and hierarchy.
  Non-uniform x/y scaling (squashed type, oval circles) is the giveaway of a bad import.

**Never scale x and y by different factors.**

## 2. Convert coordinates
Figma boxes (`absoluteBoundingBox`) are **canvas-absolute**; strata boxes are
**frame-relative**. Subtract the root frame's origin, then scale:

```
x' = round((node.x - frame.x) * S)
y' = round((node.y - frame.y) * S)
w' = round(node.width  * S)
h' = round(node.height * S)
box = [x', y', w', h']
```
Forgetting the origin subtraction is the single most common import bug — everything lands
offset by the frame's canvas position.

## 3. Node → layer mapping
| Figma node | strata layer |
|---|---|
| `FRAME` / `GROUP` / `COMPONENT` used as a container | a **sub-comp** in `comps` + a `comp` instance layer (box = the group's box) — **a comp IS strata's group** (§3½). Flatten only trivial wrappers. |
| `RECTANGLE` / `ELLIPSE` with a solid fill | `solid` (ellipse/rounded → add a `mask`) |
| `TEXT` | `text` |
| `RECTANGLE` with an **image** fill | `image` — export the fill (§6) |
| `VECTOR` / `BOOLEAN_OPERATION` / `STAR` / `LINE` / icons | **export to PNG** → `image` (no vector primitive in strata) |
| `INSTANCE` (repeated component) | like a frame — and **rename uniquely** (§8) |
| any node with `visible: false` | **skip it** |

### 3½. Groups ARE comps — the structural mapping
A Figma **group (or frame/component used as a container) maps to a strata comp; a comp
is strata's "group".** Don't flatten the tree by default — mirror it:

```json
"comps": { "stat_card": { "width": 320, "height": 180, "layers": [
    { "type": "solid", "name": "card_bg",    "color": "#1c2030", "box": [0, 0, 320, 180] },
    { "type": "text",  "name": "card_value", "text": "2.4M", "font": "./bold.ttf",
      "size": 48, "box": [24, 40, 272, 60] } ] } },
"layers": [
  { "type": "comp", "comp": "stat_card", "name": "card1", "box": [120, 470, 320, 180] } ]
```

- **Children's boxes are relative to the GROUP's origin,** not the root frame's: subtract
  the *group's* x/y from each child (then scale). The comp instance's own `box` carries the
  group's position in the frame. Subtracting only the root origin — so children double-count
  the group offset — is the classic mistake.
- **Why it matters:** the comp instance animates as ONE layer — move/scale/fade the whole
  group exactly like dragging a Figma group; effects and masks apply to the whole unit.
- **Repeated `INSTANCE`s → one comp, many instance layers** (each with a unique name and
  its own box) — mirroring Figma's component/instance model.
- **Flatten only** trivial organizational groups (a group holding one element, or pure
  layer-panel tidying with no shared motion/effect) — needless nesting hides layers from
  personalization keys.

## 4. Text — the most error-prone mapping
| Figma | strata |
|---|---|
| `characters` | `text` |
| `style.fontFamily` + weight | **a real `.ttf`/`.otf` file path** — never the family name |
| `style.fontSize * S` | `size` |
| `style.textAlignHorizontal` + `…Vertical` | `align` (`"left middle"`, `"center top"`, …) |
| `style.lineHeightPx / style.fontSize` | `leading` (ratio) |
| `style.letterSpacing` | `tracking` |
| `fills[0].color` | `color` (hex, §5) |

- `"font": "Inter"` **will not compile.** Keep an explicit map from Figma family+weight →
  local font file, and confirm the file covers every glyph used (non-ASCII, currency,
  quotes, emoji) — missing glyphs crash the export.
- Figma's text metrics ≠ the IDM engine's. **Don't rely on pixel-exact text width.** Give
  the box slack (or keep `shrink`) so a slightly wider render doesn't wrap or shrink oddly.
- Auto-width text (`textAutoResize: WIDTH_AND_HEIGHT`) has a box that hugs the glyphs —
  widen it and let `align` do the positioning.

## 5. Colour & opacity
- Figma fills are `{r,g,b,a}` as **0–1 floats** → `#` + `round(c*255)` per channel.
- Node `opacity` → the layer's `opacity`; a fill's own `opacity` multiplies it.
- **Gradients have no strata primitive** — export the gradient as an image, or approximate
  with a colour-overlay effect.

## 6. Images & vectors must be exported
strata's encoder reads assets **from disk**, so every image fill, icon and vector has to be
exported from Figma to a local **PNG/JPG** and referenced by path. Export at **≥ the final
pixel size** (2× is safe) or it renders soft. Idomoo accepts `mp4/jpg/png` media and
`ttf/otf` fonts only.

## 7. Z-order
Figma lists children **bottom-first** (index 0 = furthest back), and strata renders array
order with later layers on top — so the order usually carries over 1:1. Verify it in the
preview; if the stack looks inverted, reverse it.

## 8. Names — this one breaks renders
Figma trees are full of `Rectangle 1`, `Text`, `Frame 2`, and repeated component instances.
strata requires **globally unique layer names**: the exporter keys layers by name, and
duplicates crash the render (error 3000). The compiler auto-uniquifies as a safety net, but
then personalization keys become unpredictable.

**On import, rename everything meaningfully and uniquely** — prefix by section:
`hero_title`, `card1_value`, `card1_label`, `card2_value`, `cta_bg`. Never keep the raw
Figma names.

## 9. Effects
| Figma | strata |
|---|---|
| `DROP_SHADOW` | `shadow` — offset from `offset.x/y * S`, `size` from `radius * S`, colour+alpha |
| `LAYER_BLUR` | `blur` (`radius * S`) |
| soft glow | `glow`, or a `shadow` with zero offset |
| `BACKGROUND_BLUR` | no direct equivalent — bake it into an exported image |

## 10. Reuse the design tokens
Pull Figma **variables/styles** and use them as the scene's palette and type scale — and
pass the same hexes to `strata generate image --colors "#a,#b"` so generated art matches the
design system instead of drifting from it.

## 11. Verify against the source — the loop that makes this accurate
```bash
strata preview scene.json --at <t> --grid     # free local wireframe
strata validate scene.json                    # dup names, overlaps, off-canvas, glyphs
```
Put the **preview next to the Figma screenshot** and check, in order:
1. **Origin & scale** — does the whole composition sit in the same place at the same size?
2. **Proportions** — relative sizes/gaps match (catches non-uniform scaling)?
3. **Alignment** — shared edges still share edges?
4. **Z-order** — nothing hidden behind the wrong layer?
5. **Completeness** — nothing missing (skipped vectors?) and nothing imported that was
   `visible: false`?

Fix drift here — it's free. Only then `snapshot` / `render`.

## 12. A faithful import is still a slideshow — add motion
Importing the frame gives you **the layout, not the video**. Author motion on top: staggered
entrances (`position` delta + `opacity`, 300–500ms), reveals ordered by hierarchy, Ken-Burns
on stills, masks for wipes, a held CTA. See [craft.md](craft.md) Part 1 and [recipes.md](recipes.md).

---

### Import checklist
- [ ] Canvas decided: scale-to-fit (same aspect) or re-composed (different aspect)
- [ ] One uniform `S`; origin subtracted from every box
- [ ] Groups mirrored as comps; children's boxes relative to their GROUP's origin
- [ ] Fonts mapped to real `.ttf`/`.otf` paths; glyphs covered
- [ ] Images/vectors exported to local PNG at ≥ final size
- [ ] Every layer renamed **unique** and meaningful
- [ ] `visible:false` nodes skipped; z-order confirmed
- [ ] `preview --grid` compared against the Figma screenshot; `validate` clean
- [ ] Motion authored — it isn't a video until it moves
