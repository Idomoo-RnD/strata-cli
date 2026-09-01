# Video generation — references, dialogue, shot control, editing

Advanced modes of `strata generate video`. Read
**[video-generation.md](video-generation.md)** first — the hard rule, `--realistic-human`,
prompt anatomy and chaining all live there and all still apply.

**Reminder of the hard rule:** `--ref-image` / `--ref-video` / `--ref-audio` can never be
combined with `--first-frame` / `--last-frame`. Attaching any reference makes a frame an
instant `422`. Want a reference look *and* a controlled start frame? Generate the
reference-driven clip first, then chain off its last frame.

*(None of this applies to `strata generate fastvideo` — see the end of this file.)*

---

## Contents

- [🔑 How to address a reference — get this wrong and it is never cited](#-how-to-address-a-reference--get-this-wrong-and-it-is-never-cited)
- [`--ref-image` — carry a character, a product or a world across clips](#--ref-image--carry-a-character-a-product-or-a-world-across-clips)
  - [Give every image an explicit JOB](#give-every-image-an-explicit-job)
  - [Build the reference images for the job](#build-the-reference-images-for-the-job)
  - [Measured](#measured)
  - [Notes](#notes)
  - [Checklist](#checklist)
- [`--ref-video` — control the SHOTS](#--ref-video--control-the-shots)
  - [Use 1 — mimic footage the user gave you](#use-1--mimic-footage-the-user-gave-you)
  - [Use 2 — block the shot yourself with a grey-box animatic](#use-2--block-the-shot-yourself-with-a-grey-box-animatic)
  - [The strongest combination: sheets + plate + sketch](#the-strongest-combination-sheets--plate--sketch)
  - [⚠ What does NOT transfer cleanly](#-what-does-not-transfer-cleanly)
  - [Checklist](#checklist-1)
- [`--ref-audio` — characters that speak YOUR audio](#--ref-audio--characters-that-speak-your-audio)
  - [One speaker — say all three things, or it narrates instead of acting](#one-speaker--say-all-three-things-or-it-narrates-instead-of-acting)
  - [Two speakers — nail the roles down BEFORE the shot list](#two-speakers--nail-the-roles-down-before-the-shot-list)
  - [Verify the voice actually landed — don't trust your ears](#verify-the-voice-actually-landed--dont-trust-your-ears)
  - [🔇 NEVER lay the source TTS over the generated clip — use the CLIP'S audio](#-never-lay-the-source-tts-over-the-generated-clip--use-the-clips-audio)
  - [Notes and checklist](#notes-and-checklist)
  - […or use `generate avatar` instead — the decision](#or-use-generate-avatar-instead--the-decision)
  - [`--ref-audio` with MUSIC — choreograph the clip to a track](#--ref-audio-with-music--choreograph-the-clip-to-a-track)
  - [A series — same skeleton, swap the theme](#a-series--same-skeleton-swap-the-theme)
- [`strata sketch` — author the camera plan as a 3D animatic](#strata-sketch--author-the-camera-plan-as-a-3d-animatic)
- [Editing and extension — ⚠ LAST RESORT](#editing-and-extension---last-resort)
  - [🛑 Reach for these LAST](#-reach-for-these-last)
  - [Editing — change one thing, freeze everything else](#editing--change-one-thing-freeze-everything-else)
  - [Extension — the next N seconds of the same take](#extension--the-next-n-seconds-of-the-same-take)
  - [Checklist](#checklist-2)
- [Choosing a mode](#choosing-a-mode)
- [What `generate fastvideo` cannot do](#what-generate-fastvideo-cannot-do)

## 🔑 How to address a reference — get this wrong and it is never cited

| Reference | Cite it as | Limit |
|---|---|---|
| `--ref-image` | **`[Image 1]`, `[Image 2]`** — *square* brackets | ≤ 9 |
| `--ref-video` | **`<Video_1>`, `<Video_2>`** — *angle* brackets | ≤ 3 |
| `--ref-audio` | **`[Audio 1]`, `[Audio 2]`** — *square* brackets | ≤ 3 |

Numbering is **1-based, in the order the flags appear on the command line**. Mixing the
bracket styles up means the reference is silently never cited: the model still generates, it
just ignores what you attached.

---

## `--ref-image` — carry a character, a product or a world across clips

Attach images that steer subject and style, and address them as `[Image N]`. This is how you
put **your** character in **your** location without owning a single frame of the result.

```bash
strata generate video "<prompt citing [Image 1] and [Image 2]>" \
  --ref-image <character_sheet.png> --ref-image <location_plate.png> \
  --ratio 16:9 --duration 12 -o shot.mp4
```

### Give every image an explicit JOB

Name each one, say what it governs, and list its invariants — the same discipline as the
identity lock:

```text
[Image 1] is the CHARACTER SHEET and is the definitive reference for the boy in every
shot: the same very large round amber eyes behind big round glasses with thick black
rims, the same messy sandy-blond hair, the same bright orange hoodie with a white
drawstring, the same dark green cargo shorts, the same red high-top sneakers with white
soles. Match [Image 1] exactly for his face, his proportions and his wardrobe in every
single shot.

[Image 2] is the LOCATION plate and is the definitive reference for the world in every
shot: the same narrow night market alley in the rain, the same striped canvas awnings,
the same tangle of paper lanterns and hot pink, teal and amber neon, the same steaming
noodle cart, the same wet cobblestones smearing the neon. Match [Image 2] exactly for
the set, the palette and the lighting in every single shot.
```

Then keep citing them **inside the shots** — "the boy from [Image 1] small in the frame",
"straight down the alley of [Image 2]" — and restate both in the Static Description:
**state it three times**.

### Build the reference images for the job

- **Character** → a proper **model sheet**: the same character in front, three-quarter, side
  and back views on one flat grey background, same ground line, plus a couple of expression
  heads — every angle it needs, not one pose.
- **Location** → a clean **plate with NO PEOPLE in it**. Put `ABSOLUTELY NO PEOPLE, no
  characters, no figures anywhere in the frame` in the image prompt — a stray figure in the
  plate will fight the character you are inserting.
- **One subject per image**, clean and unambiguous. A reference containing two people makes
  the model guess.

Both are ordinary `strata generate image` calls, and their printed `url:` goes straight into
`--ref-image`.

### Measured

Character sheet + location plate, 5-shot prompt, 12 s, 720p, 16:9, `--audio`:

- **1280×720, 12.05 s, 6.0 MB**, generation **259 s**.
- **Location transfer is extremely faithful** — not "a neon market" but *that* market: the
  same signage, awnings, noodle cart and reflections. The opening wide read as the plate
  itself with the character walked into it.
- **Character transfer held**: glasses, amber eyes, hair, hoodie, cargo shorts, high-tops.
- ⚠ **It dropped a shot — 4 of the 5 requested.** The same model kept all 5 in text-to-video
  *and* image-to-video; with two references it lost the medium tracking shot. **Budget ~4
  shots per 12 s with references**, not 5.
- ⚠ **A small prop drifted.** The red backpack is on the sheet and named in the prompt, yet it
  vanished in the final walking-away shot. **Wardrobe survives; small accessories do not.** If
  a prop matters, give it its own shot and its own sentence, or accept losing it.

### Notes

- **Set `--ratio` explicitly.** There is no input frame to infer from — do not leave it on
  `adaptive` here.
- A reference with a **real face, a logo or a real product** → expect `--realistic-human` (the
  CLI auto-retries and says so).
- References give you *who and what*, never an exact opening frame.

### Checklist

- [ ] **No** `--first-frame` / `--last-frame` — hard `422`, whichever reference type you attach
- [ ] ≤ 9 images, addressed as `[Image N]`, 1-based in flag order
- [ ] Every image given an explicit JOB and its invariants listed
- [ ] References re-cited inside the shots AND in the Static Description
- [ ] Character reference is a multi-view model sheet, not one pose
- [ ] Location plate generated with "no people" stated in its prompt
- [ ] `--ratio` set explicitly — nothing to infer from
- [ ] ~4 shots per 12 s, not 5
- [ ] Any must-keep prop given its own shot and its own sentence

---

## `--ref-video` — control the SHOTS

A reference video hands the model the thing a prompt is worst at conveying: **the edit**. Cut
timing, shot sizes, camera height and camera movement all transfer. Cite as `<Video_1>` —
**angle** brackets. Two ways to use it.

### Use 1 — mimic footage the user gave you

Cite it as the shot plan and **write no shot list of your own** — if you enumerate shots you
are overriding the very thing you attached:

```text
MIMIC <Video_1> AS THE SHOT PLAN. Copy the edit of <Video_1> exactly: the same number
of shots in the same order, the same shot sizes in the same sequence, the same cut
rhythm and timing, and the same camera behaviour in each shot. Do not invent a
different structure. Only the subject and the location change; the shot flow is
<Video_1>'s.
```

*Measured*, with a 4-shot reference and a completely different subject:

| | reference | output |
|---|---|---|
| cuts | 1.500 / 3.458 / 6.917 s | **1.500 / 3.458** / 8.000 s |
| shot progression | wide → macro detail → face ECU → departing wide | **identical, 1:1** |

The first two cuts came back **frame-identical**, while subject and location changed completely.

Upload the footage first if it has no URL (`strata upload clip.mp4`). *Measured:* a reference
video is exempt from the privacy pre-filter — photoreal people, including a face close-up,
were accepted with no flag. (`--realistic-human` with only `--ref-video` is a hard error.)

### Use 2 — block the shot yourself with a grey-box animatic

When *you* want the camera plan, build it with `strata sketch` (below) and feed the render in
as `--ref-video`.

**The instruction that makes it work is telling the model what the reference _isn't_.**
Without this it copies the grey untextured look:

```text
<Video_1> IS A GREY-BOX ANIMATIC - A CAMERA AND STAGING PLAN ONLY. It is untextured
geometry and flat blocks on a plain background. Each labelled block is a PLACEHOLDER
with its name written on it, standing exactly where that thing stands and moving exactly
along the path that thing moves. IGNORE the look of <Video_1> completely - ignore the
background, the grey blocks and the text labels. Take from <Video_1> ONLY:
- the number of shots and the exact moments it cuts;
- the camera position, height, angle and lens for each shot;
- the camera movement inside each shot, at the same speed;
- the ground position and travel path of every labelled placeholder.

REPLACE EACH LABELLED BLOCK WITH THE REAL THING, at the same position, the same scale
and following the same path:
- the placeholder labelled BOY is the boy from [Image 1];
- KERB, BUILDING, TRAFFIC LIGHT and BUS STOP are those features of [Image 2].
```

Close the Static Description with the negative too: *"fully textured and fully lit, nothing
grey, no placeholder blocks, no text on screen."* *Measured:* zero leakage — no blocks, no
labels, no plain background in the output.

*Measured*, a 3-shot sketch with cuts designed at 2.00 / 4.00 s:

- output cut at **2.042 / 3.958 s** — within **one frame** at 24 fps;
- all three camera setups reproduced (low wide push-in → high top-down → side medium arc);
- the **box mapping was honoured**: a small box on the table became the pocket watch, two tall
  boxes at the back became shelves of clocks flanking frame left and right, at the same scale
  and position.

### The strongest combination: sheets + plate + sketch

References compose. Character sheet as `[Image 1]`, location plate as `[Image 2]`, animatic as
`<Video_1>`: the images say **who and where**, the video says **how it is shot**.

*Measured* on a 10 s street-crossing sketch: character came off the sheet (glasses, hoodie,
sneakers, backpack), world came off the plate (zebra stripes, pedestrian light, bus shelter,
shopfronts), and the first cut matched at **3.500 s exactly**.

### ⚠ What does NOT transfer cleanly

- **Look leaks from the reference.** With real footage as the reference, a macro insert came
  back on the *reference's* wet cobbles rather than the new location's surface. State the
  intended surfaces and palette in the Static Description.
- **Style breaks on close inserts.** Macro and top-down inserts came back with a near-photoreal
  adult hand instead of the cartoon character's hand, even while the wides and the face
  close-up stayed on-model.
- **Extreme camera positions get moderated.** A near bird's-eye (camera 13 m up) came back as a
  modest raised three-quarter angle. Design the sketch within believable camera heights.
- **Per-actor placement is a hint, not a constraint.** Staging held once the actors were the
  *subject* of the shot; in the establishing wide the crossing came back empty in the
  foreground with figures far down the street. Do not rely on the animatic to place extras.
- **Cut timing drifts on later cuts.** The first cut matched exactly in every test; the last
  cut lands anywhere from 0.6 s early to 1.1 s late. **Trust cut 1; treat the rest as
  approximate.**
- **Sprite labels can leak as on-screen TEXT.** *Measured:* a 5 s run rendered the word `KERB`
  onto the asphalt despite "no text on screen" in the Static Description — the same sketch at
  10 s did not. The negative reduces this, it does not guarantee it. **Check the output for
  stray label text**, and keep labels short and generic.
- **Slowest mode.** *Measured:* 315 s, 357 s, 367 s and once 541 s, versus 139 s for keyframes.

### Checklist

- [ ] Cited as `<Video_1>` — **angle** brackets
- [ ] **No** `--first-frame` / `--last-frame` (reference media, hard `422`)
- [ ] ≤ 3 reference videos
- [ ] Mimicking footage? Then write **no shot list** — the reference is the shot list
- [ ] Using an animatic? "IGNORE the look of `<Video_1>` completely" + an explicit list of
      what to take from it
- [ ] Every placeholder mapped by label to a real thing, ideally to `[Image N]`
- [ ] Static Description ends with the negative: nothing grey, no blocks, no text on screen
- [ ] Camera heights in the sketch kept believable
- [ ] Surfaces and palette stated, so the reference's look does not leak in
- [ ] Only cut 1 relied on for exact timing
- [ ] Output checked for stray label text burned into the picture

---

## `--ref-audio` — characters that speak YOUR audio

Attach up to **3** tracks and address them as `[Audio 1]`, `[Audio 2]`, … The model
**lip-syncs a character to a recording you supply**, so you control the voice instead of
accepting whatever it invents — which is what makes branded spokes-characters and real
dialogue possible.

> ⛔ **Audio never travels alone — it needs a visual reference to attach the voice to.**
> *Measured 2026-08-25:* `--ref-audio` with no `--ref-image`/`--ref-video` is rejected —
> `422 "Reference audio requires at least one reference image or video"`. So a talking
> person is always **two references**: a portrait or character sheet of the speaker in
> `--ref-image` (+ `--realistic-human` when photoreal) and the recording in `--ref-audio`.
> The CLI now refuses a bare `--ref-audio` before spending the request.

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
because `--audio` mixes ambience underneath: **>0.7 means it is your recording**, not a
re-performance.

### 🔇 NEVER lay the source TTS over the generated clip — use the CLIP'S audio

This is the general rule for **any clip in which someone speaks** — whether the voice came from
`--ref-audio`, from a `{line}` in a plain text-to-video prompt, from an avatar, or from the user's
own footage: the clip's audio is the performance. Generating a narration of the same words and
laying it on top cannot sync.

Once a TTS track has been used as `--ref-audio`, **the generated clip's own audio is the
deliverable.** Put that clip in the scene with its audio intact and do **not** add the
original `.mp3` as a separate `audio` layer — doubling it means two voices.

The speech lands **1.3 s, 3.8 s and 6.3 s into the clips** — never at 0. The model places each
line against the picture it generated and paces the delivery to the performance, so the words
sit at a different time inside the clip than in the source file. Laying the `.mp3` at `t=0` is
seconds early, and **no fixed offset repairs it**, because the internal pacing is re-timed
too, not just shifted.

So:

- generate the clip **with `--audio`** — that is what carries the lip-synced voice;
- keep that track through every ffmpeg step ([video-editing.md](video-editing.md) rule 3 —
  the first `-map` silently drops it);
- reference the **video** in the scene, and give it **no** competing `audio` layer;
- if the voice needs to be louder or cleaner, treat it in the clip's own track
  (`-af "volume=…"`), never by re-adding the source.

**One line, one place.** A TTS file is either an *input to the generation* or a *layer in the
scene* — never both.

### Notes and checklist

- [ ] `--ref-audio` is paired with `--ref-image` (or `--ref-video`) — audio alone is a `422`

- **Size the clip to the speech.** A 3.6 s line in a 12 s clip leaves the model inventing
  filler — budget line-length + 2–3 s of setup per speaker.
- Shot budget holds up here: 3/3 and 4/4 delivered with references attached. Stay at **≤4**.
- Pick contrasting voices (age, accent) so the audience can tell who speaks without the face.

- [ ] TTS generated first; its **URL** passed to `--ref-audio` (≤3)
- [ ] **No** `--first-frame` / `--last-frame` — reference media, hard `422`
- [ ] Each audio assigned to exactly ONE character, in caps, before the shot list
- [ ] The spoken line in `{curly braces}`, identical to the TTS text
- [ ] "lip-synced … every syllable, every pause" demanded explicitly
- [ ] Each speaker given their OWN close-up, held for most of the line
- [ ] For dialogue: "never speak at the same time", and each shot states who is silent
- [ ] `Audio:` says what sits underneath, and "No other voices"
- [ ] Clip length ≈ speech length + setup; ≤4 shots
- [ ] Voice presence verified by envelope correlation (>0.7)

### …or use `generate avatar` instead — the decision

Both make a person talk. They are not interchangeable:

| | `generate avatar` | `generate video --ref-image --ref-audio` |
|---|---|---|
| what it is | ONE still, lip-synced to an exact track — a presenter holding a pose | a character who **acts**: moves through a scene, multiple shots, two speakers |
| framing | fixed by the still | the model composes shots |
| verbatim script | ✅ the track IS the audio | ✅ lip-synced to your TTS (verify >0.7) |
| **personalized scenes** | ✅ **the right answer** — a stable presenter plate is what Idomoo swaps by layer name | ⚠ a re-generated performance is not a swappable plate |
| **non-personalized presenter** | a talking still — fine for a fixed plate or a quick cut | ✅ **the default** — a directed, filmed shot |
| cost | one still + one call | a character sheet, TTS, and a 3–9 min generation |

**Presenter, spokesperson, host, testimonial → `generate video --ref-image --ref-audio`.**
**Personalized, or the user wants a fixed plate / a quick cut →
`generate avatar`.** State the route and its trade-off in the storyboard either way — the full
decision lives in [avatar.md](avatar.md).

---

### `--ref-audio` with MUSIC — choreograph the clip to a track

The same input, the other job: attach a music track and the model **cuts and moves to it**.
This is the strongest way to get a beat-locked clip without post — the edit points come out
already on the music. Four moves, in this order, every one load-bearing:

1. **Assign the job up front, before anything else:** `Use [Audio 1] as the music track for
   the entire video, and choreograph everything to it.`
2. **Tie the motion to the phrasing, in CAPS:** `HIS MOVEMENT FOLLOWS THE MUSIC IN [Audio 1]:
   every gesture rises and falls with the music's phrasing — he expands as the music swells,
   softens and settles as it quiets.`
3. **End every shot with `Cut on the beat.`** instead of `Cut.` — and say it once more in
   prose: *"each camera CUT lands on a musical beat or transition in [Audio 1] so the edit is
   locked to the track."*
4. **Close `Audio:` with the track as the whole soundtrack:** *"use [Audio 1] as the full
   soundtrack throughout, with only the faintest natural breath and fabric rustle beneath it.
   No narration, no voice."* Needs `--audio`.

The last shot should **land with the music's closing phrase** — say so — or the clip ends
mid-bar. The rest of the prompt is the ordinary anatomy: identity lock ×3, double-contrast
shots, `Static Description:`. Worked in full on a tai-chi piece — the measured result was the
"super good" one.

When the music is *yours to place* rather than the model's to cut to — a VASCO scene with a
bed under it — do the opposite: generate **without** a track and cut in the scene on
`strata beats` onsets ([generative-fx.md](generative-fx.md)). Never both: a clip cut to one
track and then laid over another fights itself.

### A series — same skeleton, swap the theme

For a set of clips that must read as one family (a campaign, a character's week, a product
line), keep **`Style & Mood:`, the location, the identity lock and `Static Description:`
byte-identical** across every prompt, and change only two things: the one-line **theme in
CAPS** and the **five shots** that express it.

> `taichi_A` — about WEIGHT and ROOTEDNESS (low angles, bare feet, palms pressing down)
> `taichi_B` — about CIRCLES and ROTATION (orbit arc, spiral turn, rim light from behind)
> `taichi_C` — about RISING and OPENING (bird's-eye opener, arms unfold, camera cranes up)

Same seed across the series if you want the look to hold even tighter (`--seed`). The
identical skeleton is what makes three clips look like they came from one shoot.

## `strata sketch` — author the camera plan as a 3D animatic

When a shot is complex enough that words fail — *"crane down past the sign as she crosses left
to right, cut to a low tracking shot following the car"* — build the blocking as a grey-box
animatic, render it to MP4, upload it, and feed it in as `--ref-video` with the "IGNORE the
look" instruction above.

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

## Editing and extension — ⚠ LAST RESORT

Put the source clip in `--ref-video` and cite it as `<Video_1>`; the prompt either **changes**
it (edit) or **continues** it (extension).

### 🛑 Reach for these LAST

Both are the slowest mode (~300–330 s *measured*), both re-generate the whole clip rather than
touching the original, and both give less control than every mode above. **Exhaust the other
features first**, in this order:

| Want to… | Use this instead | Why it beats editing/extension |
|---|---|---|
| A different look / colour / material | **Re-generate** with the prompt you actually want | Same cost, full control, no drift from a source |
| Keep a character across clips | **`--ref-image`** | Purpose-built for identity; cheaper to steer |
| Keep a *world* across clips | **`--ref-image`** with a location plate | Same |
| Continue a shot with EXACT continuity | **Chain on `--last-frame-out`** | *Measured:* pixel-exact handoff, no identity drift |
| Reuse the camera plan | **`--ref-video` as a shot plan** | You keep authorship of the content |
| Fix a content rejection | **`--realistic-human`** | A flag, not a re-generation |

Only when none of those fits — the user hands you a finished clip and wants **that clip**
changed or continued — is this the tool.

### Editing — change one thing, freeze everything else

*Measured.* Source: a studio watch clip. Instruction: brushed steel → polished gold, change
nothing else.

| At | Source | Edited |
|---|---|---|
| 1.5 s | steel case, navy dial | **gold case**, navy dial, same pose |
| 5.0 s | macro, steel hands | **gold hands and markers**, same crop |
| 8.5 s | pulled back with reflection | **gold**, same framing, same reflection |

It held the boundary exactly: case, bezel, crown and hands turned gold while the **dial stayed
navy and the strap stayed dark leather**, as specified. Framing, camera moves, background and
reflections were unchanged at every matched timestamp.

What made it work — the prompt is a **freeze list plus one change**:

```text
EDIT <Video_1>. Keep EVERYTHING about it identical - the same three shots, the same cut
points, the same camera moves at the same speed, the same framing, the same lighting
setup, the same background and the same reflections.

CHANGE ONE THING ONLY: <the change, described physically>.

Change nothing else. Do not re-time it, do not re-frame it, do not add or remove a shot,
do not move the camera differently.
```

Then repeat the invariants in the Static Description. **One change per call** — a list of
edits is a re-generation wearing a disguise, so write the prompt you want instead. Global
changes (grade, season, weather, material, time of day) land far more reliably than local ones
("remove the cup from the table" re-generates the whole shot and the rest drifts with it).

### Extension — the next N seconds of the same take

*Measured:* a 10 s continuation of a 12 s clip. **0 cuts**, opens on the source's final
framing, and the grade, lens and location carry over without a frame handoff.

```text
CONTINUE <Video_1>. This video is what happens NEXT, immediately after its final frame -
the same subject, the same location, the same light, the same lens and grade. It must
feel like the next ten seconds of the same take, not a new video.
<then: what happens, physically, in order>
Match <Video_1> exactly for <the identity invariants>.
```

⚠ **Extension duplicated the subject.** *Measured:* between ~4.8 s and ~7.2 s the clip
contained **two women** — one in a dark top, one in the olive shirt from the source — before
resolving back to one as she exited frame. The continuation was otherwise correct.

So identity is **not** guaranteed through a large action:

- **Continuity of a PERSON matters → chain on `--last-frame-out`**, which gave a pixel-exact
  handoff with no duplication.
- **Reference-video extension** carries the whole clip's motion, grade and feel — use it when
  the *look* must continue and the action is small.
- The duplication appeared during the **largest movement**. If you must extend a person shot,
  keep the duration short and the action simple, and **check the middle of the clip**.

### Checklist

- [ ] Every other feature ruled out first (table above) — this is the last resort
- [ ] Source clip in `--ref-video`, cited as `<Video_1>`
- [ ] EDIT: an explicit freeze list, then exactly **ONE** change, then "change nothing else"
- [ ] EDIT: invariants repeated in the Static Description
- [ ] EXTEND: "what happens NEXT, immediately after its final frame … the same take"
- [ ] EXTEND: identity invariants restated, duration short, action simple
- [ ] EXTEND: **middle of the clip checked for a duplicated subject**
- [ ] Budget ~300 s — the slowest mode

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
| a person delivering a script to camera | `generate avatar` |
| a clip needing a global change | editing — **last resort** |
| a clip needing more length | **chain**, not extension |
| speed matters more than control | `generate fastvideo` — fast mode only |

## What `generate fastvideo` cannot do

`strata generate fastvideo <image>` is the OLD image-to-video path, used **only when fast mode
is explicitly asked for**. Nothing on this page applies to it: no references, no
`[Image N]` / `<Video_1>` citations, no keyframes, no `--audio`, no `--last-frame-out`, no
`--realistic-human`, no shot list — just one image plus a short motion line. It does accept a
**local file** (auto-encoded), unlike `generate video`, and being a single continuous move
with no cuts it is safe for a `.jet` overlay clip ([video-generation.md](video-generation.md)).
