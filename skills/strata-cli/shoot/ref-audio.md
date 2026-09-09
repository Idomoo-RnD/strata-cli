# `--ref-audio` — characters that speak your audio

Dialogue and lip-sync: what the endpoint requires, what it refuses, and how to get a performance rather than a talking still.

Part of the reference/dialogue/shot-control guide — the index, and every other part, is in [video-generation-advanced.md](../video-generation-advanced.md).

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
`strata beats` onsets ([generative-fx.md](../craft/generative-fx.md)). Never both: a clip cut to one
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
