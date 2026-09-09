# Recipe library — drop-in keyframe patterns

51 recipes in the **compact scene format**. Each snippet is one or a few layers: paste into a scene's `layers`, swap the font/box/colours, tune the timing. Times are **seconds**; coordinates assume a **1280×720** comp — for the 1920×1080 grid in layouts.md multiply every box, size and distance by **1.5**. The craft theory (shots, easing, 3D/camera, rhythm) lives in [craft.md](craft.md).

**Conventions used below**
- `"./font.ttf"` — any real `.ttf`/`.otf`. `"./image.jpg"` — your media.
- A few FX want tiny helper PNGs, generated once: **`noise.png`** (grayscale value-noise, for luma dissolves), **`flare.png`** / **`leak.png`** (bright shapes on black, used with `"blend":"add"`). Optional.
- **Anchor rule** (the #1 gotcha): when a recipe sets `anchor`, every `position` keyframe is the absolute point where that anchor lands — the *resting* keyframe equals the anchor ([format.md](format.md), the anchor+position warning).

## Contents

- [1. Text](recipes/type.md#1-text)
  - [Tracking-in (letters spread → converge)](recipes/type.md#tracking-in-letters-spread--converge)
  - [Rich multi-style headline (per-span colour + word entrance)](recipes/type.md#rich-multi-style-headline-per-span-colour--word-entrance)
  - [Typewriter with a caret that follows the text](recipes/type.md#typewriter-with-a-caret-that-follows-the-text)
  - [Line-by-line stagger](recipes/type.md#line-by-line-stagger)
  - [Per-word bounce-in](recipes/type.md#per-word-bounce-in)
  - [Wavy / travelling lift](recipes/type.md#wavy--travelling-lift)
  - [RGB-split glitch (VERIFIED with channel masks)](recipes/type.md#rgb-split-glitch-verified-with-channel-masks)
  - [Per-letter 3D flip-in](recipes/type.md#per-letter-3d-flip-in)
  - [Colour sweep across letters](recipes/type.md#colour-sweep-across-letters)
  - [Marker highlight sweep](recipes/type.md#marker-highlight-sweep)
  - [Seamless loop — first frame equals last frame, invisibly](recipes/type.md#seamless-loop--first-frame-equals-last-frame-invisibly)
  - [Rule wipe — a divider drawing on between two regions](recipes/type.md#rule-wipe--a-divider-drawing-on-between-two-regions)
  - [Textured text — gradient / stripes / gold shine (VERIFIED)](recipes/type.md#textured-text--gradient--stripes--gold-shine-verified)
- [2. Transitions](recipes/transitions.md#2-transitions)
  - [Scale-through (A blasts past, B scales in)](recipes/transitions.md#scale-through-a-blasts-past-b-scales-in)
  - [Whip-pan (motion-blur swish)](recipes/transitions.md#whip-pan-motion-blur-swish)
  - [Light-leak bloom over the cut](recipes/transitions.md#light-leak-bloom-over-the-cut)
  - [Clock wipe (radial wedge)](recipes/transitions.md#clock-wipe-radial-wedge)
  - [Iris (circle) reveal](recipes/transitions.md#iris-circle-reveal)
  - [3D flip transition](recipes/transitions.md#3d-flip-transition)
  - [Speed-line streak](recipes/transitions.md#speed-line-streak)
  - [Luma dissolve (textured)](recipes/transitions.md#luma-dissolve-textured)
- [3. Motion](recipes/transitions.md#3-motion)
  - [Anticipation + overshoot](recipes/transitions.md#anticipation--overshoot)
  - [Float / bob idle loop](recipes/transitions.md#float--bob-idle-loop)
  - [3D camera push-in](recipes/transitions.md#3d-camera-push-in)
  - [Fly-through warp (scale-from-depth)](recipes/transitions.md#fly-through-warp-scale-from-depth)
  - [Bounce drop + squash](recipes/transitions.md#bounce-drop--squash)
- [4. Masks](recipes/masks.md#4-masks)
  - [Underline that writes itself](recipes/masks.md#underline-that-writes-itself)
  - [Progress ring that fills to a value](recipes/masks.md#progress-ring-that-fills-to-a-value)
  - [Radial tick scale, one object](recipes/masks.md#radial-tick-scale-one-object)
  - [Logo draw-on from the logo's own outline](recipes/masks.md#logo-draw-on-from-the-logos-own-outline)
  - [Feathered iris reveal](recipes/masks.md#feathered-iris-reveal)
  - [Shape morph (blob ↔ star)](recipes/masks.md#shape-morph-blob--star)
  - [Moving spotlight](recipes/masks.md#moving-spotlight)
  - [Photo through text (alpha matte)](recipes/masks.md#photo-through-text-alpha-matte)
  - [Knockout text (hole in a panel)](recipes/masks.md#knockout-text-hole-in-a-panel)
  - [Barn-door reveal](recipes/masks.md#barn-door-reveal)
  - [Soft gradient wipe](recipes/masks.md#soft-gradient-wipe)
- [5. Special FX](recipes/fx.md#5-special-fx)
  - [Neon glow pulse + flicker](recipes/fx.md#neon-glow-pulse--flicker)
  - [Depth-of-field focus pull](recipes/fx.md#depth-of-field-focus-pull)
  - [Chromatic aberration (RGB split breathing)](recipes/fx.md#chromatic-aberration-rgb-split-breathing)
  - [Ambient light leaks](recipes/fx.md#ambient-light-leaks)
  - [Lens flare sweep](recipes/fx.md#lens-flare-sweep)
  - [Corner-pin perspective turn](recipes/fx.md#corner-pin-perspective-turn)
  - [Mirror reflection](recipes/fx.md#mirror-reflection)
  - [Frosted-glass panel](recipes/fx.md#frosted-glass-panel)
  - [Colour grade (teal–orange duotone)](recipes/fx.md#colour-grade-tealorange-duotone)
  - [Long flat shadow](recipes/fx.md#long-flat-shadow)
- [6. Extras](recipes/fx.md#6-extras)
  - [Rich text — many styles in one block (VERIFIED)](recipes/fx.md#rich-text--many-styles-in-one-block-verified)
  - [Easing comparison — one ease per row](recipes/fx.md#easing-comparison--one-ease-per-row)
- [7. Data viz](recipes/data-viz.md#7-data-viz)
  - [Count-up number — NATIVE, and safe for personalization (VERIFIED by render)](recipes/data-viz.md#count-up-number--native-and-safe-for-personalization-verified-by-render)
  - [Bar chart (mask wipe L→R)](recipes/data-viz.md#bar-chart-mask-wipe-lr)
  - [Progress ring (expanding wedge)](recipes/data-viz.md#progress-ring-expanding-wedge)
  - [Stat bar / percentage fill](recipes/data-viz.md#stat-bar--percentage-fill)
  - [Line-chart draw (path mask sweep)](recipes/data-viz.md#line-chart-draw-path-mask-sweep)
  - [Parallax depth (3D) — MEASURED](recipes/data-viz.md#parallax-depth-3d--measured)
- [8. Tracking — put text ON a surface, or make an element FOLLOW a subject](recipes/tracking.md#8-tracking--put-text-on-a-surface-or-make-an-element-follow-a-subject)
  - [When to use it](recipes/tracking.md#when-to-use-it)
  - [Surface mode — output and use](recipes/tracking.md#surface-mode--output-and-use)
  - [Point mode — output and use](recipes/tracking.md#point-mode--output-and-use)
  - [⚠️ Generate the footage TO FIT the effect](recipes/tracking.md#️-generate-the-footage-to-fit-the-effect)


---

Each recipe is a keyframe pattern to paste and adjust, not a rule. The rules they obey live in the scene-format reference and in the craft files.
