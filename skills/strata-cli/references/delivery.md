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
| `validate` | Structural diagnostics, assets, relevant warnings | A visually successful render |
| `glyphs` | Font coverage for supplied text | Final text layout or readability |
| `preview` | Local wireframe bounds and layout | Actual glyphs, media appearance, or camera projection |
| `compile` | Creation of an IDM from the source | Final motion, audio, or cloud exporter success |
| `snapshot` | A real cloud-rendered frame | Whole-video timing, continuity, or sound |
| `review` | Timecoded measurements and inspection images from a video | Automatic aesthetic approval |

A snapshot uploads an entry to the chosen library and can spend cloud budget.
Use a local check when it answers the question, and a permitted rendered check
when the local tools cannot.

## Prepare and render

For scene work:

```bash
strata validate scene_v1.json
strata glyphs scene_v1.json
strata preview scene_v1.json --at 2 --grid -o layout.png
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
persists. Keep generation, snapshots, and renders within the authorized budget;
there is no required number of iterations.

Use distinct candidate filenames or other clear version tracking. Wait for the
render to finish and verify its returned file. A started process or printed
job ID is not a completed video.
The scene filename supplies its cloud library name; the internal comp `name`
does not. Automatic scene history and an embedded IDM metadata stamp provide
additional version information through `versions` and `inspect`.

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
loudness target. A silent brief does not need a music track or loudness target.

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
