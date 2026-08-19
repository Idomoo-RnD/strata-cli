# Video generation — references, dialogue, shot control, editing

Advanced modes of `strata generate video`. Read
**[video-generation.md](video-generation.md)** first — the hard rule, `--realistic-human`,
prompt anatomy and chaining all live there and all still apply.

**Reminder of the hard rule:** `--ref-image` / `--ref-video` / `--ref-audio` can never be
combined with `--first-frame` / `--last-frame`.

---

## `--ref-image` — carry a character, a product or a world across clips

Give the model images of *what things are*, and it composes new shots with them. Repeatable
across a whole series in a way that a text description of a face never is.

```bash
strata generate video "<prompt referring to the references>" \
  --ref-image <character.png> --ref-image <location.png> --duration 12 -o shot.mp4
```

- **Refer to them in the prompt** — "the woman from the reference", "the product from the
  reference image". An unreferenced reference is largely ignored.
- **One subject per image**, clean and unambiguous. A reference containing two people makes
  the model guess.
- Character, wardrobe, product, location and style can each be their own reference.
- *Measured:* budget **≤4 shots per 12 s** with references attached — one fewer than a plain
  text-to-video prompt. The model spends capacity holding identity.
- A reference with a **real face, a logo or a real product** → expect `--realistic-human`
  (the CLI auto-retries and says so).

References give you *who and what*. They do **not** give you an exact opening frame — that is
`--first-frame`, and you cannot have both. Establish the look with references, then chain.

## `--ref-video` — copy a camera plan or a motion style

The model copies **shot flow, camera movement and cut rhythm** from the reference video, not
its pixels. Two uses:

**1. The user hands you a video to mimic.** Upload it if it has no URL
(`strata upload clip.mp4`), pass it as `--ref-video`, and describe *your* content in the
prompt. Say what to copy: *"match the camera movement and cut rhythm of the reference video."*

**2. You author the camera plan yourself** — see `strata sketch` below.

*Measured:* a reference **video** is exempt from the privacy pre-filter — a clip full of
photoreal people, including a face close-up, was accepted with no flag at all. (Passing
`--realistic-human` with only `--ref-video` is a hard error.)

## `--ref-audio` — voice and dialogue

Supplies a voice or an audio bed to sync to. Needs `--audio` for the result to carry sound.

For **dialogue**, write the spoken line in the prompt inside the `Audio:` section, attributed
and quoted:

```
Audio: she says, in a low even voice, "we open at six." Room tone, no music.
```

Keep spoken lines **short** — a 5 s clip holds roughly one sentence. Over-writing dialogue
makes the model rush the delivery or drop the tail of the line.

---

## `strata sketch` — author the camera plan as a 3D animatic

When a shot is complex enough that words fail — *"crane down past the sign as she crosses
left to right, cut to a low tracking shot following the car"* — build the blocking as a
grey-box animatic, render it to MP4, upload it, and feed it in as `--ref-video`. The model
copies the camera and the timing; the prompt supplies the content.

This is the CLI's own tool. **Do not rewrite a Three.js scene by hand.**

```bash
strata sketch plan.json -o plan.mp4        # also: --html plan.html  --frames dir/
strata upload plan.mp4                     # -> URL
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

- **`shots` is required and a shot IS a cut** — the cut timing is the main thing the model
  copies. `from`/`to` are seconds; `pos`/`lookAt` are the start of the move and
  `toPos`/`toLookAt` the end (omit them for a locked-off shot). `ease` defaults to true.
- **`boxes`** are static set pieces; **`sprites`** are labelled flat billboards that move
  along `path` — `[[timeSeconds, [x,y,z]], …]`, interpolated. Y is up; put a standing figure
  at y ≈ half its height.
- Labels render as **small 2D screen-space text** so the model reads the blocking. They are
  dropped rather than smeared when a subject is too far away.
- Solid shaded blocks, no wireframe — deliberately. *Measured:* a wireframe animatic **leaked
  its wireframe look into the generated video**; solid grey boxes do not.
- `--html` writes an interactive page for eyeballing the blocking before you render;
  `--frames` dumps the PNG sequence.

**The animatic is a camera plan, not a look.** Keep the prompt's `Style & Mood:` and identity
lock complete — the reference contributes nothing but movement and timing. And because it is
a `--ref-video`, the hard rule applies: no `--first-frame` alongside it.

Sketch + reference images compose well: `--ref-image` for the character and world, plus
`--ref-video` of the animatic for the camera — full control of *who*, *where* and *how it
moves*, without a single frame having to exist first.

---

## Editing and extension — LAST RESORT

Both work. Both are the **last thing to reach for** — try text-to-video, image-to-video,
keyframes and references first. Each is a fresh generation with its own failure modes, and
neither is a substitute for authoring the shot correctly.

### Editing an existing clip

Pass the source clip as `--ref-video` with an instruction prompt describing the change:

```bash
strata generate video "Change the season to winter — bare branches, snow on the ground, cold
blue light. Keep the camera move, the framing and the subject exactly as in the reference." \
  --ref-video <source url> --duration 8 -o edited.mp4
```

State what must **not** change as explicitly as what must. Global changes (grade, season,
weather, time of day) land far more reliably than local ones ("remove the cup from the table"
— the model re-generates the whole shot and the rest of the frame drifts with it).

### Extending a clip

Same shape, prompting for the continuation. ⚠ *Measured failure:* extending a clip of one
woman produced **two women** in the 4.8–7.2 s range — the model re-introduced the subject
instead of continuing her.

**Prefer chaining on `--last-frame-out` whenever person continuity matters** (see
[video-generation.md](video-generation.md#chaining--clips-longer-than-15-s)). Chaining pins
the boundary to an actual rendered frame; extension only infers it.

---

## Choosing a mode

| You have… | Use |
|---|---|
| only a description | text-to-video |
| the exact opening frame | `--first-frame` |
| both ends authored as images | `--first-frame` + `--last-frame` |
| a character/product/world to keep consistent | `--ref-image` |
| a camera plan or a clip to imitate | `--ref-video` (+ `strata sketch`) |
| a voice or a line to speak | `--ref-audio` + `--audio` |
| a clip needing a global change | editing — **last resort** |
| a clip needing more length | **chain**, not extension |
