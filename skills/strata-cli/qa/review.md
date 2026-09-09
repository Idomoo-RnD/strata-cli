# Review — the critic pass on the rendered video

Review every final candidate, first or revision. A poster proves one frame, not easing, rhythm,
reading time, continuity or sound. Compiling, validating or using 3D/alpha/tracking is never by
itself approval. Judge the approved direction under [design-contract.md](../make/design-contract.md).

## Contents

- [1. Run the tool](#1-run-the-tool)
- [2. Watch it four ways](#2-watch-it-four-ways)
- [3. The nine categories — pass or must-fix](#3-the-nine-categories--pass-or-must-fix)
- [4. Evidence rules](#4-evidence-rules)
- [5. Reading the report](#5-reading-the-report)
- [6. Budget and final regression gate](#6-budget-and-final-regression-gate)
- [7. Comparing against the declared position](#7-comparing-against-the-declared-position)

## 1. Run the tool

```bash
strata review out.mp4 --scene scene.json -o review/
strata review out.mp4 --scene scene.json --reference ref.mp4 -o review/
```

Requires ffmpeg. Without `--scene`, intended keyframe settles cannot be checked.

| Output | Use |
|---|---|
| report.md / review.json | timecoded cuts, freezes, hard stops, loudness, motion and settle diagnostics |
| contact.png | overall shot/layout sequence |
| contact_phone.png | small-tile hierarchy screen, not an exact phone simulator |
| cut_NN.png | seven consecutive frames around a detected cut |
| settle_NN.png | frames around intended keyframe ends |

## 2. Watch it four ways

1. **At normal speed with sound:** whole-piece rhythm, emotional arc, voice/bed/SFX hierarchy.
   If the agent cannot listen or play video, say so. Onset alignment, LUFS/true peak/silence and
   frequency-band checks support but do not replace a listening judgment. Ask for human review
   where that judgment is material; never invent a viewing.
2. **Muted:** message, hierarchy, captions, CTA and reading time. Do not require the whole message
   in three seconds if the approved format deliberately reveals it later.
3. **At destination/phone size:** legibility, safe areas and platform chrome. Contact-phone tiles
   are about 185 px wide, smaller than a 390–430 px phone; inspect at true size before rejecting.
4. **Frame-step around cuts and settles:** continuity, path/velocity, settling, clipping and blur.

### Motion is judged on a FILMSTRIP, never on single frames

Spacing between consecutive frames reveals velocity, anticipation and settle; one poster does
not. Use cut/settle strips and make additional ones for long holds, springs, parallax and draw-ons.
Crop small elements before scaling so fringes, shimmer and seams remain visible.

```bash
# Example: 0.8 s at 25 fps; substitute the actual scene fps and corresponding tile count.
ffmpeg -v error -y -ss 1.2 -t 0.8 -i out.mp4 -vf "fps=25,scale=200:-2,tile=20x1:padding=2:margin=2" -frames:v 1 strip.png
# Example: a pixel-level crop around a small moving element.
ffmpeg -v error -y -ss 1.2 -t 0.6 -i out.mp4 -vf "crop=260:260:410:230,fps=25,scale=160:160:flags=neighbor,tile=15x1:padding=2:margin=2" -frames:v 1 zoom.png
```

Choose windows from `perSec`, `perSecMax`, shots/cuts and the storyboard: the busiest interval,
longest hold and last shot deserve explicit inspection. Label the source time and tile indices.
Compare first/middle/last frames for a slow light shift, but inspect consecutive frames to judge
its motion quality. Identical pixels can be an intentional locked hold; classify them before judging.

## 3. The nine categories — pass or must-fix

No average score hides a blocking defect. Categories use **pass / must-fix / not applicable /
needs evidence**. Calibrate the intended bar in the approved brief; premium is not a mandatory
photographic treatment. A functional result below the approved creative bar needs revision too.

| Category | Pass means | Typical blocker |
|---|---|---|
| Art direction | decisions arise from this subject, approved brand/styleframes and viewing context | generic treatment contradicts the approved direction |
| Hierarchy | eye order is deliberate at rest and during movement; restraint is preserved | competing reads hide the message |
| Weight & easing | timing, path, inertia and settle express the chosen material | accidental stop, unintended bounce or distracting motion |
| Typography | exact copy, glyphs, hierarchy, breaks, animation unit and reading time work together | unreadable/hidden text, missing glyph, wrong claim |
| Editing | beats release information purposefully; cuts/holds carry the intended rhythm | missing beat, broken continuity, exhausted footage |
| Compositing | chosen flat/illustrative/spatial treatment is coherent; edges and layering are intentional | halo, misregistration, wrong mask, illegible contrast |
| Sound | approved destination spec; voice intelligible; sound-picture relationship intentional | missing/doubled voice, broken sync, clipping or wrong spec |
| Originality | signature belongs to this brief; unnecessary decoration removed | default treatment defeats the approved concept |
| Brand fidelity / delivery | atoms, claims, aspect/fps/codec, safe areas and data variants correct | wrong logo, legal copy, output spec or personalized value |

The exceptional target is a memorable, coherent piece in which information, physical character,
typography and rhythm reinforce each other.

| Level | Calibration, relative to the approved brief |
|---|---|
| Functional | plays and can be understood, but may still miss the requested creative bar |
| Premium | subject-specific direction, deliberate focal/timing hierarchy, coherent type and material, clean evidence-backed delivery |
| Exceptional | the design feels both surprising and inevitable for this subject; sound, motion and information reinforce one memorable idea |

It need not have shadows, gradients, changing grades,
a moving camera, a noisy soundtrack or an extreme energy metric. A designed dissolve may pass;
so may a locked end card. The intended direction—not a universal feature checklist—sets the bar.

## 4. Evidence rules

Every blocking finding uses this chain:

```text
[priority] timecode — evidence → viewer effect → likely cause → smallest useful fix → trade-off
```

Cite the frame/strip and tile when visual evidence matters. “More cinematic” or “add polish” is
not actionable: name what the viewer loses and what property/moment causes it. Separate:

- **Must fix:** breaks approved meaning, creative acceptance, hierarchy, fidelity, continuity,
  legibility, sync, privacy, truthful data or delivery.
- **Should improve:** visible nonblocking weakness; record whether accepted and why.
- **Experiments:** alternatives, not assumed improvements.
- **Keep:** decisions a revision must not quietly destroy.

Never downgrade a blocker to fit the render budget. A revised creative requirement needs explicit
approval (or an in-scope unattended decision documented as such), not silent relabeling. Findings
for the user should be readable; keep scene-key details in the implementation notes.

## 5. Reading the report

- **Cuts/shot lengths:** compare to storyboard boundaries, not a constant ratio. The tool can
  miss motion transitions and count flashes as cuts. Verify strips before editing a correct join.
- **Freezes:** detector candidates, not automatic failures. Distinguish an intentional locked
  hold, a live hold missing its motion, source exhaustion and an unintended animation stop.
  Classification requires the approved plan plus frames; do not invent intent after a failure.
- **Hard stops:** a cut or deliberate snap may be correct; an accidental discontinuity is not.
- **Settles:** the tool measures surrounding energy, not which layer produced it. A later element
  can look like an overrun. Inspect the element and curve before changing it.
- **Energy/profile:** helps find windows for inspection. Whole-frame means do not measure quality,
  weight or viewer attention. Local activity can matter more than the mean.
- **Loudness/true peak/silence:** compare to the destination and approved mix, not a universal LUFS
  value. Planned silence is allowed. [music.md](../shoot/music.md) documents the measured encoder
  ceiling and preparation/postprocessing options; remeasure the delivered MP4 after any post pass.

### Type legibility — measured, with two blind spots

With `--scene`, top-level text boxes are sampled at three times for a contrast estimate using
expected ink color and background medians. Tool thresholds are 4.5:1 below 4 % of frame height and
3.0:1 above it; these are screening heuristics, not accessibility certification. A CTA must remain
readable through its actual span, not just at sampled instants.

- Strokes/shadows/scrims can distort the median-based estimate; inspect actual text before deciding.
- Sub-comp text is not measured at its composed screen coordinates by this check. Manually inspect
  it and report the missing automated coverage. `no ink found` needs inspection for hidden,
  recolored, transformed or absent copy—it is not a reliable contrast pass.

### Known false positives — check the frames before believing the metric

The report's `energy`/`stillness` use whole-frame means at 64×36 greyscale. `energyMax` and
`stillnessLocal` use the busiest of nine cells; freeze detection uses these cells.

- **Small-area motion:** a move across ~10 % of a frame can be near-still globally and active
  locally. Read the pair and crop the element; do not add global motion to satisfy a mean.
- **Low-amplitude motion:** a broad light wash can be below both thresholds despite visible travel.
  High stillness alone does not prove nothing moved. Inspect slow change across the shot.
- **Film vs generated/graphic sources:** grain and near-black regions change statistics. A previous
  generated ad measured **0.82** whole-frame stillness, **0.20** local and **zero freezes** despite a
  declared 0.50. That declaration was not transferable, not evidence to brighten/noise the image.
  If the report's disagreement diagnostic is absent, that still does not prove a defect.
- **Flash detection:** lightning or a white flash within one shot can count as a cut. Verify the
  same shot on both sides before changing the edit. The detector is not a flash-safety analyzer.
- **Motion transitions:** a whip or continuous handoff may not create a detectable discontinuity;
  count that boundary from the authored shot list plus visual evidence.
- **Settle attribution:** `outExpo`/`outCubic` cannot overshoot, but another arriving layer can
  raise energy after their endpoint. A moving camera continuing past a keyframe is not necessarily
  an overrun either.
- **Intentional stillness:** a locked legal card or deliberate breath can produce a freeze report
  and still pass. Source exhaustion cannot. The hold plan specifies which was intended.
- **Phone tiles:** passing a tiny tile is reassuring, failing one is a cue to inspect at the real
  viewing width—not proof that the delivered text fails.

## 6. Budget and final regression gate

Aim for a candidate → consolidated review → focused revision, not endless cosmetic exploration.
Default spend allowance is up to two full renders. A previous unattended run spent **44 of 83
minutes on six renders**; that motivates better preflight, not automatic shipment of render two.

**A first candidate that passes can ship. Every revision gets a regression gate.** Verify all old
must-fixes and inspect critical copy/claims/brand, typography, audio, source coverage, transitions,
first/last frames and data edges. New defects are not ignored because they were absent from the
first list. Preserve the keep list and the approved direction; do not reopen unrelated taste
experiments on each revision.

If any blocker remains after the budget is used, stop cloud spending and isolate the likely cause
with local evidence. Propose a scoped probe and subsequent revision budget; ask for approval. Until
resolved, deliver only a clearly marked incomplete preview/report, not a false final. Repeated
identical failures mean the construction needs rethinking, not another guessed value.

Generation, snapshots and short probes are separate ledger entries and still cost time/resources.
Use a probe only when it answers a real risk. No required number of snapshots per shot; no new
full render for a cosmetic note that is explicitly accepted. A final postprocessed MP4 also gets
fresh delivery checks, since postprocessing can introduce regressions.

## 7. Comparing against the declared position

`energy` is mean absolute greyscale change at a 64×36 downscale, cut frames excluded. `stillness`
is the share of sampled frames below change 1; the local versions use the busiest grid cell.
These are diagnostic units, not calibrated ratings of “premium.” Historical examples (quiet film
energy 1.06, action 11.43) demonstrate variation, not target values for unrelated material.

Start a review with a table: **criterion | approved intent/spec | observed evidence | confidence |
disposition**. Include duration, shot boundaries/range, hero hold purpose, typography and mix. Add
energy/stillness/profile only when useful; mark uncalibrated expectations explicitly. A mismatch
in a proxy is a question to investigate, not a blocker without a viewer effect. Do not retroactively
change an actual delivery specification to make the output pass.

When there is a reference, preserve the relationships the direction promised: focal contrast,
relative timing, restraint, material response, information and sound hierarchy. Grain, resolution
and grade differences limit numeric comparison. Inspect the longest shot and end frame specifically:
a locked hold should read and feel intentional; a live hold should show its promised action. The
fix may be a clearer composition, timing adjustment, replacement footage or motion—not automatically
a bigger push or more light. [Case studies](../craft/case-studies.md) provide scoped examples.
