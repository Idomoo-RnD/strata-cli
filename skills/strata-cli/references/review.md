# Review — the critic pass on the rendered video

**When:** after every render, first or revision. A poster proves composition; it cannot show
easing, rhythm, cuts, settles, sound or legibility in motion — the things a viewer actually
judges. The review is done on the **MP4**, with evidence, and it decides whether the piece ships.

The rule that matters most: **compiling, validating, a clean snapshot, or the presence of
advanced features (3D, camera, `.jet`, occlusion, tracking) is never a reason to approve.** Only
the watched video is.

## Contents

- [1. Run the tool](#1-run-the-tool)
- [2. Watch it four ways](#2-watch-it-four-ways)
- [3. The nine categories — pass or must-fix](#3-the-nine-categories--pass-or-must-fix)
- [4. Evidence rules](#4-evidence-rules)
- [5. Reading the report](#5-reading-the-report)
- [6. Revise until clean](#6-revise-until-clean)
- [7. Comparing against the declared position](#7-comparing-against-the-declared-position)

## 1. Run the tool

```bash
strata review out.mp4 --scene scene.json -o review/
strata review out.mp4 --scene scene.json --reference ref.mp4 -o review/    # with a deconstructed reference
```

It writes, into `review/`:

| File | What it is |
|---|---|
| `report.md` | the findings with timecodes — cuts, freezes, hard stops, loudness, silence, motion energy per shot, settle checks against the scene's keyframes |
| `contact.png` | a contact sheet of the whole piece (one tile per second, or fewer for long pieces) |
| `contact_phone.png` | the same at phone-tile size — if a caption is unreadable here, it is unreadable on a phone |
| `cut_NN.png` | seven frames around every detected cut (−3 … +3), so the join is judged frame by frame |
| `settle_NN.png` | frames around every keyframe end the scene declares, to see whether the element lands or stops dead |
| `review.json` | everything the report says, as data (for the eval harness) |

The tool needs `ffmpeg`. Without `--scene` it finds cuts, freezes and hard stops but cannot check
settles against intended keyframes.

## 2. Watch it four ways

The tool finds what a machine can find; the critic still watches. Four viewings, in this order:

1. **1× with sound.** Does the piece land as a whole? Do cuts sit on the audio? Is the mix at the
   target it declared, and does it carry the arc? An agent cannot literally listen — measure
   instead and say so: `strata beats` onsets against the cut times, the
   report's integrated LUFS / true peak / silences, and per-band levels
   (`ffmpeg -af highpass=f=120,volumedetect`) to prove the mix is not all sub-bass. Never claim a
   viewing that did not happen.
2. **Muted.** Does the message survive without sound — legible copy, clear focal order, the CTA
   obvious? Most feeds start muted.
3. **Phone size** (`contact_phone.png`, or the MP4 at ~360 px wide). Do captions read, does the
   hero dominate, is anything lost in the chrome bands?
4. **Frame-stepped around every cut and settle** (`cut_NN.png`, `settle_NN.png`). Does the join
   carry a shape or vector across, or jump? Does the element ease to rest, stop dead or bounce?
   Is motion blur on the fast frames?

## 3. The nine categories — pass or must-fix

Every category gets **pass** or **must-fix** — not a score out of ten; an 8 is not a decision. A
must-fix carries a timecode, a frame reference and the rule it breaks. The ladder below calibrates
the word "pass" and is not averaged: a category passes only at the middle column, and the right
column is what the piece is aiming at.

| | Functional (not a pass) | Premium (the pass line) | Exceptional |
|---|---|---|---|
| Brief / brand / reference fit | present, but could belong to another brief | decisions clearly arise from *this* subject, brand and viewing context | the treatment feels inevitable for this brief and still surprises |
| Story / information release | the sequence is understandable | every beat changes what the viewer knows, feels or expects | information and emotion are released with memorable control |
| Focal hierarchy | the main thing is findable | eye order is deliberate at rest **and through the motion** | attention is choreographed effortlessly through complex change |
| Temporal composition | timing works locally | the piece sits where it said it would on the range, with real variation around it | the whole piece has dramatic shape without feeling mechanical |
| Weight & easing | motion is smooth | curves, inertia, path and settle express material and intent | motion has a distinctive physical character, no compromises visible |
| Typography | copy is legible | scale, breaks, animation unit and read time form one system | meaning, voice and letterform behaviour reinforce each other |
| Editing / transitions | cuts connect shots | every cut has a job and keeps or deliberately breaks continuity | the joins add meaning and are inseparable from sound and motion |
| Camera / space | framing is usable | shot size, depth and camera behaviour are motivated and varied | spatial design creates real orientation, tension or reveal |
| Compositing / finish | no obvious artifacts, but every surface one flat value | light has a direction, type sits at a depth in the frame, the grade changes between beats | surfaces survive close inspection and still serve the story |
| Sound / picture | audio present and roughly in sync | at its declared target, with a quiet beat and a loud one; hits, silence, VO and bed shape the cut | sound and image are one system; removing either weakens the idea |
| Originality / restraint | familiar devices dominate | one earned signature; no defaults, no spare decoration | a recognisable motion identity without mannerism |
| Brand fidelity / delivery | it plays | atoms exact; safe areas, fps, codec, first/last frames correct | holds up across sizes, languages and data extremes |

| Category | Passes when | Typical must-fix |
|---|---|---|
| **Art direction** | the frames match the styleframes / the brand; one signature; nothing decorative survived | a scrim, rule, glow or gradient nobody chose (anti-slop tell at 00:04.2) |
| **Hierarchy** | first read is the focal element in every shot; the still list holds | two elements fight at 00:07.0; the wordmark drifts during the hero shot |
| **Weight & easing** | entrances settle on the bible's curves; exits faster than entrances; no dead stops, no unearned overshoot | element stops dead on `inOutSine` at 00:03.6; every entrance overshoots |
| **Typography** | read time respected (0.5 s + 0.3 s/word); captions in chunks; no tofu; safe areas | line leaves at 00:11.8 before it can be read; caption in the bottom 15 % |
| **Editing** | `shortest` and `longest` inside the declared range, the longest shot the one the storyboard said would carry the idea, **and the motion the storyboard named for that hold visible in its frames**; cuts on the audio; shot size and camera change at every cut; motion ideas per shot and transition families as declared | declared a 0.3 s flash and a 3 s hold, measured 0.32–2.16 s at regularity 0.37 — one speed; the 4.5 s hold is frozen at 0.88 stillness against the reference's 0.16, the end card 4.12 s of static wordmark; three transition families where two were declared; a cut 0.2 s off the onset at 00:09.9 |
| **Compositing** | overlays sit *in* the image; mattes clean; no sticker slides; grade coherent; **type carries the plate's light** — a contact shadow in the key's direction, one light direction per frame, the fill following the surface, grain and focus falloff matched | a `.jet` edge halo at 00:05; a cut-out translated over a plate; the wordmark's colour band runs level across all six letters, so the fill is a rectangle behind a mask, not light on a surface ([craft.md](craft.md)) |
| **Sound** | integrated LUFS within ~1 dB of the storyboard's target; the loud and quiet beats as far apart as it promised; three layers balanced; SFX peak on the cut; no clipping | delivered at −16.9 LUFS against a −23 broadcast target; loudest and quietest beat 2 dB apart, so nothing lands |
| **Originality** | it could not have been produced for any similar brief | the default palette, the numbered markers, the hero-metric layout |
| **Brand fidelity** | atoms exact (hex, fonts, mark, clear space); motion language matches the brand doc | wrong red at 00:02; the mark redrawn; a font substituted |

## 4. Evidence rules

**Every must-fix is written in one shape:**

```text
[priority] timecode — evidence → viewer effect → likely cause → smallest useful fix → trade-off
```

*Example:* `[1] 00:00.0–00:03.1 — bands are flat colour, no material (contact.png tiles 1–3) →
the piece claims "nothing is flat" over its flattest frame → the texture was generated but only
used on the wordmark → mask a slice of the rock into each band → +12 masks, ~1 s of compile.`

The chain matters: **viewer effect** stops a note being a style opinion, **likely cause** names the
build pass that owns it, **smallest useful fix** stops a revision becoming a redesign, the
**trade-off** lets it be refused knowingly.

Group the notes as **Must fix** (breaks meaning, hierarchy, fidelity, continuity, legibility, sync
or finish) · **Should improve** (visible weakness below the intended bar) · **Experiments**
(alternatives worth testing, not assumed better) · **Keep** — the decisions a revision must not
quietly destroy, which is what makes the second render comparable to the first.

- A finding cites a **timecode** (`mm:ss.f`) and, where a frame matters, the **frame file**
  (`cut_03.png`, tile 4). "The motion feels off", "make it more dynamic", "make it cinematic" and
  "add polish" are not findings — name the property, the moment and the intended viewer effect.
- A finding names the **rule** it breaks — the bible field, the anti-slop pattern, the
  motion-design tell, the brand atom.
- Findings are listed **must-fix first**, then observations. Observations do not block.
- The report is written for the user to read: no scene keys, no internal jargon, the fix in one
  line next to the finding.

## 5. Reading the report

- **Cuts and shot lengths** — count and `shots.lengths` against the storyboard's shot list (a
  missing cut is a shot that did not happen; an extra one is a montage verb that leaked into a
  generated clip), then `shortest`, `longest` and `rhythmRegularity` (1 = metronomic) against the
  declared range, never against a constant — 47.5:1 at regularity 0.03 and 3.1:1 at 0.65 are both
  world-class ten seconds (SKILL.md, *Place the piece on the range*).
- **Freezes** — runs where nothing changes for longer than 0.6 s that are not the end hold or a
  declared held shot. Inside a generated clip that is a hover; inside an authored scene it is a
  layer that stopped before the shot ended.
- **Hard stops** — motion energy that collapses within one frame. Some are cuts (fine); the rest
  stopped dead — check each against the bible's overshoot policy.
- **Settles** (with `--scene`) — for every keyframe end the scene declares, the energy in the six
  frames before and after. Settled: after ≪ before. Stopped dead: before is high and after is
  zero on a soft curve. Bouncing: after stays high past the keyframe.
- **Motion energy per shot** — a number per shot. Judge the *mean* against the declared energy and
  the *profile* against the declared arc. Uniformly high is correct for a piece that declared
  energy 11 and wrong for one that declared 1; uniformly low is a slideshow unless stillness was
  the declared point. Flat at the right mean is still flat.
- **Loudness** — integrated LUFS against the storyboard's target and loudness range against the
  promised loud/quiet gap ([music.md](music.md), *Decide the mix*). **Silence** longer than 1 s is a
  must-fix only where the storyboard did not plan it — a planned one is the dynamic range doing its
  job, and it is named in the notes.
- **True peak** — the encoder pins a hot mix against a ceiling near **−0.5 dBFS** and leaves a
  quieter one quieter ([music.md](music.md), *Decide the mix*), so "peak above −1 dBFS" on a mix
  already at the ceiling is not fixable from inside the scene: clear it with an ffmpeg gain pass on
  the delivered MP4, or lower the layer levels and re-render.
- **Reference comparison** (§7) when a reference was given.

### Known false positives — check the frames before believing the metric

Every number in the report is computed on the **whole frame**, from tiny greyscale frames. Three
consequences, each measured on a real render:

- **A small-area move reads as a freeze.** A 0.68 s grind that moved ~10 % of the frame was
  reported as frozen. If a freeze is flagged where something *is* moving, open the frames either
  side and compare — a real freeze is identical pixels, not low energy.
- **A large-area low-amplitude move also reads as a freeze** — the opposite failure, and the more
  dangerous one, because holds are sent toward exactly this kind of motion. *Measured:* a light
  shaft crossing the whole frame came back **93 % still**. Stillness and energy both under-register
  a gentle wash over a wide area, so on a hold whose life is light, grade or drifting texture the
  metric can neither clear nor condemn it — step its first, middle and last frames. Low stillness
  proves life there; high stillness does not prove death.
- **Shots shorter than ~0.3 s are merged into their neighbour.** A declared two-frame flash may
  never appear in `shots.lengths`, and `shortest` then reads longer than the cut is. Verify a flash
  on its `cut_NN.png` frames; never lengthen a correct flash to make the list agree.
- **A settle can be reported as "bounce or overrun" when the energy is the NEXT element.** The
  check reads energy after the keyframe, and cannot tell whose energy it is. `outExpo` and
  `outCubic` cannot overshoot at all, so a bounce verdict on one of those is always something else
  arriving — confirm with `settle_NN.png` before changing a curve that is correct.
- **A camera that is meant to keep moving past a keyframe** is not an overrun. Say so in the notes
  rather than "fixing" it.
- **A deliberate hold is listed as a freeze.** The tool cannot tell a planned breath before a slam,
  a locked end card, or the covered beat of a hidden swap from a clip that ran out. Classify each
  freeze in the notes — *planned, this long, alive by…* — and fix the ones the storyboard did not
  plan; an unplanned freeze is still a must-fix. **And "planned" excuses the length, not the
  deadness:** frames that really are identical for seconds are a freeze whatever the storyboard
  called it, and go back as a must-fix on Editing.
- **`contact_phone.png` is stricter than a phone.** Its tiles are ~185 px wide; a phone is
  390–430 px. Copy that fails the tile may still pass on the device — check at true width before
  calling type a must-fix, and never the other way round (passing the tile is a real pass).

## 6. Revise until clean

- Every must-fix goes back to the **pass it belongs to** (blocking / primary / secondary /
  finishing — [director.md](director.md) §7), never a patch on top.
- Re-render, re-run `strata review`, re-watch the four ways. A piece ships when **no must-fix
  remains** — not when the count went down. Versioned filename on every revision (`_v2`, `_v3`);
  the report names the version reviewed.
- Two identical must-fixes on consecutive renders mean the approach is wrong, not the value.

## 7. Comparing against the declared position

**Every piece gets this comparison, reference or not.** The storyboard's four numbers are the
target; the report's tokens are the measurement. Open the review with a two-column table —
declared beside measured — for duration, cut count, `shortest` / `longest` / `rhythmRegularity`,
energy mean and profile, loudness and range, silence ratio. The gap is the finding: twice the
intended shot length, a third of the intended energy, stillness where none was declared, a mix
2 dB off target — each a must-fix on **Editing** or **Sound**, fixed in the cut and the mix. Declaring an extreme and measuring the middle is the failure this
section exists to catch, and the likeliest one — the middle is what the defaults produce.

**The cut has its own middle**, so name the longest entry in `shots.lengths` and say what that shot
carries, and the shortest and say what it is. If the longest is where the storyboard said the idea
lands, the range is real; if it is a connective shot that ran long, the finding is *one speed*, not
*spread too narrow*, and the fix is to hold the shot that deserves holding rather than stretch and
clip until the ratio clears. A narrow range on a piece that declared one is not a defect.

**Then check the hold is alive** — a right range and a dead hero shot are two separate failures and
a piece can pass one and fail the other. Read stillness and energy *inside* the longest shot, look
for the motion the storyboard named there, and **count its frames whose frame-to-frame difference
falls below 0.5**: the reference's longest hold has none, ours had 133 of 138 on the hero shot and
95 of 98 on the end card (SKILL.md, *Place the piece on the range*). Read the profile by thirds —
ours collapsed 4.16 / 6.90 / 0.71, a piece that stops trying at the end — and **check the last shot
explicitly**, because the end card is a hold and is where this is missed. The fix is motion inside
the shot, never a shorter shot.

With `--reference ref.mp4` (or a `ref_tokens.json` from `strata deconstruct`) the reference's
tokens join as a third column, plus its palette. A reference is a technique source, not a thing to
match everywhere — a flat energy or loudness profile where the reference peaks is a finding only
where the direction promised its rhythm.
