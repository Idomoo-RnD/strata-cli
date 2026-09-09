# `strata sketch` — the 3D animatic

Authoring a camera plan as a grey-box animatic and feeding it back in as a shot reference.

Part of the reference/dialogue/shot-control guide — the index, and every other part, is in [video-generation-advanced.md](../video-generation-advanced.md).

## `strata sketch` — author the camera plan as a 3D animatic

When a shot is complex enough that words fail — *"crane down past the sign as she crosses left
to right, cut to a low tracking shot following the car"* — build the blocking as a grey-box
animatic, render it to MP4, and feed an approved hosted copy as `--ref-video` with the "IGNORE the
look" instruction above. Read [upload policy](upload.md) first: only non-sensitive material with
informed consent can be published. Concept/customer details can make an animatic confidential.

This is the CLI's own tool. **Do not rewrite a Three.js scene by hand.**

```bash
strata sketch plan.json -o plan.mp4        # also: --html plan.html  --frames dir/
strata upload plan.mp4 --allow-public-upload  # only after informed consent for this non-sensitive input
strata generate video "<the real content>" --ref-video <that url> --duration 12 -o out.mp4
```

`plan.json`:

```json
{
  "width": 1280, "height": 720, "fps": 24, "duration": 12, "fov": 45,
  "ground": { "size": 40, "step": 2 },
  "boxes": [
    { "label": "building", "at": [-6, 2, 4], "size": [4, 4, 4] },
    { "label": "car",      "at": [3, 0.5, 0], "size": [3.6, 1.2, 1.6] }
  ],
  "sprites": [
    { "label": "woman", "size": [0.8, 1.8],
      "path": [[0, [-4, 0.9, 0]], [6, [0, 0.9, 0]], [12, [4, 0.9, 0]]] }
  ],
  "shots": [
    { "from": 0, "to": 6,  "pos": [0, 6, 12], "lookAt": [0, 1, 0],
      "toPos": [0, 2, 6], "toLookAt": [0, 1.4, 0] },
    { "from": 6, "to": 12, "pos": [-8, 1, 3], "lookAt": [0, 1, 0] }
  ]
}
```

- **`shots` is required and a shot IS a cut** — cut timing is the main thing the model copies.
  `from`/`to` are seconds; `pos`/`lookAt` start the move, `toPos`/`toLookAt` end it (omit them
  for a locked-off shot). `ease` defaults to true.
- **`boxes`** are static set pieces; **`sprites`** are labelled billboards moving along `path`
  — `[[timeSeconds, [x,y,z]], …]`, interpolated. Y is up; put a standing figure at y ≈ half
  its height.
- Labels render as **small 2D screen-space text** so the model reads the blocking, and are
  dropped rather than smeared when a subject is too far away. Keep them **short and generic**
  (above).
- Solid shaded blocks, no wireframe — deliberately. *Measured:* a wireframe animatic **leaked
  its wireframe look into the generated video**; solid blocks do not.
- `--html` writes an interactive page for eyeballing the blocking before you render;
  `--frames` dumps the PNG sequence.

Two more worked plans — adapt, do not start from zero:

**Product turntable** (orbit + push-in — the commonest product ask):
```json
{ "width": 1280, "height": 720, "fps": 24, "duration": 8, "fov": 45,
  "ground": { "size": 24, "step": 2 },
  "boxes": [ { "label": "product", "at": [0, 1.0, 0], "size": [1.4, 2.0, 1.4] },
             { "label": "pedestal", "at": [0, 0.25, 0], "size": [2.4, 0.5, 2.4] } ],
  "shots": [ { "from": 0, "to": 5, "pos": [6, 2.2, 6], "lookAt": [0, 1.2, 0],
               "toPos": [-6, 2.2, 6], "toLookAt": [0, 1.2, 0] },
             { "from": 5, "to": 8, "pos": [0, 1.2, 3.2], "lookAt": [0, 1.2, 0],
               "toPos": [0, 1.2, 2.2], "toLookAt": [0, 1.2, 0] } ] }
```

**Character through a doorway** (sprite path through a gap, camera leading then following):
```json
{ "width": 1280, "height": 720, "fps": 24, "duration": 10, "fov": 50,
  "ground": { "size": 40, "step": 2 },
  "boxes": [ { "label": "wall L", "at": [-4.5, 2, 0], "size": [6, 4, 0.6] },
             { "label": "wall R", "at": [4.5, 2, 0], "size": [6, 4, 0.6] },
             { "label": "table", "at": [3, 0.5, -5], "size": [2.4, 1.0, 1.2] } ],
  "sprites": [ { "label": "woman", "size": [0.8, 1.8],
    "path": [[0,[0,0.9,7]], [4,[0,0.9,1.5]], [5.5,[0,0.9,-1]], [10,[2.6,0.9,-4.2]]] } ],
  "shots": [ { "from": 0, "to": 4.5, "pos": [0, 1.6, -3], "lookAt": [0, 1.2, 6],
               "toPos": [0, 1.6, -4.5], "toLookAt": [0, 1.2, 3] },
             { "from": 4.5, "to": 10, "pos": [-3.5, 1.8, -3.5], "lookAt": [0, 1.2, -1],
               "toPos": [-2.5, 1.8, -5.5], "toLookAt": [2.6, 1.0, -4.2] } ] }
```

**The animatic is a camera plan, not a look.** Keep the prompt's `Style & Mood:` and identity
lock complete — the reference contributes nothing but movement and timing.

---
