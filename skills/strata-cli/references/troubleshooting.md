# Troubleshooting

Use this when a command, warning, or output differs from what was intended.
Start from the symptom, identify the responsible stage, and change one useful
cause. A retry should have a reason to behave differently.

## Contents

- [Scene, text, and motion](#scene-text-and-motion)
- [Media and generation](#media-and-generation)
- [Cloud rendering and reusable files](#cloud-rendering-and-reusable-files)
- [When the cause is still unknown](#when-the-cause-is-still-unknown)

## Scene, text, and motion

| Symptom | Useful check and recovery |
|---|---|
| Unknown property | Distinguish compact keys from raw VASCO. Inspect `--vasco` output and the [scene model](scenes.md#scene-and-layer-model). |
| Sub-comp ends early | Check its duration and instance timing against [group rules](scenes.md#groups-and-clipping). |
| Layer jumps off-position | Check anchor targets versus offsets in [coordinates](scenes.md#coordinates-and-anchors). |
| Text clips, shifts, or wraps badly | Check glyph coverage, real font, fitted size, box, and [alignment](scenes.md#text). |
| Typeface differs | Supply the correct font file/variant; see [text](scenes.md#text). |
| Text reveal jumps | Check range units, end, and intermediate keys against [typewriter.json](../examples/typewriter.json). |
| Digit effect corrupts punctuation | Check selected characters and separate symbols; see [text](scenes.md#text). |
| Window moves with content | Replace the moving layer mask with a fixed comp/matte; see [masks](scenes.md#shapes-masks-and-mattes). |
| Camera output is black or tiny | Check absolute position, depth, FOV, and `is_3d`; see [camera](advanced.md#camera-and-parallax). |
| Intentional overlap is flagged | Inspect actual depth/blending before changing it; see [preflight](delivery.md#prepare-and-render). |
| Corner pin disappears | Check receiving layer type and point order; see [tracking](advanced.md#tracking-a-surface-or-subject). |
| Attached graphic drifts | Compare analyzed footage with final crop/fps/timing; correct mapping or failed sections. See [tracking](advanced.md#tracking-a-surface-or-subject). |
| Reactive graphics mislead | Check the analyzed asset, offset, and normalization; see [audio-driven motion](advanced.md#audio-driven-motion). |

## Media and generation

| Symptom | Useful check and recovery |
|---|---|
| Clip freezes early | Check decoded coverage, trim-in, and fps; adjust source or slot. See [media layers](scenes.md#media-and-audio-layers). |
| Opaque cutout | Check actual format/alpha over a contrasting background; see [JET](media.md#transparent-video-and-jet). |
| Empty `matte` result | Check subject class; choose a suitable extraction method. See [JET](media.md#transparent-video-and-jet). |
| Cutout holes or halo | Check background similarity/spill and refine key/edges; see [JET](media.md#transparent-video-and-jet). |
| Talking cutout loses sync | Align the performance's soundtrack, trim, speed, and start; see [talking actors](media.md#talking-actors-and-dialogue). |
| Silent or doubled speech | Check stream maps and duplicate audio; rebuild from retained source. See [editing](media.md#direct-video-editing). |
| Music masks speech | Check the final mix; prepare ducking/fades in stems. See [audio](media.md#narration-music-and-audio-preparation). |
| Wrong audio extension | Inspect actual format; keep WAV or transcode. See [audio](media.md#narration-music-and-audio-preparation). |
| Captions drift or lose words | Check segment timing and parsing; obtain word alignment if needed. See [captions](media.md#captions-and-speech-timing). |
| Incompatible-input/422 error | Check frames versus refs and audio's visual companion. See [generation](media.md#video-generation). |
| Fixed-camera/style flag fails | Use supported flags and describe the choice in the prompt. See [generation](media.md#video-generation). |
| Face-related rejection | Inspect the actual error and documented CLI handling before another attempt. See [generation](media.md#video-generation). |
| Model not activated | Use an available model when appropriate or explain the account limitation. Retrying will not activate it. |
| Identity or framing drifts | Clarify reference roles or edit accepted material. See [images](media.md#images-and-consistent-references) and [video](media.md#video-generation). |
| URL fails/expires | Check accessibility and required hosting format; use an authorized replacement. See [hosting](media.md#files-urls-and-public-hosting). |

## Cloud rendering and reusable files

| Symptom | Useful check and recovery |
|---|---|
| Library/auth failure | Check `auth status`, credentials, and selected destination without exposing secrets. See [tools](tools.md#setup-and-command-help). |
| Exporter error 3000 | Inspect details, fonts/assets, and compiled structure; isolate the failure. The code alone is not a diagnosis. See [IDM files](advanced.md#existing-idm-files-and-raw-vasco). |
| Extracted assets conflict | Check `assets.json`, IDs, and warnings; obtain missing originals. See [IDM files](advanced.md#existing-idm-files-and-raw-vasco). |
| Replacement key fails | Match actual exported layer names and editable roles. See [personalization](advanced.md#personalization-and-chart-geometry). |
| Label changes but chart does not | Rebuild geometry or replace an exact chart image; see [charts](advanced.md#charts). |
| Tags fail or slots repeat | Check vocabularies, coverage, and shared indices. See [tagging](advanced.md#tagging-reusable-scenes). |
| Revision introduces a defect | Review this exact final candidate for regressions; see [delivery](delivery.md#review-the-final-candidate). |

## When the cause is still unknown

Record the command/version, input, error, and relevant time or layer. Prefer a
small reproduction that preserves the failing feature. Start with offline
validation or inspection; use an authorized cloud probe only when the question
requires the real renderer.

Existing limitations in these guides are retained from the shipped 1.0.174
documentation and implementation. The copied fixtures identify offline examples,
not proof of every possible visual result. Keep the scope of a finding narrow:
one failure does not establish a universal rule.

After a fix, repeat the check that exposed the defect. If it changes the final
artifact, inspect the relevant final-output behavior as well. Report a remaining
unknown plainly; do not turn a guess into a newly documented engine fact.
