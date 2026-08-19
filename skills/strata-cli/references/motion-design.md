# Motion design — make it read as broadcast/After-Effects, not web animation

VASCO is an After-Effects-class engine: cubic-bezier easing, per-character animators,
3D layers + a real camera, masks, track mattes, blend modes, motion blur. The *default*
temptation is "web animation" — everything fades and slides in together on ease-in-out.
That is the amateur tell. This file is the motion language that separates the two.

**Read this by default** whenever the user hasn't dictated the motion. Pair it with
`video-layouts.md` (where things sit) — this is how they move.

---

## The 10 techniques (each maps to a real key)

### 1. Custom easing, not ease-in-out — the single biggest upgrade
Linear reads mechanical; `inOutSine` on everything reads like a slideshow. Pro motion uses
**steep curves that snap then settle**. Use the easing vocabulary deliberately, or a raw
cubic-bezier `[x1,y1,x2,y2]` for a custom curve:
- `outExpo` / `outQuart` — fast, premium snap that glides to rest (default for confident UI/text).
- `outCubic` — clean settle.
- `[0.16,1,0.3,1]` — "snap and settle" bezier; `[0.7,0,0.84,0]` — hard accel-out for exits.
- `inOutCubic`/`inOutSine` — reserve for slow drifts and camera, NOT entrances.
**Tell to avoid:** the same soft ease on every move.

### 2. Overshoot + settle — After Effects' signature
A value goes **past** its target and settles back. Two ways:
```json
// explicit overshoot: scale blows past 1.0, settles
"scale": [ {"t":0,"v":0.6,"ease":"outExpo"}, {"t":0.4,"v":1.06}, {"t":0.55,"v":1.0,"ease":"outCubic"} ]
// or lean on the easing:
"position": [ {"t":0,"v":[0,80],"ease":"outBack"}, {"t":0.5,"v":[0,0]} ]
```
`outBack` (position/scale) and `outElastic`/`outBounce` (toy/energetic only) build it in.
**Tell to avoid:** elements stop dead exactly on their mark.

### 3. Overlap & offset (stagger) — nothing arrives together
Secondary elements lag the primary by a few frames; a group cascades. This is what makes a
frame feel *composed* rather than switched-on.
- Between layers: stagger `start` (title 0.0s, subtitle 0.15s, CTA 0.3s).
- Within text: **per-word / per-character animators** (`based_on: words|characters`, `shape: ramp_up|smooth`) so a line rises word by word — use `percentage` range units so any length cascades.
**Tell to avoid:** a whole text block fading in as one flat object.

### 4. Anticipation — a tiny wind-up before the move
A 2–3 frame counter-move before the main action, at **10–20% of the main action's magnitude**: a card dips down 6px before rising, a title
nudges left before flying right. Small, but it signals intent and weight.

### 5. Follow-through / secondary motion — parts trail and settle
When a card moves, its **shadow, label and icon lag a few frames** behind and settle after —
**1–4 frames** behind the parent, at **30–50% of the primary's amplitude**, on a softer curve.
Author each as its own layer.
**Tell to avoid:** every element of a component locked rigidly together.

### 6. Motion blur — the film vs. stutter difference
On by default for visual layers (`motion_blur`), and it is exactly what HTML animation lacks.
**Keep it on** for any fast move (whip pans, snappy entrances, quick position changes) — it
is the difference between "filmed" and "stepped". Only disable for a deliberate hard-cut look.

### 7. Continuous life — no dead frames
Except a deliberate held shot, **something is always in gentle motion**: a slow camera drift,
a Ken-Burns on a still, a subtle scale-breathe on the hero, background parallax. A truly frozen
frame reads as a bug. (Held shots are still allowed — but they're a choice, not the default.)

### 8. Designed transitions — not crossfades
Opacity crossfades between scenes are the biggest slideshow tell. Instead:
- **Wipe** — animate a `mask` across the frame.
- **Scale-through** — outgoing scales up + fades as incoming scales in (see recipes.md §2).
- **Whip pan** — a fast motion-blurred position move that hands off between shots.
- **Match cut** — align a shape/position across the cut so it feels continuous.
- **Shape/morph** — a `solid` or mask grows to cover, then reveals the next scene.
**Tell to avoid:** every scene change is a dissolve.

### 9. Depth & camera — impossible in flat HTML
`is_3d:true` on layers + a `camera` (push-in, orbit, parallax). A slow camera push under a
settling title reads premium and cannot be faked in a 2D web timeline. Give layers different
**z**, move the camera, and near/far drift apart. (See SKILL.md "3D & camera".)

### 10. A consistent motion language — a "motion brand"
Every entrance in a piece shares the **same curve and timing family**; exits share another.
Random transitions per shot read as a template grab-bag. Pick one entrance ease (e.g. `outExpo`
~450ms), one exit, one transition type, and repeat them — consistency *is* the design.

---

## Choosing the values — briefs are adjectives, scenes need numbers

The techniques above say *what* to do. These tables say *which value*, so "make it feel
premium" resolves to a curve and a frame count instead of a guess.

> Adapted from LottieFiles' `motion-design` skill (MIT). The relationships and ratios are
> medium-agnostic and carry over; the **durations do not** — that skill is UI animation
> (80-400ms, sized to interaction latency). Everything below is converted to **frames at
> 25fps** and runs FAST for video. Treat them as a floor for on-screen elements and lengthen
> for full-frame moves. Where our own measured values exist, ours win.

### Emotion → motion

| Emotion | Character | Path | Ease | Duration |
|---|---|---|---|---|
| Joy / delight | bouncy, arcs, overshoot | curved, upward | `outBack` | 5–10f |
| Calm / serenity | smooth, flowing | gentle curves | `inOutSine` | 12–25f |
| Urgency / alert | sharp, fast, direct | straight | `outExpo` | 3–5f |
| Weight / sadness | slow, downward | drooping curves | `inOutCubic` | 15–30f |
| Surprise / impact | sudden, expanding | radial outward | `outExpo` | 4–8f |
| Elegance / grace | slow, controlled | long smooth arcs | `[0.4,0,0.2,1]` | 10–18f |
| Confidence | direct, decisive | straight, horizontal | `outExpo` | 5–10f |
| Tenderness | soft, gentle | very subtle curves | soft `inOutSine` | 15–25f |

**Path carries meaning on its own:** angular = tense/mechanical · curved = relaxed/organic ·
vertical up = growth · vertical down = settling · horizontal = journey · radial out =
release · radial in = focus. Pick the path to match the message, not just the easing.

### Material → easing
Ask what the thing would be made of, then scale the base duration and overshoot:

| Material | Duration | Overshoot |
|---|---|---|
| Rigid (metal, glass panel) | ×1.2 | 0% |
| Elastic (rubber) | ×0.8 | 15–25% |
| Fluid (water, ink) | ×1.5 | 5% |
| Paper / card | ×1.0 | 3–5% |
| Gas (smoke, haze) | ×2.0 | 0% |

### Two ratios worth memorising
- **Exit = 65–75% of the entrance.** Things leave faster than they arrive.
- **Distance scales duration**, it does not scale linearly: ⅒ frame ×0.8 · ⅕ ×1.0 ·
  ⅓ ×1.3 · ½ ×1.5 · full frame ×1.8–2.0.

### The 1/3 rules
- **Distance** — no move crosses more than ⅓ of frame without an intermediate keyframe.
  Break it with a direction change, a speed change or an arc.
- **Elements** — with 3+ things animating, no more than ⅓ are active at once. Stagger so the
  first settles as the last starts. Total stagger stays under ~12f.

### Counter-motion — what makes a move feel weighted
The hero moves; something else moves *against* it at a fraction of the speed:

| Hero | Counter | Ratio |
|---|---|---|
| enters from left | background drifts right | 20–30% |
| scales up | shadow scales down | 10–20% |
| rotates CW | ambient layer drifts CCW | 15–25% |
| lifts (y up) | shadow spreads + softens | 20–30% |

### Shot structure
Setup **20–30%** (elements enter) → action **30–40%** (the hero moment) → resolution
**30–40%** (settle, secondary reactions). Then leave **3–5f of stillness** before the next
move. That pause is what makes the next beat land.

## Rhythm & timing
- The **same move at 6 frames is snappy; at 30 it's languid.** Vary tempo across the piece —
  punchy hooks, slower reveals — don't run everything at one speed.
- **Hold for read time** (~0.5s + ~0.3s/word) before moving text off; never animate out before
  it can be read.
- **Cut/land key moves on the audio** when there's music or VO — get the actual onset times with `strata beats` ([generative-fx.md](generative-fx.md)); never eyeball them.

## The finish pass (compositing polish)
What separates a "designed" frame from a plain one, all available here:
`glow` on hero type/marks · `blend:"add"` light-leaks/flares · subtle grain · `shadow` for depth ·
**track mattes** (text reveals video, shapes reveal images) · `blend` modes so overlays sit *in*
the image, not on it.

---

## The "web-animation tells" checklist — run before shipping
If two or more are true, it will read as HTML animation, not motion design — fix them:
- [ ] Easing is linear or `inOutSine` on most moves (→ use snap-and-settle curves)
- [ ] Everything enters together on the same timing (→ stagger/offset)
- [ ] Elements stop dead on target (→ add overshoot + settle)
- [ ] No motion blur on fast moves (→ keep `motion_blur` on)
- [ ] Scene changes are opacity crossfades (→ designed transition)
- [ ] The frame is flat — no camera move, no depth (→ add a push/parallax)
- [ ] The same single entrance style repeats with no variation in tempo
- [ ] A frame sits fully static with nothing alive in it
