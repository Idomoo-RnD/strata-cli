# `strata sketch` — the pen sketch, a reference and nothing else

A hand-drawn sketch, still or moving, that tells a generation model a pose, a concept, a composition or a camera move.

Part of the reference/dialogue/shot-control guide — the index, and every other part, is in [video-generation-advanced.md](../video-generation-advanced.md). The other mode of the same command, the grey-box 3D animatic, is in [sketch.md](sketch.md).

## What it is for

Some things words do not carry and a grey box cannot show: a body in a pose, a hand gesture, where
the subject stands in the frame and how big, what the eye reads first, a concept the model should
follow rather than invent. The pen sketch draws them with rough.js — ink lines on white, the hand
of a storyboard artist — and hands the drawing to generation as a **reference**:

| You need | Make | Hand it to |
|---|---|---|
| a pose or gesture for a person, an animal, a product held a certain way | a still | `generate image … --ref-image <url>`, or `generate video … --first-frame` after the image exists |
| a composition: where things sit, their scale, the reading order | a still, with labels | `--ref-image`, cited in the prompt as the layout |
| a concept drawing the model should follow | a still | `--ref-image` |
| a camera move or a piece of blocking that must be seen to be understood | the video | `generate video … --ref-video <url>` |

rough.js ships inside the binary. Nothing is fetched. The sketch is deterministic for a seed, so a
revision changes one number in the spec and the rest of the drawing stays.

## Two outputs, one command

```bash
strata sketch pose.json -o pose.png            # a still; --at 1.5 picks the moment (default: fully drawn)
strata sketch camera.json -o camera.mp4        # the video; needs ffmpeg; --frames keeps the PNG sequence
strata upload pose.png --allow-public-upload   # only after informed consent for this non-sensitive drawing
strata generate video "<the real content>" --ref-image <that url> --duration 6 -o out.mp4
```

The output is **PNG or MP4 only**. The spec's `duration` decides which makes sense: a still spec has
none; a video spec needs one. The 3D animatic keeps its own spec (`shots`, `boxes`, `sprites`); the
command tells them apart by `"kind": "pen"` or the presence of `elements`.

## The spec

```json
{ "kind": "pen", "width": 1280, "height": 720, "fps": 24, "duration": 4,
  "seed": 11, "roughness": 1.5, "stroke": "#111", "strokeWidth": 3, "background": "#fff", "boil": false,
  "camera": { "center": [{ "t": 0, "v": [640, 360] }, { "t": 4, "v": [800, 300], "ease": "inOutSine" }], "zoom": [{ "t": 0, "v": 1 }, { "t": 4, "v": 1.8 }] },
  "elements": [ … ] }
```

| Element | Fields | Draws |
|---|---|---|
| `circle` | `at`, `r` | a head, a ball, a mark |
| `ellipse` | `at`, `size` | hands, feet, an eye |
| `rect` | `at` (top-left), `size` | a panel, a screen, a frame |
| `line` | `from`, `to` | a horizon, a rule, a guide |
| `curve` | `points`, `closed` | a smooth stroke through points; closed for a torso, a leaf |
| `polygon` | `points` | a building, a table, a block |
| `path` | `d` | any SVG path — an eye, a mouth, a logo outline |
| `limb` | `points`, `widths` | an outlined arm, leg, stem: a centreline with per-point half-widths |
| `arrow` | `from`, `to` (or `points`), `head` | movement, a camera direction, "goes here" |
| `text` | `at`, `text`, `size`, `align` | a hand-lettered label: BUILDING, WOMAN, PUSH IN |
| `group` | `children`, plus any transform | a whole figure that moves, scales or rotates as one |

Every element takes: `name`, `start` and `duration` (when it is on screen), `draw: { from, to }`
(the draw-on window, seconds from its start — the stroke appears in the order the pen would make
it), `position`, `scale`, `rotation`, `pivot`, `opacity`, `stroke`, `strokeWidth`, `roughness`,
`seed`, and `fill` (`none`, `hachure`, `solid`) with `fillColor` and `hachureGap`. Anything that
moves is a keyframe list on `animate` — `position`, `scale`, `rotation`, `opacity`, `draw`, and the
geometry itself: `points` (a pose that changes), `size`, `r`, `widths`. Keyframes are the scene
format's own `{ "t", "v", "ease" }` ([animation.md](../engine/animation.md)), so the eases you know
apply. `boil: true` reseeds every frame for the shimmer of hand animation; leave it off for a
reference, the model does not need the noise.

## Worked specs — adapt, do not start from zero

- **A pose** — [pen-pose.json](../examples/pen-pose.json): a woman in tree pose, 720×1280, drawn
  on in the order a hand would draw it (head, torso, arms, legs, feet, face, mat, label). Limbs are
  outlined so the body has weight; the figure is one `group` with `position` and `scale`, so a
  different framing is two numbers.
- **A composition with a camera move** — [pen-camera.json](../examples/pen-camera.json): a frame
  with thirds, a horizon, a hatched building, a labelled subject on the right third, an arrow for
  her entrance, and a camera that holds for 1.5 s then pushes in on her. The still is the layout
  reference; the video is the camera reference.

A pose that *changes* is `animate.points` on the limbs: the arm's centreline at 0 s and at 2 s, and
the sketch draws every in-between — a wave, a reach, a turn of the head — which is what a
`--ref-video` needs for a gesture the prompt cannot time.

## Using it as a reference — the prompt

The sketch contributes **geometry and timing**. The look must come from the prompt, in full, every
time: `Style & Mood`, the identity lock, the Static Description. Cite the sketch by its address
([ref-image.md](ref-image.md), *How to address a reference*) and say what it is:

> *[Image 1] is a pencil storyboard sketch. Follow its POSE and COMPOSITION exactly: the woman
> stands on the right third in tree pose, palms together above her head. Ignore its look
> entirely — the finished frame is photographic, …*

> *<Video_1> is a hand-drawn animatic. Copy its CAMERA MOVE and TIMING: a hold, then a push-in on
> her over three seconds. Ignore its look entirely — …*

⚠ **The look leaks unless the prompt overrides it.** *Measured* on the 3D animatic: a wireframe
version leaked its wireframe lines into the generated footage, which is why that mode renders
solid blocks ([sketch.md](sketch.md)). A pen sketch is a stronger look than a wireframe. Treat every
pen reference as one that will be copied, write the "ignore its look" line, and check the first
returned clip for pencil texture, hatching or white backgrounds before generating the rest. Whether
a pen reference holds its pose *and* loses its look in one pass is **cloud proof pending**: the
first job to use one records the result in `decisions.md` and, if it holds, in this page.

## What it must never be

A pen sketch is never a layer in the delivered piece. If the brief *wants* the ink look — a
hand-drawn explainer, a sketched marker around a word — that is content, built for it: a still or
PNG sequence placed as an image layer, a path drawn on with `strata path`, a `.jet` over the plate
([marker highlight](../recipes/type.md#marker-highlight-sweep), [generating-assets.md](../cli/generating-assets.md)).
The reference sketch and the content drawing can share a spec; they never share a purpose.

## Checklist

- [ ] The sketch says one thing — a pose, a layout, a concept, a camera move — and is labelled so the model reads it
- [ ] Still → PNG; video → MP4 with `duration`; `--at` chosen when the moment matters
- [ ] Uploaded only after consent for a non-sensitive drawing ([upload.md](upload.md))
- [ ] Prompt cites it by address, names what to copy, and says *ignore its look*
- [ ] First returned clip checked for leaked pencil texture before the rest are generated
- [ ] Not in the scene: the deliverable has no sketch layer unless the brief asked for ink
