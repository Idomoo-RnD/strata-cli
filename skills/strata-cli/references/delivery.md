# Delivery

Use this when checking, rendering, or handing over an output. This guide owns
acceptance. Apply the checks relevant to the task: a trim, silent graphic,
narrated film, and personalized template need different evidence.

## Contents

- [Choose the check](#choose-the-check)
- [Prepare and render](#prepare-and-render)
- [Review the final candidate](#review-the-final-candidate)
- [Check sound and speech](#check-sound-and-speech)
- [Fix and hand over](#fix-and-hand-over)

## Choose the check

| Check | What it establishes | What it does not establish |
|---|---|---|
| `validate` | Structural diagnostics, assets, relevant warnings — including where each text line's glyphs land (frame edge, title-safe), its size on screen, moves done in a snap, a placeholder left in, a word spelt two ways | A visually successful render |
| `validate --contrast` | Each text line against what is drawn behind it, on local frames; reports illegible lines only | Legibility on the cloud render; a thin but readable line |
| `review --fast` | One timecoded contact sheet, loudness, true peak and how the sound ends, in seconds | Cuts, motion and settles — never a delivery review |
| `glyphs` | Font coverage for supplied text | Final text layout or readability |
| `preview` | Real frames drawn locally: layout, type, media, blends, effects, 3D | Final motion, audio, or cloud exporter success (close to a render, not identical) |
| [`view`](view.md) | The scene playing in a browser, stills exported at chosen times, notes from the user | Sound, or cloud exporter success (local frames, like `preview`) |
| `compile` | Creation of an IDM from the source | Final motion, audio, or cloud exporter success |
| `review` | Timecoded measurements and inspection images from a video | Automatic aesthetic approval |

The text and motion checks run on every `validate` and `compile` in
milliseconds; run `validate --contrast` once copy and backgrounds are placed. A
warning names a time: look at that moment with `preview --at` before changing
anything. *measured:* on 22 agent scenes with one defect planted per copy,
glyphs off the frame 21/21 (the box-only check found 12/21), type under 1.6% of
frame height 20/22, a placeholder 22/22, a word spelt two ways 7/7, a snap
11/17, low contrast 19/21 — and no default warning on the 22 clean scenes;
on their delivered renders `--contrast` flagged 17 lines, about 13 visibly weak.

Iterate a look with `preview` frames; the final proof comes from the rendered
video. Render once, then take the
moments you need from the MP4 locally: `strata review` writes a contact sheet
and frames around every cut, and one ffmpeg pass takes exact frames
(`ffmpeg -i scene_v1.mp4 -vf "select='eq(n\,25)+eq(n\,100)'" -vsync 0 -q:v 2 f_%02d.jpg`;
frame = seconds × fps). Every render also returns one poster JPG at
`--poster-time`. A render uploads once per version: an unchanged scene reuses
its upload without compiling. *measured:* a 15 s scene took 37.9 s to render as
a new version; once uploaded, a render plus local frames took 6.5–9.2 s.
Use a local check when it answers the question, and a permitted render when
the local tools cannot.

## Prepare and render

For scene work:

```bash
strata validate scene_v1.json
strata glyphs scene_v1.json
strata preview scene_v1.json --at 0.5,2,4.5 -o look.png
strata compile scene_v1.json -o scene_v1.idm
strata render scene_v1.json --library "<selected-id>" -o scene_v1.mp4
```

These show the tools, not a mandatory command sequence for every edit.
`render` can compile scene JSON itself. A source-only task may finish before
rendering, provided its limitations are clear.

Before spending on a render, check the candidate's relevant times, local asset
paths, font coverage, explicit comp durations, and unique layer names. Check
clip duration after trim-in and any frame-rate conversion. Investigate warnings;
record a reason when evidence shows a warning is harmless. For example, 2D
overlap diagnostics can be misleading for intended 3D overlaps.

Use the user's selected library, including an already established project
setting or preference. If no destination is established, obtain that choice
instead of selecting an arbitrary account library. Existing authorization
persists. Keep generation and renders within the authorized budget;
there is no required number of iterations.

Use distinct candidate filenames or other clear version tracking. Wait for the
render to finish and verify its returned file. A started process or printed
job ID is not a completed video.
The scene filename supplies its cloud library name; the internal comp `name`
does not. Automatic scene history and an embedded IDM metadata stamp provide
additional version information through `versions` and `inspect`.

`render` returns the scene's id (`scene_id`). Render the same file again after an
edit and it keeps that id: the new version replaces the scene in place, then
renders, so anything pointing at the id gets the new version. Idomoo accepts a
replacement only with every placeholder of the old scene (same layer names and
kinds; new ones may be added), the same fps and the same aspect ratio. An edit
that breaks one becomes a new scene, and the output names the rule and the new
id. `--scene <id>` puts the file into a given scene (and fails instead);
`--new-scene` always makes a new one. `strata replace file --scene <id>` replaces
without rendering (`--dry-run` only checks).
*measured:* on the test library, a 2-second scene was replaced in 12 s and kept
its id; an edited file rendered three times stayed one scene id (replaced, then
reused unchanged), and an edit that removed a placeholder layer became a new
scene with the reason printed. Changes to fps, aspect ratio and a removed layer
were refused before upload.

## Review the final candidate

```bash
strata review scene_v1.mp4 --scene scene_v1.json -o review_v1
strata review scene_v1.mp4 --scene scene_v1.json --reference reference.mp4 -o comparison_v1
```

For direct editing, review the resulting video without `--scene` when no scene
exists. The scene option enables checks against authored timing.

| Review output | Useful for |
|---|---|
| `report.md`, `review.json` | Cuts, motion, holds, settles, loudness, and warnings |
| `contact.png` | Sequence and overall layout |
| `contact_phone.png` | Small-scale hierarchy; not an exact device simulation |
| `cut_NN.png` | Consecutive frames around detected cuts |
| `settle_NN.png` | Frames near intended keyframe ends |

The report identifies the candidate with its file/hash and, when supplied,
the scene hash. A later render or re-encode needs its own check.

Read the report and open relevant evidence. Inspect the first and last frames,
important text, busiest motion, longest holds, and suspicious intervals. Judge
movement from playback or consecutive frames, not a single attractive poster.
Check small details at useful resolution and text at its intended viewing size.

As relevant, verify:

- The approved message, facts, copy, brand, and requested emotional/visual intent.
- Readable hierarchy, contrast, destination safe areas, and reading time.
- Continuity, clipping, masks, alpha edges, tracking, and transitions.
- Source coverage: no unintended frozen end, missing action, or exhausted clip.
- Duration, dimensions, aspect, frame rate, codec, captions, and audio requirements.
- Data truth and the agreed editable/replacement roles.

Respect requested accessibility features. Keep captions clear of important
picture, avoid relying on color alone for essential distinctions, and check
intense flashing with an appropriate analysis tool when present.

An intentional locked hold or silence can be correct. Distinguish it from a
source that ran out or an animation that stopped accidentally. Do not add drift,
noise, sound, or gratuitous movement solely to improve a detector score.
Numeric diagnostics need context; they are not universal creative targets.

For a reference match, compare the relationships the user requested, such as
cut timing, placement, or motion. Do not treat every measured difference as a
failure if the brief deliberately changes that feature.

## Check sound and speech

Judge the **final video's** mix. Correct input stems do not prove they survived
the render or edit. When possible, listen at normal speed; measurements support
but do not replace judgments about pronunciation, masking, or musical balance.

`review --scene` can identify narration from audio layers with recognizable
voice/speech names. For a premix or an unrecognized layer name, supply stems and
their scene offsets:

```bash
strata review final.mp4 --narration "speech_1.wav@1.0,speech_2.wav@9.5" -o audio_review
```

Inspect narration presence at the right time, sentence endings, pauses, and
voice-over-bed diagnostics. The reported margin is a level comparison, not
proof of intelligibility. Check clipping/peaks and any specified delivery
loudness target. `review` and `review --fast` warn on a true peak above
−1 dBFS and on sound still at full level on the last frame — a cut, not an
ending, unless the storyboard declared a hard stop. *measured:* of 22 delivered
agent renders, 4 peaked above −1 dBFS and 1 ended at full level. A silent brief does not need a music track or loudness target.

For a talking actor, verify lip sync against the returned performance audio.
For captions, verify wording and timing against the final edit. Automatic
transcription may require cloud processing or hosting; follow the
[media rules](media.md#captions-and-speech-timing) before using it.

Repair fades, trim-ins, and ducking in the source stems as described under
[audio preparation](media.md#narration-music-and-audio-preparation), then check
the new final mix.

## Fix and hand over

Group related fixes, preserve what already works, and review the revised final
candidate for both the corrected defect and regressions. A new defect still
matters even if the previous fix succeeded.

For personalized output, check representative edge rows: long/short copy,
different scripts, minimum/maximum values, empty data, and media aspect changes.
Verify actual placeholder keys and changing chart geometry. Do not launch a
large batch merely to test a few cases unless that work is authorized.

Deliver the requested video and agreed source/IDM/assets or template contract.
State what was actually inspected and any remaining limitation. Keep useful
project decisions or a reusable recipe when continued work benefits from them;
do not add documentation as a ritual.

If playback, listening, a required render, or another material check was
unavailable, say so. If a blocking defect remains and the budget is exhausted,
stop spending and identify the specific additional work needed. Hand over an
explicitly incomplete preview when useful; do not label it a verified final.
