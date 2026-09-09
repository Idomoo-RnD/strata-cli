# Clips destined for an alpha overlay

A clip that will be keyed or matted has to be generated differently — no shots, no cuts.

Part of the video-generation reference — the index, and every other part, is in [video-generation.md](../video-generation.md).

## ⛔ Clips destined for a `.jet` alpha overlay — NO shots, NO cuts

When the clip will be matted (`strata matte` → `.jet`) to sit as a transparent overlay, the
shot-list approach is **wrong** and produces an unusable matte.

**Generate ONE continuous shot.** Say it explicitly — `ONE single continuous shot, no cuts.`
— and drop `Shot N:` / `Cut.` / "five distinct shots" entirely. Keep `Style & Mood:`, the
identity lock, `Static Description:` and the physical motion description.

**Why cuts break matting.** Background removal runs **per frame with temporal smoothing** —
each frame's matte is informed by its neighbours — and a hard cut is a discontinuity it
cannot follow:

- the matte **flickers or collapses** for several frames either side of every cut;
- each shot has a different subject scale and framing, so no single edge treatment serves them
  all — halo on some shots, chewed edges on others;
- shots where the subject is **absent or reduced to a detail** (a macro on hands, a wide where
  the subject is 40 px tall) produce overlay frames that are useless in a layout;
- the composite reads as a glitch, not an edit, because the layer underneath does not cut with it.

**An overlay layer is a single continuous performance. The edit happens in the scene, around
the overlay.**

### Prompt for a clean matte, not just a nice shot

- **Separate subject from background** — an evenly-lit, plain, contrasting backdrop: a flat
  colour, a clean seamless wall, or solid green/blue if you intend to key. Never a background
  sharing the subject's colours.
- **Keep the subject whole and in frame throughout.** A subject that leaves frame or is
  cropped by the edge mattes badly at that boundary.
- **A locked-off camera, stated in the prompt** (the `--camera-fixed` flag is rejected by the
  model — see *Locking the camera*), or at most a very slow drift — camera movement changes the
  background continuously and gives the matte more to get wrong.
- **Avoid what mattes badly**: wispy or flyaway hair, smoke, steam, water spray, glass, veils,
  heavy motion blur, and backlight strong enough to silhouette. Say so in the prompt.
- **Full body or a clean waist-up**, held throughout, so the overlay is reusable.
- **No `--audio`** — an overlay's audio is discarded, so don't pay for it.
- Keep it **short** (4–6 s), and end near where it began if it needs to loop.

Then: `strata matte <clip.mp4> -o overlay.jet`. See [assets.md](../assets.md) for matte/jet
mechanics and [video-layouts.md](../craft/video-layouts.md) for composing an alpha overlay.

---
