# Logo motion — the logo dictates the move

Classify the mark first; the motion language follows from what the logo is, never from a house style of reveals.

Part of the craft reference — the index, and every other part, is in [craft.md](../craft.md). The route that lands here is [logo-ident.md](../routes/logo-ident.md); the construction is in [video-styles.md](video-styles.md), *logo-ident*.

Adapted from *logo-motion* by Kimo.Artcave and Kiarash Moazzez (Figma Community, Community Free Resource License), with the execution rewritten for this engine.

## 1. Classify the mark before any creative decision

Open the artwork and inventory it: which parts are filled shapes, which are strokes, which are
text, how many separable pieces there are, what overlaps what. Then name the type — a mark can be
two at once (an icon plus a wordmark is the commonest):

| Type | How to recognise it | The move that belongs to it |
|---|---|---|
| **Illustrative / figurative** — an animal, an object, a face | complex filled shapes, organic edges, few or no strokes | it arrives *alive*: a scale-in with a small overshoot as if landing, a micro-rotation of a few degrees that resolves, a short atmospheric materialise (opacity over 0.2–0.4 s), then a breathing settle. Body first, extremities trailing by 1–2 frames. **Never** draw it on as a stroke, never split it into parts, never rotate it past ±5° |
| **Geometric / abstract** — swoosh, trefoil, bullseye | simple shapes, symmetry, mathematical edges | it *constructs*: each piece travels in from the side it sits on and clicks into place with a crisp settle; tight stagger (2–3 frames apart); rings draw on as strokes; parts with rotational symmetry rotate to their final angle. No springs, no glow, no blur |
| **Wordmark / logotype** — the name is the mark | text, or custom letterform vectors | kinetic type: letters or words as units, staggered in reading order, a rise or slide of 8–20 px, ±2–5° of rotation per letter resolving to 0, an opacity cascade. Heavy faces enter quick and confident; light faces slow and graceful. Block letterforms are never drawn on |
| **Script / calligraphic** — connected letterforms | continuous stroke-like paths of varying weight | the one case for draw-on: a stroke reveal along the path, faster on straights and slower in curves, strokes overlapping so the writing stays continuous, a faint opacity lead for ink bleed. Needs a real stroke to reveal; a filled outline of a script has nothing to draw — the draw-on section of [format.md](../format.md) says how it is built |
| **Monogram / lettermark** — one to three letters | overlapping or interlocking glyphs | a layered reveal: letters one at a time, back to front, sliding into their interlock; the front letter last so the overlap is a beat; colour can differentiate them at the end |
| **Emblem / badge** — content inside a border | a container shape wrapping the rest | radial: the container first (a ring draws on, a shield scales in), the interior after it settles, centre outward; the finished badge gets one small pulse |
| **Line art / outline** | defined by strokes, little or no fill | progressive draw-on of the strokes in a sequence, stroke weight allowed to grow from thin; if a filled version exists it fades in after the outline completes |

Hybrids: the icon animates first with its own type's move, the wordmark follows with kinetic type
after a 0.2–0.4 s gap. An emblem with an illustration inside runs the emblem move, then the
illustrative move on the interior.

## 2. Character sets the easing

Read the mark's energy, weight and domain, then pick the curve family from
[motion-design.md](motion-design.md), *Material → easing*:

| Character | Curve | Why |
|---|---|---|
| luxurious, editorial | fast out, very long settle | feels expensive |
| playful, energetic | a small overshoot, or a light spring | bounce is the personality |
| corporate, precise | clean ease-out, no overshoot | controlled |
| organic, natural | a barely-there spring | alive, not cartoon |
| bold, aggressive | fast attack, decisive stop | force |
| delicate, refined | gentle acceleration, soft landing | care |

Linear is never a logo curve unless the brief wants a mechanical constant. A hold between beats is
a curve too — the freeze before the reveal.

## 3. Timeline

Two to four seconds; under 1.5 s reads as an accident, over 5 s as indulgence unless asked. Three
acts: anticipation (a shadow, a blur clearing, the first hint), the main action with the type's
move, the resolution where the last piece lands, overshoots settle and one finishing beat — a
pulse, a shadow emerging — punctuates the end. Stagger by one strategy only: hierarchy, a spatial
sweep, depth order, or the mark's own narrative (container before contents). Layer at least three
properties across the piece; one property alone is a slide, not a reveal.

## 4. In this engine

- Every separately timed part is its own layer or comp ([figma.md](../brand/figma.md), *Prepare the file*); a part that arrives from its own side needs its own `position` keys and its own ease.
- Draw-on is built the way the draw-on section of [format.md](../format.md) says (`strata path` for an SVG); a filled shape "drawing on" is a wipe, built with a mask.
- Overshoot lives in the ease, at the brand's stated size ([craft.md](../craft.md), *Polish*), never as extra keyframes.
- The finishing pulse is one scale key pair on the whole mark, to 1.02–1.03 and back, never more.
- Prove it on a filmstrip of consecutive frames, not a poster ([review.md](../qa/review.md), *Motion is judged on a FILMSTRIP*).

## Never

- One technique for every logo. Path-trimming an animal; handwriting a block wordmark; bouncing a delicate script.
- A single opacity fade on the whole mark — a slide-deck default.
- Every element on the same start, duration and ease.
- Deconstructing a figurative illustration into pieces, or spinning it.
- A reveal longer than five seconds.
- Animating the tiny details before the primary form exists.
