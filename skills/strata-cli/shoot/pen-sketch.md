# `strata sketch` — the pen sketch, a reference and nothing else

A hand-drawn still that tells a generation model a pose, a concept or a composition.

Part of the reference/dialogue/shot-control guide — the index, and every other part, is in [video-generation-advanced.md](../video-generation-advanced.md). The other mode of the same command, the grey-box 3D animatic, is in [sketch.md](sketch.md) — **that one moves, this one does not.**

## What it is for

Some things words do not carry and a grey box cannot show: a body in a pose, a hand gesture, where
the subject stands in the frame and how big, what the eye reads first, a concept the model should
follow rather than invent. The pen sketch draws them with rough.js — ink lines on white, the hand
of a storyboard artist — as a **PNG still**, and hands the drawing to generation as a **reference**:

| You need | Hand the still to |
|---|---|
| a pose or gesture for a person, an animal, a product held a certain way | `generate image … --ref-image <url>`, or `generate video … --first-frame` once the image exists |
| a composition: where things sit, their scale, the reading order | `--ref-image`, cited in the prompt as the layout |
| a concept drawing the model should follow | `--ref-image` |
| a camera move, blocking over time, cut timing | **not this tool** — the grey-box animatic, [sketch.md](sketch.md), as `--ref-video` |

A camera direction can still be *written on* the still (`CAMERA: PUSH IN ON HER`, an arrow for an
entrance) the way a storyboard frame carries it; the move itself is the animatic's job.

rough.js ships inside the binary. Nothing is fetched. The sketch is deterministic for a seed, so a
revision changes one number in the spec and the rest of the drawing stays.

## One output

```bash
strata sketch pose.json -o pose.png            # PNG only
strata upload pose.png --allow-public-upload   # only after informed consent for this non-sensitive drawing
strata generate video "<the real content>" --ref-image <that url> --duration 6 -o out.mp4
```

The command tells a pen spec from an animatic spec by `"kind": "pen"` or the presence of
`elements`. A pen spec with `duration`, `fps`, `camera`, `animate` or `draw` is refused with the
pointer to the animatic — the pen sketch has no timeline by design.

## The spec

```json
{ "kind": "pen", "width": 1280, "height": 720,
  "seed": 11, "roughness": 1.5, "stroke": "#111", "strokeWidth": 3, "background": "#fff",
  "frame": { "center": [640, 360], "zoom": 1, "rotation": 0 },
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
| `group` | `children`, plus any transform | a whole figure placed, scaled or rotated as one |

Every element takes `name`, `position`, `scale`, `rotation`, `pivot`, `opacity`, `stroke`,
`strokeWidth`, `roughness`, `seed`, and `fill` (`none`, `hachure`, `solid`) with `fillColor` and
`hachureGap`. `frame` is the framing of the whole drawing — a zoom on the subject, a tilt — not a
move. Thin, low-opacity lines make guides (thirds, a safe area) the model reads as structure.

## Worked specs — adapt, do not start from zero

- **A pose** — [pen-pose.json](../examples/pen-pose.json): a woman in tree pose, 720×1280. Limbs
  are outlined so the body has weight; the figure is one `group` with `position` and `scale`, so a
  different framing is two numbers, and a different pose is the limbs' `points`.
- **A composition** — [pen-composition.json](../examples/pen-composition.json): a frame with
  thirds, a horizon, a hatched building, a labelled subject on the right third, an arrow for her
  entrance and the camera direction lettered at the bottom, storyboard-style.

## Using it as a reference — the prompt

The sketch contributes **geometry**. The look must come from the prompt, in full, every time:
`Style & Mood`, the identity lock, the Static Description. Cite the sketch by its address
([ref-image.md](ref-image.md), *How to address a reference*) and say what it is:

> *[Image 1] is a pencil storyboard sketch. Follow its POSE and COMPOSITION exactly: the woman
> stands on the right third in tree pose, palms together above her head. Ignore its look
> entirely — the finished frame is photographic, …*

⚠ **The look leaks unless the prompt overrides it.** *Measured* on the 3D animatic: a wireframe
version leaked its wireframe lines into the generated footage, which is why that mode renders
solid blocks ([sketch.md](sketch.md)). A pen sketch is a stronger look than a wireframe. Treat every
pen reference as one that will be copied, write the "ignore its look" line, and check the first
returned image or clip for pencil texture, hatching or a white ground before generating the rest.
Whether a pen reference holds its pose *and* loses its look in one pass is **cloud proof pending**:
the first job to use one records the result in `decisions.md` and, if it holds, in this page.

## What it must never be

A pen sketch is never a layer in the delivered piece. If the brief *wants* the ink look — a
hand-drawn explainer, a sketched marker around a word — that is content, built for it: a still
placed as an image layer, a path drawn on with `strata path`, a `.jet` over the plate
([marker highlight](../recipes/type.md#marker-highlight-sweep), [generating-assets.md](../cli/generating-assets.md)).
The reference sketch and the content drawing can share a spec; they never share a purpose.

## Checklist

- [ ] The still says one thing — a pose, a layout, a concept — and is labelled so the model reads it
- [ ] Anything that moves is the animatic's job ([sketch.md](sketch.md)), not a pen spec
- [ ] Uploaded only after consent for a non-sensitive drawing ([upload.md](upload.md))
- [ ] Prompt cites it by address, names what to copy, and says *ignore its look*
- [ ] First returned image or clip checked for leaked pencil texture before the rest are generated
- [ ] Not in the scene: the deliverable has no sketch layer unless the brief asked for ink
