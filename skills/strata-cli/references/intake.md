# Intake — what to do when the user hands over material

**Read this FIRST whenever the request comes with inputs** — a storyboard image, a script, a
shot list, reference sheets, footage, product photos, a voice, a brand kit. The rest of the
skill assumes the piece is being invented; this file covers the case where the piece already
exists on paper and the job is to *execute* it faithfully. Skipping intake is how an agent
ignores a finished storyboard and improvises something else.

**The supplied material is the brief.** Every note, timecode, line of dialogue and
continuity instruction in it outranks the skill's own craft defaults ([anti-slop.md](anti-slop.md)'s
first rule: the brief's words always win). Execute it; do not redesign it. Where it
leaves an axis free, the skill's craft applies as usual.

---

## Contents

- [0. Open everything, and say what it changes](#0-open-everything-and-say-what-it-changes)
- [1. Recognise what was handed over](#1-recognise-what-was-handed-over)
- [2. The shot table — the one artefact intake produces](#2-the-shot-table--the-one-artefact-intake-produces)
- [3. Route every scene to a mode](#3-route-every-scene-to-a-mode)
- [4. Build the production bible, then execute in storyboard order](#4-build-the-production-bible-then-execute-in-storyboard-order)
- [5. Assemble to the timecodes](#5-assemble-to-the-timecodes)
- [Worked example — a supplied 30 s commercial storyboard](#worked-example--a-supplied-30-s-commercial-storyboard)

## 0. Open everything, and say what it changes

**Every file, link or folder the user attaches is opened and read before anything is
planned** — images with vision, PDFs page by page, URLs fetched, footage probed and
frame-sampled, audio listened to via `strata captions`. Not skimmed, not inferred from the
filename. For each one, write down in the intake summary **what it is and what it changes
in the video**: the brief, the brand atoms, the cast, the copy, the timing, the look, the
deliverable format. An input that changes nothing is stated as such (*"the deck's slides 4–9
are internal, no effect on the spot"*) — so the user can correct me before a render, not
after. An unopened attachment is the single most expensive intake failure: the user sent
the answer and I generated a different one.

## 1. Recognise what was handed over

| Input | What it is | What I extract → what it changes |
|---|---|---|
| **Storyboard image** (frames in a grid with captions, timecodes, dialogue, a notes block) | the whole piece, decided | the shot table below, one row per scene, plus every note verbatim |
| **Script / VO doc** | the words and their order; timing implied by reading speed | dialogue per beat, who speaks, tone; timecodes from `strata captions` once TTS exists |
| **Shot list / spreadsheet** | scene structure without pictures | the shot table; storyboard the visuals for sign-off |
| **Character / product reference sheets** | the cast and props, decided | the production bible ([production-bible.md](production-bible.md)) — never regenerate what was supplied |
| **Material the notes cite but the folder lacks** (sheets, a logo, fonts, footage) | a gap in the brief, not a licence to invent | ask for it first, in the intake summary; if it is not coming, build a stand-in from the material's own frames (`generate image --reference` on a crop) and flag that the supplied one replaces it before the first clip that cites it |
| **Footage / photos** | plates and subjects that must appear | which scenes they cover; `strata captions` / `strata beats` for their timing; `.jet` if a subject must sit over a layer |
| **A brand kit / `.brand/brand.md`** | the atoms | [brand.md](brand.md) — it governs everything below |
| **A previous cut / `.idm`** | a version to continue from | `strata inspect` reads its embedded version; `strata versions` if the folder has history ([format.md](format.md)) |
| **PDF** | could be *any* of the above — a storyboard, a script, a brand book, a content deck, a product spec, a treatment | read every page; classify each page or section into one of the rows above and extract accordingly. A brand-book PDF → [brand.md](brand.md) atoms (palette, type, logo rules) written into `.brand/brand.md`; a deck → the copy and the claims; a treatment → the arc and tone |
| **URL** | a content page, a product page, a brand site, a reference video, a shared doc | fetch it and read it as the document it is: product page → the product's real name, claims, look and price for the copy and the hero shots; brand site → the atoms; a reference video → the motion signature ([reference-styles.md](reference-styles.md)) and, if the user wants its *edit*, `--ref-video`; a doc link → its content as a script or brief |
| **Product photos / logos** | the hero object and the mark | the product sheet in the bible — real marks need `--realistic-human`; a photo becomes a `.jet` if it must sit over layers ([assets.md](assets.md)) |
| **A voice sample** | the voice the brand uses | it is the `--ref-audio` / avatar audio, never re-voiced with a TTS |

Then the **effect map** — one line per input, before any plan:

```markdown
| Input | Kind | Changes: brief · brand · cast · copy · timing · look · format | How |
```

e.g. *storyboard.png · storyboard · brief, cast, copy, timing · every scene, all dialogue,
:30 TV* / *brand.pdf · brand book · brand, look · palette + Helvetica Neue + logo clear-space
→ .brand/brand.md* / *product-page URL · content · copy, cast · real product name and the
three claims; hero photo for the product sheet*.

A storyboard **image** is read with vision, frame by frame, left to right, top to bottom.
Transcribe rather than summarise: captions verbatim, timecodes exact, dialogue in quotes,
the notes block whole. If a frame is ambiguous, say so in the table rather than guessing. A
cell whose first and last frames are two different setups (an interior, then an exterior) is
two sub-shots: split the row, divide its time, and say so — one clip cannot cut inside itself
without a montage verb, and a montage verb cuts everywhere.

## 2. The shot table — the one artefact intake produces

```markdown
| # | Scene | In–Out | First frame | Last frame | On screen | Dialogue / VO | Continuity notes |
```

One row per scene, in order, from the material — not from me. Then three fields for the
whole piece: **duration and format** (":30, 16:9, TV"), **voices** (who speaks, and only
them), **continuity priorities** (the material's own ranking — a storyboard that says
*"the X is the highest-priority continuity element"* has told me where the risk is).

This table goes into the storyboard step (SKILL.md step 2) **as the storyboard** — I present
it back with my execution plan attached (mode per scene, assets to build), not a re-imagined
version. Sign-off is on the plan, because the creative is already signed.

## 3. Route every scene to a mode

The material decides the mode, scene by scene, through one constraint the API enforces:
**frames and references are mutually exclusive** ([video-generation.md](video-generation.md)).
So a scene is either *frame-driven* (exact pictures) or *reference-driven* (consistent cast),
never both in one call — and the way to get both is to put the consistency into the
**frames themselves** by generating them as images with references.

| The scene has… | Mode | Why |
|---|---|---|
| **dialogue on camera** (someone visibly speaks) | `generate video --ref-image <sheet> --ref-audio <tts>` | lip-sync needs the audio reference; consistency comes from the sheet. The talking-person recipe: [video-generation-advanced.md](video-generation-advanced.md#--ref-audio--characters-that-speak-your-audio) |
| **first + last frame given, no speech** | generate both frames as **images with `--reference` to the sheets**, then `--first-frame` + `--last-frame` | exact pictures, one continuous shot, consistency baked into the stills ([video-generation.md](video-generation.md), Keyframe interpolation) |
| **one frame given, an action described, no speech** | frame as an image with references → `--first-frame` + the action | the still locks the look; the prompt supplies the motion |
| **a reaction / listening shot** (no words) | as above — frames, never audio | a listener is a frame-driven shot; do not attach `--ref-audio` to a non-speaker |
| **establishing / wide with no principal close** | text-to-video or `--ref-image` for the location plate | no face to protect; the plate carries the set |
| **product hero** (the object is the shot) | `--ref-image <product sheet>` + `--realistic-human` for logos | the product sheet is the continuity element |
| **end card / titles / lower-thirds / captions** | a **VASCO scene**, never generated | type is authored, not generated ([format.md](format.md), [blocks.md](blocks.md)) |
| **VO over pictures** (narrator, not on camera) | any of the above **without** `--ref-audio`; the narration is an audio layer in the scene | `--ref-audio` is for a mouth on screen — [SKILL.md](../SKILL.md)'s clip's-audio rule |

Off-screen dialogue *during* a frame-driven scene (a preacher heard while we watch the
card) is **VO over pictures**: the clip is silent, the line is laid in the scene.

## 4. Build the production bible, then execute in storyboard order

Before any clip: the cast, props and locations as reference sheets with **one canonical
identity block each**, reused verbatim in every prompt — [production-bible.md](production-bible.md).
Then generate **in waves, not scene by scene** ([assets.md](assets.md), *Generate in
waves*): all sheets, plates and TTS at once; then every scene whose inputs exist, at once;
then only the continuations. Each clip is checked against its row (the frame, the action,
the line) as it lands, and the VASCO scene is authored while the waves run. A clip that comes back short is covered
with more shots, never stretched ([video-generation.md](video-generation.md)).

## 5. Assemble to the timecodes

The material's in/out points are the edit. Clips land as `video` layers at those times;
the end card is authored; dialogue that is VO sits as audio layers; captions come from
`strata captions` on the delivered clips, not from the script (the performance decides
timing). Then the ordinary gates: `validate`, `preview --grid`, `snapshot`, the Definition
of Done.

- **A board's timecodes round to frames.** Tenth-of-a-second in/out points do not land on
  24 or 25 fps frames; round each cut to the nearest frame (≤ 21 ms at 24 fps), keep the
  running total exact, and state the rounding in the summary. Pick the scene fps from the
  clips ([assets.md](assets.md), *Scene fps follows the footage*).
- **Trimming a talking clip to its cell.** The model's minimum is 4 s and the line lands
  1.3–6.3 s in, never at 0 — so run `strata captions` on the delivered clip, take the window
  that opens on the board's first-frame beat and closes ≥ 0.2 s after the line's `t1`, and cut
  exactly the cell's length with `ffmpeg -ss <t0> -i clip.mp4 -t <len>` — no `-map`, so the
  in-clip voice survives ([video-editing.md](video-editing.md)). If the line cannot fit the
  cell, the slack comes from an adjacent silent cell and the summary says which; a clip is
  never retimed.

## Worked example — a supplied 30 s commercial storyboard

A storyboard image with 10 scenes + end card, first/last frames, dialogue and a notes block
reading *"use supplied character and product reference sheets for all principal elements;
voices: PREACHER, GROOM, AGENT only; no music; the product-card 'bride' is the highest-
priority continuity element"* resolves to:

- **Bible:** three character sheets (groom, preacher, agent), one product sheet (the veiled
  card on its easel — the priority element, so it gets its own sheet *and* its own sentence
  in every prompt), two location plates (altar, office). Identity blocks written once.
- **Modes:** scenes where the preacher or agent visibly speaks → `ref-image + ref-audio`
  with that character's TTS; the groom's tearful close-ups (listening, no words) → first/last
  frames as images with references → keyframe interpolation; the card-bride hero shot →
  product sheet reference, VO laid in the scene; the getaway → first frame + action; the end
  card → a VASCO scene with the three icons as authored layers.
- **Assembly:** eleven layers at the storyboard's timecodes, three TTS tracks placed under
  their scenes, no music (the notes said so — do not add one).
