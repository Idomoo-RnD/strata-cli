# Tracking recipes

Putting type ON a surface in footage, and making an element follow a moving subject.

Part of the recipe library — the index, and every other part, is in [recipes.md](../recipes.md).

## 8. Tracking — put text ON a surface, or make an element FOLLOW a subject

`strata track` analyses footage and writes keyframes. Two modes, one command:

```bash
strata track shot.mp4 --comp 400x400          # SURFACE -> a corner_pin effect
strata track shot.mp4 --point 640,355         # ELEMENT -> position keyframes
```

**⚠️ This command is the ONLY way to track — NEVER track by hand.** Do not eyeball a few
frames and interpolate keyframes, and do not write a one-off tracker script: a hand-track
drifts and jitters between the guessed points and the graphic reads pasted-on, every time. `strata track` measures **every** frame and smooths the path. If it reports a
weak match (surface face-on score < ~0.45, point match < ~0.4), the fix is the **footage** —
re-prompt the shot per "Generate the footage TO FIT the effect" below — never a manual fallback.

### When to use it
- **Surface mode** — the user wants text/a logo **on** a sign, screen, poster, billboard,
  phone or wall, sitting in the scene with correct perspective. Offer it unprompted when
  their footage contains an obvious flat surface: *"want the headline painted onto that
  sign?"*
- **Point mode** — a label, callout, price tag, arrow or badge that **follows** a moving
  subject (a plane, car, product). Offer it when footage has one clear subject.

### Surface mode — output and use
Writes `{ effect: {...} }` — paste it straight into a layer's `effects`:
```json
"comps": { "face": { "width":400, "height":400, "layers":[
   { "type":"text","name":"pin_l1","text":"FRESH","font":"./bold.ttf","size":110,
     "color":"#1f3b1f","box":[0,90,400,120],"align":"center middle" } ] } },
"layers": [
  { "type":"video","name":"plate","src":"./shot.mp4","box":[0,0,1280,720],"fit":"fill" },
  { "type":"comp","comp":"face","name":"pinned","box":[0,0,400,400],"blend":"multiply",
    "effects":[ <paste effect here> ] } ]
```
- ⚠️ **Must be a `comp`** (or `solid`). Corner pin is silently ignored on `text` and makes
  an `image` vanish — format.md.
- **`blend:"multiply"`** reads as printed on the surface (grain and shading show through)
  rather than pasted on top.
- The CLI reports **how face-on the surface stays**; below ~0.45 it warns — the pin
  degenerates and the content bleeds off the edge.

### Point mode — output and use
Writes `{ animate: { position: [...] } }` as **offsets from the tracked start point**, so
the follower's **`box` sets where the label sits** and the animation carries it.

**Put the callout in a sub-comp and give the COMP LAYER the keyframes — once.** A callout is
never one layer (panel, leader line, title, value), and a sub-comp is how several layers move as
one ([format.md](../format.md), *Sub-compositions*): the comp layer is the handle, so one animation
carries all of it. Surface mode already works this way; point mode is the same shape.
```json
"comps": { "callout": { "width":330, "height":86, "duration":6, "layers":[
   { "type":"solid","name":"cal_bg","color":"#0b1020","box":[0,0,330,86],"opacity":0.72 },
   { "type":"solid","name":"cal_rule","color":"#4cc9f0","box":[0,0,4,86] },
   { "type":"text","name":"cal_title","text":"FLIGHT AY318","font":"./bold.ttf","size":38,
     "color":"#4cc9f0","box":[18,10,300,42],"align":"left middle" },
   { "type":"text","name":"cal_value","text":"31,000 ft","font":"./bold.ttf","size":26,
     "color":"#8a97a8","box":[18,50,300,30],"align":"left middle" } ] } },
"layers": [
  { "type":"video","name":"plate","src":"./shot.mp4","box":[0,0,1280,720],"fit":"fill" },
  { "type":"comp","comp":"callout","name":"callout_group","box":[790,360,330,86],
    "animate": { "position": <paste keyframes> } } ]
```
*Measured by render:* four parts in one comp with a single `position` animation travelled 440 px
right and 180 px up with every part in register. The reason to care is revision cost — a re-track,
a retime or a nudge replaces **one** animation instead of four, and nothing can be left behind.
❌ Pasting the same keyframes onto each part is the anti-pattern ([traps.md](../traps.md)).

`--size` is the template radius, `--search` the per-frame search radius (keep it just above the
subject's speed — cost grows fast). The CLI prints a **weakest-match** score; below ~0.4 the
track slipped.

### ⚠️ Generate the footage TO FIT the effect
If the effect is chosen first and I'm generating the clip, I design the image **and** the
motion prompt around it — that decides whether the effect works at all.

**Surface (corner pin):**
- *Image prompt:* a **large blank rectangular** sign/screen at a **three-quarter angle**,
  **plain flat face**, contrasting border/frame, occupying a **big part of the frame**,
  sharp focus. A blank face makes detection reliable *and* leaves room for the text.
- *Motion prompt:* **slow lateral dolly**, gentle push-in/out, or small handheld parallax;
  *"the sign stays fully in frame"*, *"no cuts"*. **Never ask for an orbit, arc or 360** —
  measured: dolly tracked **144/144 frames**, an orbit dropped to **78%** and the text bled
  off the edge once the sign went near edge-on.
- Keep people/objects from crossing in front — nothing here handles occlusion.

**Point (follow):**
- *Image prompt:* one **clear, high-contrast subject on a simple background** (a plane
  against sky). The subject needs **visible structure** — matching works on texture, so a
  flat featureless blob won't hold.
- *Motion prompt:* state the **displacement explicitly** — *"travels from the left edge to
  the right edge of frame, camera completely still"*. ⚠️ Image-to-video models tend to
  **scale and rotate the subject instead of translating it**: in testing, clips asked to
  cross the frame moved the subject only ~30px. Check the track's travel and re-prompt if
  it barely moved.

Whichever mode, **match the scene fps to the clip's**, and verify with `strata preview`
or a `snapshot` before a full render.
