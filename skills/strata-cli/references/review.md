# Review — the critic pass on the rendered video

**When:** after every first render of a piece, and after every revision render. This replaces
"look at the poster and tick the checklist". A poster proves composition; it cannot show easing,
rhythm, cuts, settles, sound or legibility in motion — the things a viewer actually judges. The
review is done on the **MP4**, with evidence, and it decides whether the piece ships.

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
- [7. Comparing against a reference](#7-comparing-against-a-reference)

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

The tool needs `ffmpeg`. Without `--scene` it still finds cuts, freezes and hard stops, but cannot
check settles against intended keyframes.

## 2. Watch it four ways

The tool finds what a machine can find; the critic still watches. Four viewings, in this order,
each with a purpose:

1. **1× with sound.** Does the piece land as a whole? Do cuts sit on the audio? Does the mix
   carry the arc (voice / bed / SFX levels)? An agent cannot literally listen — measure instead
   and say that is what was done: `strata beats` onsets against the cut times, the report's
   integrated LUFS / true peak / silences, and per-band levels
   (`ffmpeg -af highpass=f=120,volumedetect`) to prove the mix is not all sub-bass. Never claim a
   viewing that did not happen.
2. **Muted.** Does the message survive without sound — legible copy, clear focal order, the CTA
   obvious? Most feeds start muted.
3. **Phone size** (`contact_phone.png`, or the MP4 at ~360 px wide). Do captions read, does the
   hero still dominate, is anything lost in the chrome bands?
4. **Frame-stepped around every cut and settle** (`cut_NN.png`, `settle_NN.png`). Does the join
   carry a shape or vector across, or jump? Does the element ease to rest, stop dead, or bounce
   for no reason? Is motion blur on the fast frames?

## 3. The nine categories — pass or must-fix

Every category gets **pass** or **must-fix**. Not a score out of ten: numbers get inflated and
an 8 is not a decision. A must-fix carries a timecode, a frame reference and the rule it breaks.

**What the levels look like** — the ladder is here to calibrate the word "pass", not to be averaged.
A category *passes* only at the middle column; the right column is what the piece is aiming at.

| | Functional (not a pass) | Premium (the pass line) | Exceptional |
|---|---|---|---|
| Brief / brand / reference fit | present, but could belong to another brief | decisions clearly arise from *this* subject, brand and viewing context | the treatment feels inevitable for this brief and still surprises |
| Story / information release | the sequence is understandable | every beat changes what the viewer knows, feels or expects | information and emotion are released with memorable control |
| Focal hierarchy | the main thing is findable | eye order is deliberate at rest **and through the motion** | attention is choreographed effortlessly through complex change |
| Temporal composition | timing works locally | rhythm varies — setup, action, settle, hold, contrast | the whole piece has dramatic shape without feeling mechanical |
| Weight & easing | motion is smooth | curves, inertia, path and settle express material and intent | motion has a distinctive physical character, no compromises visible |
| Typography | copy is legible | scale, breaks, animation unit and read time form one system | meaning, voice and letterform behaviour reinforce each other |
| Editing / transitions | cuts connect shots | every cut has a job and keeps or deliberately breaks continuity | the joins add meaning and are inseparable from sound and motion |
| Camera / space | framing is usable | shot size, depth and camera behaviour are motivated and varied | spatial design creates real orientation, tension or reveal |
| Compositing / finish | no obvious artifacts | grade, mattes, edges, blur, texture and light are coherent and restrained | surfaces survive close inspection and still serve the story |
| Sound / picture | audio present and roughly in sync | hits, silence, VO, bed and mix hierarchy shape the cut | sound and image are one system; removing either weakens the idea |
| Originality / restraint | familiar devices dominate | one earned signature; no defaults, no spare decoration | a recognisable motion identity without mannerism |
| Brand fidelity / delivery | it plays | atoms exact; safe areas, fps, codec, first/last frames correct | holds up across sizes, languages and data extremes |

| Category | Passes when | Typical must-fix |
|---|---|---|
| **Art direction** | the frames match the styleframes / the brand; one signature; nothing decorative survived | a scrim, rule, glow or gradient nobody chose (anti-slop tell at 00:04.2) |
| **Hierarchy** | first read is the focal element in every shot; the still list holds | two elements fight at 00:07.0; the wordmark drifts during the hero shot |
| **Weight & easing** | entrances settle on the bible's curves; exits faster than entrances; no dead stops, no unearned overshoot | element stops dead on `inOutSine` at 00:03.6; every entrance overshoots |
| **Typography** | read time respected (0.5 s + 0.3 s/word); captions in chunks; no tofu; safe areas | line leaves at 00:11.8 before it can be read; caption in the bottom 15 % |
| **Editing** | cuts on the audio; shot size and camera change at every cut; one dominant idea per shot; ≤ 2 transition families | three transition families; a cut 0.2 s off the onset at 00:09.9 |
| **Compositing** | overlays sit *in* the image; mattes clean; no sticker slides; grade coherent | a `.jet` edge halo at 00:05; a cut-out translated over a plate |
| **Sound** | three layers balanced; SFX peak on the cut; no silence gaps; no clipping; loudness sane | −3 LUFS peak clip at 00:12.4; 1.8 s of silence at 00:14 |
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

The chain matters: **viewer effect** stops a note being a style opinion, **likely cause** points at
the build pass that owns it, **smallest useful fix** stops a revision becoming a redesign, and the
**trade-off** lets the fix be refused knowingly.

Group the notes as **Must fix** (breaks meaning, hierarchy, fidelity, continuity, legibility, sync
or finish) · **Should improve** (visible weakness below the intended bar) · **Experiments**
(alternatives worth testing, not assumed better) · **Keep**. The **Keep** list is not padding — it
names the decisions a revision must not quietly destroy, and it is what makes the second render
comparable to the first.

- A finding cites a **timecode** (`mm:ss.f`) and, where a frame matters, the **frame file**
  (`cut_03.png`, tile 4) or a frame number. "The motion feels off" is not a finding.
- Never write "make it more dynamic", "make it cinematic" or "add polish" without naming the
  property, the moment and the intended viewer effect.
- A finding names the **rule** it breaks — the bible field, the anti-slop pattern, the
  motion-design tell, the brand atom.
- Findings are listed **must-fix first**, then observations. Observations do not block.
- The report is written for the user to read: no scene keys, no internal jargon, the fix in one
  line next to the finding.

## 5. Reading the report

- **Cuts** — count, average shot length, the two shortest and longest. Compare with the
  storyboard's shot list: a missing cut is a shot that did not happen; an extra one is a montage
  verb that leaked into a generated clip.
- **Freezes** — runs where nothing changes for longer than 0.6 s that are not the end hold or a
  declared held shot. A freeze inside a generated clip is a hover; inside an authored scene it is
  a layer that stopped before the shot ended.
- **Hard stops** — motion energy that collapses within one frame. Some are cuts (fine); the rest
  are elements that stopped dead. Check each against the bible's overshoot policy.
- **Settles** (with `--scene`) — for every keyframe end the scene declares, the energy in the six
  frames before and after. Settled: after ≪ before. Stopped dead: before is high and after is
  zero on a soft curve. Bouncing: after stays high past the keyframe.
- **Motion energy per shot** — a number per shot; the profile should follow the rhythm arc
  (quiet → build → peak → resolve). Uniformly high means everything moves; uniformly low means a
  slideshow.
- **Loudness** — integrated LUFS, range and true peak; **silence** longer than 1 s that the
  storyboard did not plan.
- **Reference comparison** (§7) when a reference was given.

### Known false positives — check the frames before believing the metric

Every number in the report is computed on the **whole frame**, from tiny greyscale frames. Three
consequences, each measured on a real render:

- **A small-area move reads as a freeze.** A 0.68 s grind that moved ~10 % of the frame was
  reported as frozen. If a freeze is flagged where something *is* moving, open the frames either
  side and compare — a real freeze is identical pixels, not low energy.
- **A settle can be reported as "bounce or overrun" when the energy is the NEXT element.** The
  check reads energy after the keyframe, and cannot tell whose energy it is. `outExpo` and
  `outCubic` cannot overshoot at all, so a bounce verdict on one of those is always something else
  arriving — confirm with `settle_NN.png` before changing a curve that is correct.
- **A camera that is meant to keep moving past a keyframe** is not an overrun. Say so in the notes
  rather than "fixing" it.
- **A deliberate hold is listed as a freeze.** The tool cannot tell a planned breath before a slam,
  a locked end card, or the covered beat of a hidden swap from a clip that ran out. Classify each
  freeze in the notes — *planned, this long, because…* — and fix only the ones the storyboard did
  not plan. A freeze the storyboard did not plan is still a must-fix.
- **`contact_phone.png` is stricter than a phone.** Its tiles are ~185 px wide; a phone is
  390–430 px. Copy that fails the tile may still pass on the device — check at true width before
  calling type a must-fix, and never the other way round (passing the tile is a real pass).

## 6. Revise until clean

- Every must-fix goes back to the **pass it belongs to** (blocking / primary / secondary /
  finishing — [director.md](director.md) §7), not to a patch on top.
- Re-render, re-run `strata review`, re-watch the four ways. A piece ships when **no must-fix
  remains** — not when the number of findings went down.
- Versioned filename on every revision (`_v2`, `_v3`); the report names the version reviewed.
- Two identical must-fixes on consecutive renders mean the approach is wrong, not the value —
  change the idea, not the number.

## 7. Comparing against a reference

With `--reference ref.mp4` (or a `ref_tokens.json` from `strata deconstruct`), the report puts
the render's tokens beside the reference's: duration, cut count and average shot length, shot
length spread, motion energy mean and profile, loudness and range, silence ratio, dominant
palette. It does not say "match everything" — a reference is a technique source. It says where
the render is *further from the reference than the direction intended*: twice the cut rate, half
the stillness, a flat energy profile where the reference peaks. Those differences are findings
if the direction promised the reference's rhythm.
