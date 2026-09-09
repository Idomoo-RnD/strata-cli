# Depth, light and atmosphere

Layers of distance, where the light comes from, and the atmosphere that stops a frame reading as flat vector art.

Part of the craft manifesto — the index, and every other part, is in [craft.md](../craft.md).

### Depth, light & atmosphere
**A reference frame is not evenly lit and not evenly filled; ours are both.** Four decisions, made
at storyboard time and checked by putting `contact.png` beside a reference frame — this is the
picture half of the range the four numbers set:
- **Light comes from somewhere.** Name the key's direction and colour per shot and keep it across
  the piece. Every authored surface of ours is one flat fill with a hard edge — a colour band, an
  end-card ground, a wordmark — lit by nothing. A gradient across the solid, a warm bounce on the
  shadow side, a specular sheen on the edge the key catches, a shadow where two planes meet, a
  falloff into the corners: no render cost, and the whole difference.
- **Type sits at a depth, not on the glass.** A reference title carries the value and haze of the
  objects at its distance and something crosses in front of it; ours is 100 % white at one weight
  over every plate. **Four tells prove a wordmark is an alpha-matte cut-out**, all found on our best
  piece's end frame — 22 % of its runtime, the frame a client judges on: no contact shadow · a lit facet of the material behind it truncated by a letter edge instead of
  breaking across it · two light directions in one frame · a colour band inside the letters running
  perfectly horizontal across all six characters, which proves the fill is a rectangle behind a mask
  rather than something projected onto the surface. **Five moves put the title in the shot:**
  1. **Colour from the plate** — sample its light, let distance thin it, never 100 % white over a graded frame; a colour `overlay` in `softlight` or `color` grades the type *with* the shot.
  2. **A real contact shadow** — a `shadow` layer style whose `angle` is the plate's key direction,
     plus a soft blurred dark solid on the surface the type meets
     ([video-layouts.md](video-layouts.md) §6, *Ground it*). One key per frame: every shadow,
     authored or filmed, points the same way.
  3. **A fill that follows the surface** — animate the matte texture along the plate's light
     direction and let its band bend where the material behind it bends. A level band across every
     glyph is the tell above ([recipes.md](../recipes.md), *Textured text*).
  4. **Something crosses in front** — occlusion (above); a `.jet` over the type is the strongest proof the words are in the scene, not on the glass.
  5. **Match grain and falloff** — the plate's depth-of-field falloff (an animated `blur` behind it,
     a touch on the type when it sits back) and one grain pass over both. A perfectly sharp, clean
     wordmark on a filmed frame is the last thing that gives it away.
- **Give a frame away.** Reference frames hold the subject at a few per cent and let the sky do the
  rest; ours centre a large subject in nearly every shot. The small empty frame is what makes the
  next close-up land.
- **Let the look change between beats.** A 30 s reference runs cool city → blown warm → white-out;
  ours grades all thirty seconds the same.
- Glow, shadow, blur, overlays, a vignette (feathered ellipse mask), a colour grade — all available. Pick one atmospheric effect as the piece's signature: stacking glow + flare + grain + vignette is what a generator does when nobody chose ([anti-slop.md](anti-slop.md)). A deliberately maximal piece can stack them — say so in the bible. A colour overlay for a coherent grade is not an effect and is always fine.
- **Motion blur is the cheapest thing that separates "rendered" from "a slideshow"** — without it every fast move strobes and the piece reads as simple even when the design is good. The compiler turns it **on for every visual layer** (text, solid, media, sub-comp), so I never have to add it and I do not switch it off; raise comp `shutter_angle` (1–1.3, default 0.5 frames) for fast moves — **except on a piece with a native occluder**: at 360° a solid that is meant to hide type goes translucent at its leading edge and the word leaks through (*measured*, cost a render); keep the default there, or blur the occluder's motion some other way.
  **⚠ The camera is the exception, and it is the one that costs the shot.** A `camera` layer's `motion_blur` defaults to **`false`** in the schema and the compiler does not override it — so on a push, dolly, orbit or whip the LAYERS are not moving, the camera is, and the whole frame renders perfectly crisp while every layer in it is nominally "blurred". Every animated camera gets **`"motion_blur": true`** written on it. `strata validate` warns when one doesn't.
