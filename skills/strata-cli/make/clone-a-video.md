# Clone a reference video — the procedure

Reproducing a piece the user hands over ("this video, but ours") with the tools this skill already has: read it, write it down, keep its structure, swap its content, rebuild, re-anchor, prove.

Part of the workflow — the seven steps are in [workflow.md](../workflow.md); the intake it extends is [intake.md](intake.md). The route that lands here is [routes/clone.md](../routes/clone.md).

## What a clone is, and is not

A clone borrows a piece's **structure and technique**: its argument, its order of beats, where the
eye goes, what enters on which line, its rhythm, its layout system. It never borrows **assets or
identity**: their footage, their logo, their voice, a recognisable face, their brand atoms. Those
stay out, and [upload policy](../shoot/upload.md) applies to the reference clip itself — it is
uploaded only for `--ref-video`, only with informed consent for a non-sensitive clip, and never
otherwise. "Make it feel like X" is a different job: a *signature* applied to a new piece
([feel-like.md](../routes/feel-like.md)). A clone keeps the piece and replaces what is in it.

## 1. Read the reference, then write it down — before any authoring

Open it. Watch it once at speed, once muted, once frame-stepped at the cuts. Then measure:

```bash
strata deconstruct reference.mp4 -o ref_tokens.json        # cuts, shot lengths, motion energy per shot, palette, onsets, bpm, loudness
strata review reference.mp4 -o ref_review/                 # contact sheet, cut strips, loudness arc — what enters at each cut
strata captions reference.mp4 --allow-public-upload -o ref_cues.json   # what is said and when — phrase-level; consent first
```

Three notes go into the project, and they are what the clone is built from:

- **Analysis** — why it works, in one page: the hook and what it promises, the argument and its
  order, the signature (the one move that makes it this piece), the layout system, the sound's
  job. Say what the viewer feels, learns or decides, and at which beat.
- **Timeline** — the shot table of [intake.md](intake.md) with three more columns: the **phrase**
  the beat lands on (from the cues), what **enters** and what **leaves**, and the beat's
  **job**. One row per beat, timecodes from the tokens, not by eye.
- **Treatment** — the answer: what is kept, what is swapped, and why each swap serves the
  user's brief rather than only avoiding the original.

Transcribe rather than summarise: the cue text verbatim, the timecodes exact, the frames named.

## 2. Keep, swap, must stay dynamic

The same three lists as a styleframe inventory ([intake.md](intake.md), *Inventory a design reference*):

| List | Holds |
|---|---|
| **keep** | beat order and lengths, cut rhythm, layout system, caption behaviour, where each B-roll lands, the sound's shape |
| **swap** | cast, product, copy, palette and type (the brand's), music, every generated image |
| **must stay dynamic** | when the clone becomes a template: every text role, media slot and designed colour ([personalization.md](../engine/personalization.md)) |

Name the three features whose loss would make the result stop resembling the reference. They are
what the review compares first.

## 3. Rebuild with what we have

- **Cast and world:** the [production bible](production-bible.md) — sheets for every recurring
  person, product and set; nothing from the reference is a sheet.
- **A presenter:** the [presenter route](../routes/presenter.md): a generated host with a designed
  voice through the avatar path or `--ref-audio` ([ref-audio.md](../shoot/ref-audio.md)), and the
  speaking clip's audio *is* the voice ([traps.md](../traps.md)).
- **The camera and the cut:** the grey-box animatic ([sketch.md](../shoot/sketch.md)) authored from
  the Timeline, or — with consent — the reference clip itself as `--ref-video`
  ([ref-video.md](../shoot/ref-video.md)), with the prompt carrying the new look in full.
- **B-roll:** one generated image or clip per Timeline row that has one, prompted from the row's
  job, not from the original's picture.
- **The scene:** assembled from the Timeline — each row a slot, each slot its clip, captions, board
  or graphic, at the row's in/out. Layouts from [layouts-catalogue.md](../craft/layouts-catalogue.md)
  where the reference's system maps to a named one.

## 4. Re-anchor on the new performance

The new narration has its own timing. Caption it, then move every event to the phrase it belongs to:

```bash
strata captions new_take.mp4 --allow-public-upload -o new_cues.json
```

Events are placed on **phrases**, not words — the endpoint returns segment timings
([captions.md](../shoot/captions.md), *SEGMENT timings, not per-word*). A B-roll that must land on
one word inside a phrase is placed by ear from the phrase's start, and the storyboard says so. A
re-voiced or translated version repeats this step; nothing is retimed by hand from the old take.

## 5. Prove it

```bash
strata review out.mp4 --scene scene.json --reference reference.mp4 -o review/
```

- The reference's tokens beside the render's: the gaps are the ones the Treatment chose, and no
  other ([review.md](../qa/review.md), *Comparing against the declared position*).
- The three load-bearing features present, at their beats.
- The audio pass: every narration span heard, the voice over the bed ([review.md](../qa/review.md),
  *The audio pass*).
- Nothing of the original's assets or identity in the frame.

## Checklist

- [ ] Analysis, Timeline and Treatment written before the first asset
- [ ] keep / swap / must-stay-dynamic lists in BRIEF.md; the three load-bearing features named
- [ ] No reference footage, logo, voice or face in the piece; the clip uploaded only with consent, only for `--ref-video`
- [ ] Every event on a phrase of the new take, re-anchored after any re-voice
- [ ] `strata review --reference` run on the final candidate; the audio pass run when there is narration
