# What to generate, and at what size

The first two decisions: whether the thing is a generated image or built geometry, and what source resolution the canvas needs.

Part of the asset-generation reference — the index, and every other part, is in [assets.md](../assets.md).

## Source resolution vs canvas — read before choosing the comp size (MEASURED)

| asset | native size |
|---|---|
| `generate image` | **1376×768** (16:9; other aspects the same area) |
| `generate video` / `fastvideo` | **1280×720** — the CLI clamps to 720p |
| `generate avatar` | 1280×720 @ 25 fps |

The default comp is 1920×1080, so a generated plate used **full-bleed** is already upscaled
**1.4–1.5×** before the mandatory push-in adds more. There is no upscaler in the CLI.
Author around it:

- **Full-bleed generated footage → author the comp at 1280×720** (or 720×1280 / 1080×1080
  for 1:1, where a 1080 square from a 1376×768 source is also ~1:1). The cloud render is
  the deliverable size; design at the footage's size and let the platform scale once.
- **In a 1920 comp, keep generated media in a framed slot ≤ its native size** — the split,
  card and mosaic layouts in layouts.md already do this, and it is why they look sharper
  than a full-bleed plate with a caption.
- **Never stack a push-in on an already-upscaled full-bleed plate**: at 1.5× plus a 10 %
  Ken-Burns the frame ends at 1.65×, and softness reads as "AI footage" faster than anything.
- Match `--aspect` / `--ratio` to the **slot**, not the comp — a 9:16 slot in a 16:9 frame
  wants a 9:16 asset, so none of the resolution is cropped away.

## Drawn or generated? — a physical thing is an image, a shape is for simple geometry

`generate image` is a strong model; use it for anything that has a surface in the real world.
**Shapes (`solid`, masks, vector paths) are for simple geometry** — panels, bands, rules, grids,
abstract marks, UI chrome, colour fields — and for nothing a viewer would recognise as an object.

| the element is… | build it as |
|---|---|
| a device (phone, laptop, watch), a product, a prop, a package, a vehicle, food, a plant, anything held or set down | **`generate image`**, keyed to alpha, placed as an `image` layer with layer-level motion (the route below) — never a stack of rounded solids |
| an icon, an illustration, a badge, a sticker, a texture, a material, a photographic backdrop | **`generate image`** — icons and flat graphics may stay stills, the one exception to *every image becomes a video* |
| the UI on a device's screen, a chart, a counter, the copy | **scene layers** (text, `strata chart`, blocks) inside the device's screen rectangle — that is what animates and personalises |
| a panel, a band, a rule, a grid, a shape wipe, an abstract mark, a colour field | **shapes** |

A drawn phone is eleven rounded solids and reads as a diagram; a generated one has bezel, glass,
reflection and weight, and the UI still sits on it as layers. The cost is one wave (~10–20 s per
image, in parallel with the clips), so the choice is never "shapes because generating is slow".

**The cut-out route for a still** — `matte` is people-only, so an object is keyed, not matted:

1. Generate it on a flat chroma-green backdrop, and say so in the prompt:
   `strata generate image "<the thing>, front view, studio product lighting, on a flat solid chroma green background, no shadow on the background" --aspect 3:4 -o ./assets/phone_green.png`
   **Or lift the thing out of a shot you already like** — `strata edit image ./hero.png "the
   smartphone from image 0, exactly as it is, isolated on a flat solid chroma green background, no
   shadow on the background"` (*measured:* it kept the body, the screen content and the lighting and
   returned a flat green plate). That is the move when the object only exists inside an approved
   photograph; generating it fresh rolls a new object.
2. Key it to a PNG with alpha (no `strata` command needed for a still):
   `ffmpeg -i ./assets/phone_green.png -vf "chromakey=0x00B140:0.15:0.05,despill=type=green" -c:v png ./assets/phone.png`
   — `similarity` 0.10–0.20 opens the key, `blend` 0.02–0.08 softens the edge, `despill` removes the green fringe. *Measured* on a generated phone (prompt as above, 896×1200): the model returned a flat, even green with no shadow, and `chromakey=0x00B140:0.15:0.05` gave a matte with alpha 0 across the whole backdrop and 255 across the whole phone — a hard, clean edge, 22.6 % coverage, nothing to choke or feather. Check yours: `ffmpeg -i phone.png -vf alphaextract alpha.png` and look; the matte must be solid inside and clean outside.
   ⚠ **Key the green you actually got, not the green you asked for.** The same "flat solid chroma
   green" prompt does not always land on `0x00B140`: *measured* on the edit above, the plate came
   back at **RGB (16, 243, 1)** — a much purer green — and keying it with `0x00B140:0.15` left
   **~13 % of the backdrop opaque** (52 % opaque instead of the phone's 38.8 %). Sample a corner
   first and key on that: `ffmpeg -i plate.png -vf "crop=8:8:0:0,format=rgb24" -f rawvideo - | xxd | head -1`
   (or read one pixel any way you like), then `chromakey=0x10F301:0.15:0.05` → 38.8 % opaque,
   61.1 % transparent, **0.14 % partial edge**, no fringe over a magenta check.
   ⚠ **Check the SUBJECT's colour, not just the matte — a green plate can tint the object itself,
   and then no key reaches it.** *Measured* on a generated matte-white camera on chroma green
   (896×1200, plate sampled at RGB (10, 206, 0)): the body interior, more than 10 px from any matte
   edge, reads **B−G +15** at all five key settings tested *including no key at all*, while the same
   product generated with no plate reads **B−G −22** — a warm white should sit near −15, so the
   plate swung the object 37 points into mauve during GENERATION. Choking cleans the rim and only
   the rim (rim **B−G +23 → +12** and R219 → R243 at `--choke 6`), and despill cannot help by
   construction: in chroma mode it only caps the key channel, so it can lower G but never raise it,
   and mauve IS low G — which is why keying with and without despill gives identical body pixels.
   So compare the body against a non-green generation BEFORE you commit to the cut-out; if the
   interior is already cast, re-generate the object on its real surface and crop to the alpha bbox,
   because a global white-balance fix cannot land either (the rim wanted B×0.58 where the centre
   wanted B×0.83).
   ⚠ **`generate image` returns JPEG bytes under a `.png` name** (*measured:* the file begins
   `ff d8 ff e0`), so 4:2:0 chroma subsampling has already averaged plate colour into the subject's
   edge chroma before you open the file. Edge contamination is therefore choked away, never
   colour-corrected — and it is why `strata jet --key <r,g,b> --method chroma --choke N` beats a
   bare `chromakey`, which has no choke at all. Use `jet` when the output is a `.jet`; for a still
   PNG, key with ffmpeg as above and erode if you see a fringe
   (`alphaextract,erosion,alphamerge`).
3. Place it: `{ "type": "image", "src": "./assets/phone.png", "fit": "contain", … }` and give it life at the **layer level** — a ≈1 px/frame drift, parallax against the plate, a slow scale ([craft.md](../craft.md), *A held shot still moves*); its screen rectangle holds the UI layers. A `.jet` is needed only when the object itself must move as footage — then generate a clip on green and `strata jet --key` it (the table under *Alpha overlays*).
