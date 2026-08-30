# Compact scene format — full reference

Compiled by `strata compile <scene.json>` into VASCO, schema-validated, then encoded to `.idm`.

## Contents

- [Scene](#scene)
  - [Version control — automatic, two layers *(measured 2026-08-24)*](#version-control--automatic-two-layers-measured-2026-08-24)
- [Layers (common)](#layers-common)
- [Text](#text)
  - [Right-to-left scripts (Hebrew, Arabic) — they already work; the flag does not](#right-to-left-scripts-hebrew-arabic--they-already-work-the-flag-does-not)
- [Image / Video (media)](#image--video-media)
- [Solid](#solid)
- [Audio](#audio)
- [Sub-compositions](#sub-compositions)
- [Camera](#camera)
  - [The camera is an exact pinhole — here is the calibration (MEASURED)](#the-camera-is-an-exact-pinhole--here-is-the-calibration-measured)
- [Tween engine (`animate`)](#tween-engine-animate)
- [Effects](#effects)
- [Masks](#masks)
- [Track mattes](#track-mattes)
- [Colors](#colors)
- [Generating assets (Idomoo AI API)](#generating-assets-idomoo-ai-api)
- [Personalization — design for replaceable elements](#personalization--design-for-replaceable-elements)
- [Graphs & charts — dynamic images](#graphs--charts--dynamic-images)
- [Unpacking and repacking an existing `.idm`](#unpacking-and-repacking-an-existing-idm)
  - [⛔ Two assets with IDENTICAL BYTES crash the exporter (error 3000)](#two-assets-with-identical-bytes-crash-the-exporter-error-3000)
  - [⚠️ The encoder stores only an asset's BASENAME](#the-encoder-stores-only-an-assets-basename)
- [Raw VASCO passthrough](#raw-vasco-passthrough)


## Scene

```json
{
  "width": 1280, "height": 720,      // max 1920 each
  "fps": 25,                          // 1..120, default 25
  "duration": 4,                      // seconds → num_of_frames (or set "num_of_frames")
  "name": "main",
  "layers": [ ... ],                  // bottom-first: first layer is the background
  "comps": { "card": { /* same shape as scene */ } }   // optional sub-compositions
}
```

Passthrough at comp level: `shutter_angle` (0–2, default 0.5 ≈ 180°), `shutter_phase`. (`transition {start,end}` also exists on a comp; its semantics are undocumented and tagging auto-stamps it — do not set it by hand.)

**File naming — the scene's FILENAME is its identity in the cloud.** `render`/`snapshot`
upload under `basename(<scene file>)` and derive the output MP4/JPG name from it, so the
filename is what the user browses in their Idomoo library. Give every new cut its own
versioned file (`promo_v1.json`, `promo_v2.json`, `promo_final.json`) instead of
overwriting — otherwise the library fills with indistinguishable entries and each render
clobbers the last MP4. (`name` above is the *comp* name, internal — it does not label the
upload.)

### Version control — automatic, two layers *(measured 2026-08-24)*

**1. Folder history.** Every `compile`/`render` snapshots the scene JSON into
`.strata/versions/vNNN/` beside it, with an index (time, scene hash, render URL once one
exists). Identical content never makes a new version — the hash is over the *canonical*
JSON, so whitespace churn is not an edit. `strata versions <scene>` lists them;
`strata revert <scene> --to N` restores one (the current state is snapshotted first, so
revert can never lose anything; `-o other.json` branches instead of replacing).

**2. Embedded stamp.** Every compiled `.idm` carries a `strata.meta.ttf` asset — the same
disguise as the tag manifest — holding `{version, parent, created, scene_hash, tool}`.
*Measured:* it survives the Idomoo exporter (uploads and renders normally), round-trips
through `idm2vasco`, and `strata inspect` prints it — so a bare `.idm` received from anyone
identifies its own version and ancestry. The filename rule above remains the **library's**
identity (Idomoo shows the filename); the stamp is the **file's** identity wherever it
travels. Two `.idm`s with the same `scene_hash` are the same cut regardless of their
numbers — numbering is per-folder.


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

## Text

```json
{ "type": "text", "text": "Hello", "font": "./arial.ttf", "size": 96,
  "color": "#ffffff", "box": [0,0,1280,200], "align": "center middle",
  "tracking": 0, "leading": 1.2, "breakline": false, "shrink": true, "min_size": 0,
  "rtl": false, "ellipsis": "…" }
```

- `font` (required): path to .ttf/.otf — deduped into the asset table.
- ⚠️ **The font MUST contain a glyph for every character in `text` (and every `styles` span).** The IDM only embeds the glyphs the font actually has — a character the font is missing renders as a blank/tofu box or **crashes the cloud render (error 3000)**. `validate`/`compile` now **auto-check coverage** and ⚠ name the exact missing characters (incl. emoji), so I fix them before rendering. **Verify glyph coverage before the final compile**, especially for anything beyond plain A–Z/0–9: accented/non-Latin letters (é ñ ü 你好 العربية), currency (€ £ ₪ ₹), punctuation people paste in (curly quotes “ ” ‘ ’, en/em dashes – —, ellipsis …), symbols (™ © ® • → ✓ ★), and emoji. Many basic fonts (Arial, and even the bundled DejaVuSans for non-Latin scripts) lack these. Options, best first: (1) pick/generate a font that covers the script — e.g. a Noto family for the target language; (2) for a styled span, point that span's `font` at a font that has the glyph; (3) substitute an ASCII equivalent the font has (`->` for →, straight `"` for “”, `...` for …). When in doubt, run the glyph check below.
- **Check coverage with the CLI** — no snippet needed:
  ```bash
  strata glyphs ./font.ttf "Your exact text — €49, “smart”, → ✓"   # one font vs some copy
  strata glyphs scene.json                                          # every text layer vs its own font
  ```
  `✅ covers all N character(s)` → safe. Otherwise it names each missing character with its
  codepoint and **exits 1**, so it can gate a build. Use it *before* authoring, to pick a font
  that can carry the copy; `validate`/`compile` then re-check every layer automatically, so a
  missing glyph cannot reach a render unnoticed.
- `align`: words from `left center right` + `top middle baseline bottom`, e.g. `"center middle"`, or `{"h": "center", "v": "top"}`.

> ### ⚠️ Vertical text sits at the BOTTOM of its box — `"top"` does nothing (VERIFIED)
> Rendered test, box `y 80 → 340` (260px tall), `size: 60`:
>
> | `align` | where the glyphs landed |
> |---|---|
> | `left top` | **y 298–339** — 1px off the box bottom |
> | `left bottom` | y 298–339 — **identical to `top`** |
> | `left middle` | y 190–231 — correctly centred (110/109px gaps) |
>
> **`middle` is the only vertical value that repositions anything.** A tall box with
> `"… top"` silently drops the text to the bottom, so:
> - **Anchor vertical maths on `box_y + box_h`, never `box_y + size`.** Stacking rows by
>   adding the font size to the box top puts every row in the wrong place — the classic
>   symptom is rows that look right in one layout and collapse in another.
> - **Size the box to the copy** (~`size × 1.3` per line) and position it by its **bottom**
>   edge; or use `"middle"` with the box centred on where the text should be.
> - A box exactly as tall as the font size **overflows ~22% below** (descenders): `size: 80`
>   in an 80px box drew to 18px past the bottom edge. Give a single line ~`1.3 × size`.
> - **`strata preview` cannot show this** — it draws layer *boxes*, not glyphs. Confirm text
>   placement with `strata snapshot` (poster only, cheap) before a full render.
>
> `validate` now warns when a text layer combines a `top` alignment with a box much taller
> than its type.
- **Rich spans (typography)** — `"styles"` is a list of per-character-range overrides, each `{ "start": <char index>, "length": <chars>, ... }`. **Confirmed to render:** `color` (hex), `size`, `tracking`, `leading`, `shift`, and a per-span `font` (`font` optional — defaults to the layer font). Example:
  ```json
  "text": "Rich VASCO Text",
  "styles": [
    { "start": 0,  "length": 5, "color": "#ff5a5f", "font": "./Bold.ttf" },
    { "start": 5,  "length": 6, "color": "#ffd166", "tracking": 6 },
    { "start": 11, "length": 4, "color": "#4cc9f0" }
  ]
  ```
  - ⚠️ **Spans MUST cover every character contiguously — including the spaces.** The renderer drops any character not covered by a span, so a gap (e.g. a space left out between two spans) **disappears** and words jam together ("Rich VASCO" → "RichVASCO"). Always extend each span to include its trailing space, or chain spans edge-to-edge so `start[n] = start[n-1] + length[n-1]` with no holes.
  - **Bold / italic:** point the span's `font` at a real **bold/italic font file** (e.g. `./Inter-Bold.ttf`). The boolean `bold`/`italic` flags do **not** synthesize a weight/slant in the renderer — they are no-ops without a variant font.
  - **`underline` / `strikethrough` / `highlight` do NOT render** in the current engine — don't rely on them. For an underline, draw a thin `solid` bar under the text; for highlight, place a `solid` (or a rounded image) behind the text layer. **And ask whether the title needs one at all** — a rule under a lone heading separates nothing and is one of the commonest agent tells; prefer weight, size or colour ([anti-slop.md](anti-slop.md)). Draw it when the brief asks.
  - **Non-ASCII spans — handled automatically.** `start`/`length` are authored in **characters**; the exporter indexes by **UTF-8 byte**, so multi-byte chars (`×`, `€`, `–`, accents, CJK, emoji) used to crash export. The compiler now converts span offsets to byte offsets at compile time, so styled non-ASCII text renders correctly — no workaround needed.
- **Per-character animators** (After-Effects-style): `"animators": [...]` — raw VASCO `IdmTextAnimator` objects, but `color` accepts hex and any object may carry `animate`. Example, words fading in one by one:

```json
"animators": [{
  "opacity": 0, "position": [0, 40, 0],
  "ranges": [{ "based_on": "words", "shape": "ramp_up",
    "animate": { "start": [{"t":0,"v":0},{"t":2,"v":1,"ease":"outQuad"}],
                 "end":   [{"t":0,"v":0.25},{"t":2,"v":1.25}] } }]
}]
```

Animator offsets (`opacity`, `position`, `scale`, `rotation`, `color`, `tracking`, `skew`, **`character_offset`** — shift digits/letters by N, wraps mod 10 for digits, negative = glyph vanishes; **`character_value`** — replace with a codepoint, needs `character_range: full_unicode`; both verified, see recipes.md "Count-up") apply to the characters selected by `ranges`; animate the range `start`/`end`/`offset` to sweep the selection. Range options: `based_on` (`characters` `characters_excluding_spaces` `words` `lines`), `mode`, `shape` (`square ramp_up ramp_down triangle round smooth`), `units`, `randomize_order`.

⚠️ **`shape` and the string edges:** `square` has hard edges; the others (`ramp_up`/`ramp_down`/`triangle`/`round`/`smooth`) **taper** the selection. A tapered shape whose window edge lands at `0` or `1` leaves the first/last unit **partially** selected — so a "hidden at t=0, reveal in order" reveal (opacity 0, `start` 0→1, `end` pinned at 1) breaks with `smooth`/`round`/`triangle`: the last unit does visible→gone→visible and the first never animates in (widening the window doesn't fix it; the taper scales with width). **Use `square` for ordered reveals; keep tapered shapes for continuous effects with the window kept off the string ends** (see recipes.md §1).

### Right-to-left scripts (Hebrew, Arabic) — they already work; the flag does not

**Hebrew and Arabic render correctly with NO special setting.** *Measured by render:* `אבג`
came back with alef **rightmost**; `מחיר 250 שח` put `מחיר` rightmost with `250` still reading
left-to-right inside it — correct bidirectional layout, done by the engine automatically.

⚠️ **`rtl: true` is a no-op in every case tested.** Same string with the flag on and off gave
**pixel-identical** output (identical ink spans, 0.4–0.5% column-profile delta = JPEG noise)
for a single Hebrew line, a mixed Hebrew+Latin line, and a long auto-fitted string. It is in
the schema and it compiles, so it is harmless — but **it fixes nothing.** If RTL copy looks
wrong, the cause is one of the things below, never a missing flag.

What actually matters for an RTL scene:

- **Glyph coverage first.** The usual failure is tofu boxes or a cloud-render crash because the
  font has no Hebrew/Arabic glyphs — not direction. `strata glyphs ./font.ttf "<the copy>"`
  before rendering; a Noto family covers what Arial may not (Arial does carry Hebrew).
- **Alignment is yours to set.** The engine lays the script out correctly but does not move the
  text block — RTL copy normally wants `align: "right …"`, and a right-aligned column of copy
  wants its box's **right** edge on the layout grid line, not its left.
- **Numbers, prices and Latin brand names stay LTR inside RTL copy** — that is correct, not a
  bug. Do not "fix" it by reversing the string in the source; a reversed source renders
  reversed.
- **Never pre-reverse text to make it look right.** If something is backwards, the string
  itself is backwards. Store logical order and let the engine lay it out.
- **Punctuation at a line's end** (`!`, `?`, `.`) sits on the **left** in RTL. Expected.

**For personalized RTL templates**, see [personalization.md](personalization.md): a Hebrew name
substituted into a template built with English samples changes the visual weight and the side
the line grows from, so prove the layout against a long RTL value, not just the English one.

## Image / Video (media)

```json
{ "type": "image", "src": "./photo.jpg", "box": [0,0,1280,720], "fit": "fill" }
{ "type": "video", "src": "./clip.mp4", "loop": true, "offset_frame": 0 }
```

`loop: true|false` → `playback_mode` loop/cut (or pass `playback_mode`: `cut loop hold`). Extensions sniffed: png/jpg/jpeg/webp/bmp/gif/tif → image; mp4/mov/avi/webm/mkv/m4v → video.

**Don't loop a clip to fill time.** A clip that restarts mid-scene reads as a glitchy GIF — the visible jump back to frame one looks cheap. Prefer to size the scene to the clip, or when one clip ends **cut/transition to a different shot** rather than replaying the same one. Use `playback_mode: "hold"` to freeze on the last frame instead of looping, and reserve `loop: true` for genuinely seamless ambient textures (subtle particles, gradients).

## Solid

```json
{ "type": "solid", "color": "#10204a", "box": [0,500,1280,140], "opacity": 0.6 }
```

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

## Sub-compositions

Define under scene `comps`, instantiate with a comp layer; reuse freely:

```json
"layers": [ { "type": "comp", "comp": "card", "box": [340,160,600,400],
              "animate": { "rotation": [{"t":0,"v":-8},{"t":3,"v":8,"ease":"inOutSine"}] } } ],
"comps":  { "card": { "width": 600, "height": 400, "duration": 3, "layers": [ ... ] } }
```

If a sub-comp contains a comp layer referencing another sub-comp, declare the referenced one **earlier** in `comps`.

⚠️ **Set `duration` on EVERY sub-comp.** A comp with no `duration` defaults to **100 frames
(~4.2s at 24fps)** regardless of how long the scene is — so in a 10s scene its content simply
**stops part-way through and the comp goes blank**, with no warning and no compile error.
Verified by render. Give each comp the duration it must cover (usually the scene's).

💡 **A comp canvas CLIPS its content — that is the only true window.** A `mask` travels with
its layer, so a mask can never act as a fixed window over a moving layer. To reveal part of
something that moves (a rolling odometer strip, a sliding strip of thumbnails), put the moving
layer **inside a comp the size of the window** and place that comp — the comp edge does the
clipping. (A static track-matte layer also works.) ⚠️ **The max 1920/axis applies to sub-comps
too** — a `1920×2800` strip comp is rejected by the schema, so this only works while the moving
strip stays under 1920 px on its long axis. For anything longer use the static track-matte, or
cycle a short strip (reset and re-fill it) instead of building the full length.

⚠️ **Unique layer names — duplicate names across sub-comps crash the render (error 3000).** The exporter keys layers (especially text placeholders) by name **globally**. Two layers sharing a name in different sub-comps — e.g. a `card` sub-comp reused with its text layer named `label` each time — collide and the render fails 3000 (it compiles/validates fine locally). The compiler **auto-uniquifies** duplicates at compile time (`label`→`label_2`, …) and prints what it renamed, so scenes render; still, author distinct, meaningful names so personalization keys stay predictable.

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

Three consequences, each of which the docs used to get wrong:

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

## Tween engine (`animate`)

Each channel is a keyframe array; the CLI bakes per-frame values at comp fps over the layer's duration.

```json
"animate": { "<channel>": [ { "t": 0.5, "v": <value>, "ease": "outCubic" }, ... ] }
```

- `t` = seconds (or `f` = frames), **relative to the layer's start**.
- `ease` shapes the segment *leaving* that keyframe; if omitted, the next keyframe's ease applies; else linear. `hold` freezes until the next keyframe.
- Easings: `linear`, `hold`, `in|out|inOut` + `Quad Cubic Quart Quint Sine Expo Circ Back Elastic Bounce` (any of `outCubic` / `ease-out-cubic` / `easeOutCubic` spellings), or cubic-bezier `[x1,y1,x2,y2]`.
- Before the first / after the last keyframe the value clamps.

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
"mask": { "rect": [0, 500, 1280, 140], "feather": 12 }                  // single shape
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

Shapes: `rect [x,y,w,h]` (+ optional `radius`: a number, or `[tl,tr,br,bl]`; each clamps to half the shorter side, so a big number gives a pill — this is how rounded cards, pills and CTA buttons are made) · `ellipse [cx,cy,rx,ry]` · `path [[x,y],...]` (`closed` defaults true) · `shape` = raw VASCO commands (`move_to`/`line_to` 2 values, `quadratic_to` 4, `cubic_to` 6). Mask blend modes: `none add subtract intersect lighten darken difference`. Shape keyframes interpolate (morph) when both ends have the same structure.

## Track mattes

The matte source must be a layer (usually `"visible": false`) in the same comp; reference it by name:

```json
{ "type": "text", "name": "matte-text", "text": "MATTE", "visible": false, ... },
{ "type": "image", "src": "./photo.jpg", "matte": { "type": "alpha", "source": "matte-text" } }
```

Types: `alpha alpha_inverted luma luma_inverted`.

## Colors

Hex anywhere a color is expected: `#rgb`, `#rrggbb`, `#rrggbbaa`. Layer/text-style colors are RGB (alpha dropped); effect and animator colors keep alpha. Raw `[r,g,b(,a)]` arrays (0..1 floats) also accepted.

## Generating assets (Idomoo AI API)

The CLI generates the media a scene needs — `strata generate image|video|fastvideo|avatar|narration|music`,
plus `voices`. **The full reference is [assets.md](assets.md)** (flags, reference images, the
upload rule, `.jet`); **prompting `generate video` is [video-generation.md](video-generation.md)**.
Two facts worth having here, next to the scene syntax:

- The saved **local path** goes in the scene's `src`; the printed **`url:`** is what you pass to
  any `generate` flag that takes a URL (`--first-frame`, `--ref-*`, avatar inputs). Generated
  assets are already hosted — never re-upload one.
- **Native sizes:** `generate image` returns **1376×768** (16:9), `generate video` is capped at
  **1280×720**, avatar is 1280×720 @ 25 fps. In a 1920×1080 comp a full-bleed generated plate is
  already upscaled ~1.4–1.5× before any push-in — see *Source resolution* in assets.md.

⚠ `strata generate video <image> --prompt "…"` is the **pre-1.0.82** form. The CLI now refuses it
(the image path would otherwise become the prompt). New form: `generate video "<prompt>" --first-frame <url>`.

## Personalization — design for replaceable elements

Idomoo is a **personalized video platform**: every layer is a placeholder (VASCO `placeholder` defaults to true) whose content can be replaced per-viewer at generate time via the API, keyed by **layer name**. Replacement swaps only the content — text value or media asset — while the layer's box, timing, animations, effects, and masks play back exactly as authored. When the user says the video is personal/personalized, author for content that WILL change:

- **Name layers meaningfully** (`first_name`, `hero_photo`, `monthly_amount`) — names are the replacement keys.
- **Text**: assume values longer and shorter than your sample. Give text boxes generous width, keep `shrink: true` (the default) with a sensible `min_size`, choose alignment deliberately (a left-aligned box grows rightward; centered grows both ways), and never split one personal value across multiple hand-positioned layers.
- **Media**: assume any aspect ratio may arrive. Use `fit: "fill"` for full-bleed slots (crops to cover) or `fit: "fit"` over a designed backdrop (letterboxes). Anchor at the box center so zooms/Ken Burns work on any replacement.
- **Animations are content-agnostic**: per-character text animators adapt to any string automatically — prefer `percentage` range units over `index` when the text varies. A 6-character and a 14-character name both cascade correctly.
- Sample values in the scene should be realistic *long* examples, so the layout is proven against the hard case.

## Graphs & charts — dynamic images

Graphs are **images, not drawn primitives**. The data-dynamism comes from swapping the image asset at generate time while the authored animation stays identical:

- Author the graph image at its **canonical/full state** — a bar chart at 100%, a progress ring fully closed, a line chart with the complete curve.
- **Animate the reveal, not the data**: a mask wipe in the direction the graph grows (left→right rect morph for horizontal bars, bottom→top for columns, an expanding ellipse for rings), or a scale/opacity entrance. When the API replaces the asset with a 50%-filled variant of the same graph, the same wipe plays and the viewer simply sees a 50% graph.
- Never try to encode the data in the animation (e.g. stopping a wipe at 62%) — the image carries the data; the animation only presents it.
- Name the graph layer for replacement (e.g. `savings_graph`) and keep the layer box's aspect ratio equal to the graph image's, so every swapped variant lands pixel-identical.

## Unpacking and repacking an existing `.idm`

```bash
strata inspect film.idm --assets un/ -o un/doc.json   # -> every asset + the VASCO doc
#   ...edit un/doc.json, or swap a file in un/...
strata repack un/doc.json -o film2.idm                # -> re-encoded .idm
```

`inspect --assets` writes **every** asset id-prefixed (`00_clip.mp4`, `01_logo.png`, …) plus
an `assets.json` manifest mapping `asset_id -> file`. `repack` rebinds **by id** using that
manifest, so swapping a file on disk is enough to swap the asset. Asset URIs resolve against
the document's own directory (or `--assets <dir>`), so an unpacked folder repacks as-is.
*Verified:* a 6-asset scene (video + 3 images + font + audio) unpacked byte-identical,
survived an edit, repacked, and rendered.

⚠️ **Repack is an escape hatch, not an editing workflow.** A VASCO document is **baked
output**: `{"t":0,"v":1,"ease":"outExpo"}` has already become ~100 per-frame 4×4 matrices,
`box`+`position` are folded into one transform, `font`/`src` are integer ids. *Measured:* a
1,880-byte scene compiles to a 69,475-byte VASCO doc with 3,575 baked animation numbers.
So **text edits and asset swaps are fine; motion is not hand-editable**, and you lose
`validate`'s glyph/overlap/position warnings and the tagging flow. **If the source scene JSON
exists, edit that and recompile — always.** `compile` refuses a VASCO document rather than
silently writing an empty `.idm`.

### ⛔ Two assets with IDENTICAL BYTES crash the exporter (error 3000)

*Measured by bisection:* a 6-asset scene where two assets held the same bytes under different
filenames failed export every time with `error_code 3000 "Scene exporter error"` — which
names nothing and reads like a corrupt scene. The same 6 assets with all-distinct content
exported fine. **The compiler now dedupes assets by content hash**, so the same file reused
under two names collapses to one id — the crash is gone and the `.idm` shrinks (measured:
4,189,841 → 3,812,502 bytes). Worth knowing when reading someone else's `.idm`, or if you
ever build one outside this CLI.

### ⚠️ The encoder stores only an asset's BASENAME

`./a/logo.png` and `./b/logo.png` both land as `logo.png`. The `.idm` is fine — bytes and ids
stay distinct — but `idm2vasco` returns assets keyed by that name, so on extraction one
file's **bytes are unrecoverable** (*measured:* 2 assets in, 1 file out, silently). The
compiler now stages a copy under a unique name (`logo_2.png`) so every `.idm` we produce is
fully unpackable, and `inspect` warns loudly if it meets a third-party `.idm` that has this
problem.

## Raw VASCO passthrough

Any layer/comp key not consumed by the sugar above is copied **verbatim** into the compiled VASCO — useful for real VASCO properties the sugar doesn't cover, e.g. `is_3d`, `motion_blur`, `placeholder`, `offset_frame`, `track_matte`, `playback_mode`, `baseline`, `field_of_view`, `shutter_angle`.

⚠️ **This is the #1 source of compile errors.** The VASCO schema is **strict (`additionalProperties: false`)**, so passthrough only works for keys that are *genuinely* VASCO properties. An invented or mistyped key — `z`/`zIndex`, `x`/`y`, `width`/`height` on a layer, `comment`, `id`, `label`, `radius` **on a layer** (it is valid only inside a mask `rect`), `src` on a non-media layer, `font`/`size`/`text` on a non-text layer — is passed through and then **rejected**, failing the compile with `unknown key "…"`. This holds inside masks too, so a typo like `feathr` is named rather than silently ignored. Stick to documented sugar or real VASCO properties.

Each layer type allows only a fixed set of properties (run `strata schema` for the authoritative list). The common ones:
- **all visual layers:** `bounds`/`box`, `anchor_point`, `transform` (via position/scale/rotation/anchor), `opacity`, `color`, `blend_mode`, `mask_id`, `effect_ids`, `animations`, `motion_blur`, `is_3d`, `first_frame`, `num_of_frames`, `offset_frame`, `placeholder`, `track_matte`, `name`, `type`, `visible`
- **text adds:** `font_id`/`font`, `font_size`/`size`, `text`, `alignment`/`align`, `tracking`, `leading`, `breakline`, `shrink`, `min_font_size`, `rtl`, `ellipsis`, `styles`, `animators`, `baseline`
- **media adds:** `asset_id`/`src`, `playback_mode`/`loop`; **camera:** `field_of_view`/`fov`

Verify before encoding: `strata validate scene.json` (offline schema check — names any bad key), or `compile --vasco` / `validate --print` to see the generated VASCO.
