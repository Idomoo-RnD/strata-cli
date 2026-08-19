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

## `--ref-audio` — characters that speak YOUR audio

Attach up to **3** tracks and address them as `[Audio 1]`, `[Audio 2]`, … The model
**lip-syncs a character to a recording you supply**, so you control the voice instead of
accepting whatever it invents. This is what makes branded spokes-characters and real dialogue
possible. Reference media, so the hard rule applies — no `--first-frame`/`--last-frame`.

```bash
strata generate voices                                     # pick a voice_id
strata generate narration "<the line>" --voice <voice_id>  # prints a hosted URL
strata generate video "<prompt below>" --ref-image <char.png> \
  --ref-audio <that url> --duration 8 --audio -o line.mp4
```

*Measured:* `generate narration` returns an `assets-temp.idomoo.ai/voiceover/….mp3` URL the
video API accepts **directly** — no download, no re-upload.

### One speaker — say all three things, or it narrates instead of acting

1. **Assign the audio a job, in caps, before the shot list:**
   `USE [Audio 1] AS THE CHARACTER'S VOICE FOR THE ENTIRE VIDEO.`
2. **Demand the sync explicitly:** *"The boy SPEAKS the words in [Audio 1] out loud, on
   camera, and his mouth, jaw and face must be lip-synced to that exact recording — every
   syllable, every pause, matching the timing of [Audio 1] exactly. He is the one talking."*
3. **Write the line in `{curly braces}`** — `His line is: {Whoa... look at all these lights!}`
   — and keep it **identical** to the TTS text, or lip-sync and audio disagree.

Then give the line **its own close-up**, held for most of it: *"CLOSE-UP on his FACE and MOUTH
filling the frame … his MOUTH clearly forming every word of [Audio 1] in perfect lip-sync."*
A wide shot wastes the performance. Close `Audio:` with what sits underneath — *"use [Audio 1]
as the boy's spoken voice for the whole video, in sync with his mouth. Underneath it only
gentle rain. No music. No other voices."*

*Measured* (1 character sheet + 1 location + 1 TTS, 8 s, 3 shots): **3/3 shots delivered**,
159 s. The mouth articulated distinctly across the line — an "O" on *"Whoa"*, open jaw
mid-sentence, a grin on the last words.

### Two speakers — nail the roles down BEFORE the shot list

Otherwise both voices bleed onto both faces:

```text
THIS IS A TWO-CHARACTER DIALOGUE AND EACH CHARACTER HAS HIS OWN VOICE RECORDING.
[Audio 1] is the BOY'S voice ONLY. [Audio 2] is the VENDOR'S voice ONLY. Each
character's mouth, jaw and face must be lip-synced to HIS OWN recording exactly,
syllable by syllable, and the two never speak at the same time - the boy speaks
first, then the vendor answers.
The boy from [Image 1] says, in the voice of [Audio 1]: {Wow! What is that smell?}
Then the vendor from [Image 2] answers, in the voice of [Audio 2]: {That, my young
friend, is the finest ramen in the entire city.}
```

Then **one close-up per speaker**, and in each say who is *not* talking — *"the VENDOR does
not speak in this shot."*

*Measured* (3 reference images + 2 TTS tracks, 12 s, 4 shots): **4/4 delivered**, 234 s. Both
characters matched their sheets and each spoke in his own close-up.

### Verify the voice actually landed — don't trust your ears

Correlate the TTS envelope against the rendered audio:

```bash
ffmpeg -i vo.mp3  -f s16le -ac 1 -ar 16000 t.raw
ffmpeg -i out.mp4 -f s16le -ac 1 -ar 16000 v.raw   # then slide t over v, best normalised correlation
```

*Measured:* one speaker **0.73**; dialogue **0.80** (boy, at 3.8 s) and **0.90** (vendor, at
6.3 s) — each inside their own close-up, so the assignment worked. It never reaches 1.0
because `--audio` mixes ambience underneath: **>0.75 means it is your recording**, not a
re-performance.

### Notes

- **Size the clip to the speech.** A 3.6 s line in a 12 s clip leaves the model inventing
  filler — budget line-length + 2–3 s of setup per speaker.
- Shot budget holds up here: 3/3 and 4/4 delivered with references attached. Stay at **≤4**.
- Pick contrasting voices (age, accent) so the audience can tell who speaks without the face.

### …or use `generate avatar` instead — the decision

Both make a person talk. They are not interchangeable:

| | `generate avatar` | `generate video --ref-image --ref-audio` |
|---|---|---|
| what it is | ONE still, lip-synced to an exact track — a presenter holding a pose | a character who **acts**: moves through a scene, multiple shots, two speakers |
| framing | fixed by the still | the model composes shots |
| verbatim script | ✅ the track IS the audio | ✅ lip-synced to your TTS (verify >0.75) |
| **personalized scenes** | ✅ **prefer this** — a stable presenter plate is what Idomoo swaps by layer name | ⚠ a re-generated performance is not a swappable plate |
| cost | one still + one call | a character sheet, TTS, and a 3–9 min generation |

**Piece to camera, or anything personalized → `generate avatar`** ([avatar.md](avatar.md)).
**Talking inside a scene, dialogue, a character who moves → `generate video`.**

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
