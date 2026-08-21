# Video layouts — video as a design element, not a full-frame default

Most "vanilla" output is one full-bleed video with a caption on top. Designed motion graphics
treat **the footage itself as compositional material** — cropped into shapes, gridded, framed,
tracked, masked by type, cut out with alpha. This file is those patterns, each wired to the key
that does it. Everything here is already in the engine (masks, corner-pin, track mattes, blend,
`.jet`, `strata track`).

**Read this by default** when the piece uses video and the user hasn't dictated the layout.
Pair with `layouts.md` (the grid) and `motion-design.md` (how it moves).

---

## The patterns

### 1. Split-screen / multi-video grid
Two or more videos sharing the frame, snapped to the grid, with a gutter between.
- **2-up:** `[0,0,640,720]` + `[640,0,640,720]`, a 4–8px `solid` seam between.
- **2×2:** four `video` boxes at the quadrants, each `fit:"fill"`; thin solid gutters.
- **Freehand:** unequal panels (a big hero + a strip of two) — more editorial than a clean grid.
Start from a **2-panel with one purpose** (before/after, product/reaction), then add panels.
**Balance exposure** across clips so it reads intentional — with ffmpeg before import (`eq`/`curves`, video-editing.md "Grade"); the engine has no colour correction.
Motion discipline: let the *content* move; keep the panel boxes still.

### 2. Footage inside shapes ("media frames") — VERIFIED
A `video` cropped to a circle, rectangle, or custom silhouette via a **`mask`**. Mind the
coordinate systems: **`ellipse` is `[cx, cy, rx, ry]`** (centre + radii), **`rect` is
`[x, y, w, h]`**.
```json
// circle: box is 400x400 at (120,160) -> centre (320,360), radius 200
{ "type":"video","name":"clip","src":"./b.mp4","box":[120,160,400,400],"fit":"fill",
  "mask":{ "ellipse":[320,360,200,200], "feather":2 } },
// straight-edged card
{ "type":"video","name":"clip2","src":"./b.mp4","box":[600,160,560,340],"fit":"fill",
  "mask":{ "rect":[600,160,560,340] } }
```
Rounded corners are `radius` on the `rect` (`"mask":{"rect":[600,160,560,340],"radius":24}` —
a number, or `[tl,tr,br,bl]`; it clamps to half the shorter side, so a big value gives a pill).
For any *other* silhouette use a **`path`** mask (`[[x,y],...]`) or raw `shape` commands (see
format.md Masks). **Generate the polygon with code** — stars, badges, blobs, gears (recipes in [generative-fx.md](generative-fx.md) §4)
— and pop the window in by scaling from ~0 with the anchor at the shape's centre. Put a `solid`
ring/border behind the video and a `shadow` on it so the framed clip reads as an object.
Great for testimonials, product loops, avatars.

### 3. Video inside letterforms — type as a window
A **track matte**: a `text` layer is the matte; the `video` shows only through the glyphs.
The classic "big word filled with footage" title.
```json
{ "type":"text","name":"titleMatte","text":"SUMMER","font":"./bold.ttf","size":360,
  "box":[0,180,1280,360],"align":"center middle","visible":false },
{ "type":"video","name":"fill","src":"./waves.mp4","box":[0,0,1280,720],"fit":"fill",
  "matte":{ "type":"alpha", "source":"titleMatte" } }
```
The word must be heavy/bold (thin type shows almost no video). Pair with a slow push on the
video so the fill drifts. Same trick fills shapes/logos with footage. **The same matte also
carries TEXTURES instead of footage** — gradient fills, scrolling stripes, gold-shine sweeps
(generate organic textures with `strata generate image`, geometric ones with code):
verified recipes in [recipes.md](recipes.md) §1 "Textured text".

### 4. Device / phone / browser frame
Product UI or a clip sitting inside a device. A frame PNG (transparent screen area) on top of a
`video` boxed to the screen rectangle; animate a slow push or a scroll of the inner content.
See the `device-frame` block in `blocks.md`. Keeps app demos from floating in the void.

### 5. Picture-in-picture / inset
A small `video` over a full-frame `video` — a speaker over their screen-share, a reaction over
gameplay. The inset gets a `solid` border + `shadow`; place it on a grid corner inside the safe
area, not tight to the edge. Rise it in with a slight overshoot.

### 6. OCCLUSION — the subject passes IN FRONT (the signature ad move)
A transparent **`.jet`** of the hero element sits ON TOP of the type, so the subject
physically overlaps and covers the words — a shoe sweeping across the brand name, a bottle
turning in front of the price, a person walking through the headline. This is the
highest-recognition composition in modern product advertising, and it is cheap here:
**video → extract the element (chroma-key a green plate; `matte` only for a person) → `.jet` → top layer.**

⚠ **Only when the shot earns it** — one hero element, large type for it to cross, a piece
where the hero should dominate, and motion that genuinely carries it **across** the words.
If the hero never overlaps the type, skip it: `matte` is the slowest step in the pipeline
and `.jet` is lossy, so a cut-out with nothing to composite against costs time and quality
for no visible effect.

**I DETECT this opportunity myself — I don't wait to be asked.** Triggers, any of:
- the piece is an **ad/promo with one hero product or subject** (shoe, bottle, phone, car,
  food, mascot, presenter) — this is the default premium treatment, propose it;
- there is a **big wordmark, price, stat or headline** that the hero could plausibly cross;
- the user's footage/image already contains a clear extractable element.
Offer it in the storyboard: *"want the shoe to pass in front of the brand name?"*

**Two builds:**
- **Free-element occlusion (the ad look):** the `.jet` product floats over a *designed*
  type layout (giant wordmark on a colour field, kinetic price, pattern background). The
  source clip's background is discarded — so generate the product on a clean/keyable
  background and let its motion (sweep, rotate, bounce) come from image-to-video, with the
  displacement stated in the prompt.
- **Same-clip occlusion ("text behind the subject"):** plate mp4 at the bottom, text, then
  the matted `.jet` of THE SAME clip on top — the subject stays in its own world and the
  text slides between (assets.md has the absolute same-clip rule).

**Creative moves that elevate it (use them):**
- **The hidden swap** — change the text *while it is occluded*: as the shoe covers the word,
  swap "RUN" → "FASTER" (stagger the two text layers' `start` at the covered moment). The
  cut is invisible and it reads as magic. Needs the subject to fully cross the word once.
- **Depth sandwich** — two text planes: one BEHIND the jet (occluded), one small badge/CTA
  IN FRONT of everything. Three depths read instantly as designed.
- **Ground it** — a soft elliptical `shadow`-blurred dark solid under the subject ties the
  cut-out to the type world; without it the element floats like a sticker.
- **Type reacts** — the occluded word shifts colour or weight after the subject passes
  (keyframe at the crossing time), as if the subject dragged the change across.
- **Loop it** — for socials, a subject that crosses and returns makes a seamless loop;
  match first/last frames of the jet.
Timing: find the crossing frames with `strata track --point` on the jet's source if you
need exact cover times for swaps/reactions.

### 7. Tracked on-screen graphics
Lock graphics to something *in* the footage with **`strata track`**:
- **Corner-pin type onto a surface** — a sign, phone screen, billboard, TV in the shot.
- **A label/callout following a moving subject** — a plane, car, player, product.
**`strata track` is the ONLY tracker — never hand-track** (no eyeballed keyframes from
sampled frames, no custom tracker scripts); only its output goes into the scene. A weak
score means fix the footage, not track manually.
Constrain the shot (lateral dolly / gentle push, not an orbit) — see recipes.md §8.

### 8. Full-bleed footage + editorial grid
Footage as a plate, a **brightness-matched scrim** where text sits, and **column-snapped
kinetic type** on top — an eyebrow, a big headline on the thirds, a bottom-safe CTA. The design
is in the type system and the grid, not in the (single) video. Match scrim opacity to the
footage brightness (see blueprints.md).

### 9. Match-cut / multi-shot in one scene
Several `video` shots in sequence via `start`/`duration`, with a **persistent graphic frame**
(a border, a lower-third, a logo bug, a progress bar) that stays put across the cuts so the piece
reads as one designed system rather than raw clips. Land the cuts on the beat.

### 10. Video as texture / accent, not the whole frame
A clip occupying **a third or a strip** of a designed frame — a colour-blocked layout where video
fills one panel and solids/type fill the rest; or a small looping video accent inside a card. The
composition is the design; the video is one element in it.

---

## Rules that make a video layout read as designed
- **Snap video boxes to the 12-column grid** (layouts.md) — a video is a design element, not an
  automatic full-bleed.
- **One hero video; supporting videos are smaller.** Size is hierarchy — don't give three clips
  equal weight unless the point is symmetry.
- **Consistent gutters/borders** between panels (4–8px seams, or a uniform 40–80px margin).
- **Match exposure/colour** across multiple clips so a grid feels intentional (ffmpeg `eq`/`curves` before import — video-editing.md "Grade").
- **Motion discipline:** usually let *one* thing carry the motion (the footage, or the camera) and
  keep the frame still — resist animating every panel at once.
- **Frame the video** (mask, border, device, shape) rather than butting a raw rectangle against
  text — the frame is what makes it look authored.
- **Overlays sit *in* the image** — use `blend` modes, scrims and grain so graphics feel composited,
  not pasted (motion-design.md, finish pass). Confetti, light leaks, designed wipes and
  audio-reactive bars are **generated as `.jet` overlays** — [generative-fx.md](generative-fx.md).
