# Foundations: principles, timing, easing, physics, rhythm

> **The executable counterpart is [motion-design.md](../motion-design.md)** - the same ideas as real
> VASCO keys, measured on this engine. This file names and classifies; that one tells you what to
> type. **Where the two give a number for the same thing, that file wins.**

## Contents

- [1. Classic animation principles (the Disney 12, adapted for graphics)](#1-classic-animation-principles-the-disney-12-adapted-for-graphics)
- [2. Motion graphics and UI motion principles](#2-motion-graphics-and-ui-motion-principles)
- [3. Timing and spacing](#3-timing-and-spacing)
- [4. Easing](#4-easing)
- [5. Physics and weight](#5-physics-and-weight)
- [6. Rhythm and patterns](#6-rhythm-and-patterns)


## 1. Classic animation principles (the Disney 12, adapted for graphics)
- **Squash and stretch**: deform along the motion axis to sell speed and softness, preserving volume. Playful, organic work and impacts; avoid on text and precise corporate pieces.
- **Anticipation**: a small move opposite the main action first, priming the eye so a fast action reads. Roughly a third of the main action's duration.
- **Staging**: one idea at a time, clear silhouette, clear focal point; nothing competes.
- **Straight-ahead vs pose-to-pose**: improvised frame-by-frame flow vs keyed poses with in-betweens. Motion graphics is almost entirely pose-to-pose; think straight-ahead for organic effects (smoke, boil, hand-drawn).
- **Follow-through**: parts keep moving after the body stops (a tail, a hanging tag, a trailing line).
- **Overlapping action**: parts of one system move on different timings; nothing starts and stops together.
- **Slow in and slow out**: acceleration and deceleration; the root of all easing.
- **Arcs**: natural motion travels on curves; add a slight arc to position paths, even in UI.
- **Secondary action**: a supporting motion that enriches the primary without competing (a soft glow pulse under a moving icon).
- **Timing**: frames per action define weight and mood — few frames light and fast, many heavy and slow.
- **Exaggeration**: push pose or easing past realism so the idea reads at a glance.
- **Solid drawing**: objects keep believable mass, perspective and proportion through the move.
- **Appeal**: charisma of the motion; clean, confident, readable.

## 2. Motion graphics and UI motion principles
- **Easing**: time is the first design decision (see section 4).
- **Offset and delay (stagger)**: related elements start a few frames apart, reading as a group with internal structure.
- **Parenting**: elements inherit motion from a parent rig; one change cascades; hierarchy for free.
- **Transformation**: one element becomes another through a continuous change (a button becomes a form, an icon a spinner).
- **Value change**: numbers and data animate their values, so the change is perceived, not just the result.
- **Masking**: a moving boundary reveals or hides; reveals cost less attention than fades.
- **Overlay**: layers on different timings communicate relative position and depth.
- **Cloning**: new elements spawn from an existing one (items fly out of a source).
- **Obscuration**: partial hiding (blur, dim, opacity) shows something sits behind a new state.
- **Parallax**: layers move at different rates to communicate depth and hierarchy.
- **Dimensionality**: folds, flips and floats that give a surface a third axis.
- **Dolly and zoom**: camera or world moves toward the viewer to navigate hierarchy.
- **Object constancy**: an element keeps its identity across transitions — the same shape moves rather than dying and reappearing.
- **Causality**: every motion has a visible cause (a click, a cut, a collision, a beat, a spoken word); unmotivated motion feels random.
- **Spatial consistency**: things exit toward where they came from; the space has stable rules.
- **Continuity of motion**: motion vectors carry across cuts so the eye is never reset.
- **Motion economy**: the fewest moves that deliver the idea; every motion earns its frames.
- **Motion hierarchy**: amplitude and duration scale with importance — hero moves big and slow, details small and quick.
- **Informative, focused, expressive**: motion explains relationships, guides attention and carries character, in that priority order.

## 3. Timing and spacing
- **Timing vs spacing**: timing = how many frames; spacing = where the object sits on each frame. Same timing, different spacing, completely different feel.
- **Frame thinking**: design in frames, not seconds. At 25 fps one frame is 40 ms; 12 frames is about half a second.
- **Duration scale**: micro 80-150 ms (toggles, hovers), small 150-300 ms (UI entrances), medium 300-500 ms (element entrances), large 500-800 ms (scene builds), hero 800-1500 ms (logo, payoff). Longer only with a reason. In a VASCO scene think in FRAMES at the comp fps, not ms - the tempo rule and what each frame count reads as are in [motion-design.md](../motion-design.md).
- **Entrance slower than exit**: viewers need to meet an element, not watch it leave — measured on this engine, exits run 65-75% of their entrance ([motion-design.md](../motion-design.md)).
- **Hold and moving hold**: a pause where an element rests; a dead hold can look frozen, so add 1-3% drift or breathing.
- **Settle**: the last 20-30% of a move, decelerating into position; rushing the settle is the most visible quality loss.
- **Anticipation ratio**: anticipation is short relative to the action, roughly 1:3.
- **Stagger interval**: shorter reads as one object, longer as separate events. 30-80 ms is the general-industry figure for UI siblings; **for a VASCO scene use the layer and per-character stagger in [motion-design.md](../motion-design.md), which is larger (a beat between title/subtitle/CTA) and caps total stagger** - that file's numbers win here.
- **Stagger direction**: follow reading direction (LTR or RTL), the motion source, or importance; one rule per project.
- **Read time**: text needs a hold proportional to its length before it may move off; never cut text before it can be read twice. Use the formula in [motion-design.md](../motion-design.md) - do not re-derive it here.
- **Frame budget**: decide the total frames for a shot, then allocate to entrance, hold and exit.
- **Ones, twos, threes**: animating every frame vs every 2nd or 3rd; stepped motion reads as hand-made or retro.
- **Snap vs glide**: very short expo moves for mechanical precision; long curved moves for elegance.
- **Overlap in time**: start the next element around the 60-70% mark of the previous one to avoid a lumpy sequence.
- **Breathing room**: deliberate stillness before a payoff; silence in time is emphasis.
- **Tempo**: events per second; vary it between sections to avoid monotony.
- **Beat alignment**: key frames land on musical beats; frames per beat = fps × 60 ÷ BPM (25 fps at 120 BPM = 12.5 frames). ⚠ That formula assumes a constant tempo - **never eyeball or compute onsets for real music, read them with `strata beats`** ([generative-fx.md](../generative-fx.md)).
- **Time remapping and speed ramps**: accelerate or decelerate a whole sequence; ramp into a cut for impact.
- **Freeze frames and frame holds**: stop time as punctuation.
- **Duration by distance**: longer travel gets a longer duration, but sub-linearly. The measured ladder (fraction of frame crossed to duration multiplier) is in [motion-design.md](../motion-design.md).

## 4. Easing
- **Linear**: constant speed; mechanical. Right for conveyor belts, tickers, scrolling loops, clock hands; wrong for almost every entrance and exit.
- **Ease-out (decelerate)**: fast start, soft landing; the default for anything arriving on screen.
- **Ease-in (accelerate)**: slow start, fast departure; the default for exits.
- **Ease-in-out**: for moves that begin and end on screen (repositioning, camera moves).
- **Curve strength ladder**: sine (gentle) → quad → cubic → quart → quint → expo (aggressive). Stronger reads snappier and more modern, gentler calmer and more classic.
- **Exponential ease-out**: near-instant start then a long tail; the "snappy premium UI" feel.
- **Back (overshoot)**: passes the target and returns; playful; 5-10% overshoot is plenty; avoid on text and data.
- **Elastic**: several decaying oscillations; cartoon energy; rare.
- **Bounce**: hits and rebounds like a ball; physical, kid-friendly.
- **Circ**: abrupt start or stop; mechanical precision.
- **Steps and hold keyframes**: jump between values with no interpolation; counters, stop-motion, retro.
- **Cubic bezier vocabulary**: (0.2, 0, 0, 1) standard; (0.4, 0, 0.2, 1) classic standard; (0.05, 0.7, 0.1, 1) emphasized decelerate; (0.3, 0, 0.8, 0.15) emphasized accelerate; (0, 0, 0, 1) pure decelerate.
- **Spring physics**: mass, stiffness, damping; duration emerges from physics; natural and interruptible. Critically damped = no overshoot; underdamped = bounce.
- **Asymmetric easing**: fast-in with a long slow settle gives a premium feel.
- **Easing per property**: opacity usually finishes before position; scale may overshoot while position doesn't. Decouple them.
- **Speed graph vs value graph**: shape the speed graph for the feel of acceleration, the value graph for positions.
- **Interruptible motion**: a new input redirects from the current velocity, never restarts from zero.
- **Velocity matching**: when one element hands motion to another, match speed at the handoff.
- **Easing consistency**: one or two curves per project; inconsistent easing is the most common amateur tell.
- **Rotation and scale easing**: rotation wants less overshoot than position; scale wants ease-out from slightly small (0.9-0.95), not from zero.
- **Eased opacity**: fades read better with ease-out on entrance and ease-in on exit, not linear.

## 5. Physics and weight
- **Mass**: heavy things start slow, stop slow, rarely overshoot; light things snap and flutter.
- **Gravity**: downward acceleration; falling objects ease-in; thrown objects ease-out then fall.
- **Drag and friction**: long deceleration tails (sliding to a stop); velocity decays exponentially.
- **Inertia and momentum**: objects resist changes; big objects need anticipation to turn.
- **Elasticity and bounce decay**: each bounce loses energy (about 60% of the previous height) and time.
- **Pendulum**: sinusoidal swing with slow ends and a fast middle.
- **Chain, rope, spring chain**: each link lags the previous one by a fixed delay.
- **Follow and lag**: a follower eases toward a leader with constant delay; cursors, trails, cameras.
- **Turbulence and wind**: low-frequency noise on position and rotation; floating, flags, ambient life.
- **Jitter and wiggle**: high-frequency noise; nerves, handheld, energy, glitch.
- **Collision**: impact with squash, a short camera shake, a hit sound, and recoil.
- **Buoyancy and float**: slow sine drift on Y with slight rotation; idle states and hero products.
- **Magnetism and attraction**: elements accelerate toward a target and settle; snapping UI.
- **Weight through timing**: to make something heavier, add frames to the acceleration and remove overshoot.

## 6. Rhythm and patterns
- **Regular rhythm**: equal intervals; calm, systematic, corporate.
- **Syncopation**: off-beat accents; energetic, musical.
- **Accelerando and ritardando**: intervals shrink or grow; tension or release.
- **Cascade**: a wave of staggered starts through a grid or list.
- **Ripple**: stagger radiating from a point.
- **Call and response**: one element moves, another answers.
- **Pulse**: periodic scale or opacity breathing for idle, live, or attention states.
- **Loop types**: seamless loop, ping-pong, cycle with a reset event.
- **Repetition with variation**: repeat a motif and change one parameter each time.
- **Phrase structure**: group beats into phrases (4 or 8); change something at phrase boundaries.
