# Media

Use this when choosing, generating, preparing, or editing an asset. Read the
section for the operation you need. Generation examples use descriptive URL
placeholders; replace them with permitted, accessible inputs.

## Contents

- [Choose the input and method](#choose-the-input-and-method)
- [Images and consistent references](#images-and-consistent-references)
- [Video generation](#video-generation)
- [Talking actors and dialogue](#talking-actors-and-dialogue)
- [Narration, music, and audio preparation](#narration-music-and-audio-preparation)
- [Captions and speech timing](#captions-and-speech-timing)
- [Transparent video and JET](#transparent-video-and-jet)
- [Sketches and camera references](#sketches-and-camera-references)
- [Direct video editing](#direct-video-editing)
- [Files, URLs, and public hosting](#files-urls-and-public-hosting)

## Choose the input and method

Inspect supplied files first: subject, dimensions, duration, frame rate, audio,
and what must stay unchanged. Reuse suitable material. Generate what is missing.
Keep originals so a failed edit can be rebuilt without regenerating its source.

| Needed result | Useful method | Main trade-off |
|---|---|---|
| Exact copy, chart, logo, or graphic | Scene layers or an approved source asset | Precise and editable; authored animation |
| New still or a changed image | `generate image`, `edit image` | Prompt/reference control; inspect identity and detail |
| New moving shot | `generate video` | Rich motion; output may depart from the request |
| A shot starting or ending at a chosen image | First/last-frame video mode | Controls endpoints; cannot combine with reference mode |
| Recurring subjects, dialogue, or a shot-plan reference | Reference video mode | Images define identity, audio speech, video staging/edit |
| Presenter built from one still and a voice track | `generate avatar` | Useful fixed source composition |
| Shorten, combine, crop, or repair an existing clip | ffmpeg, `retime`, `grade` | Direct changes with inspectable settings |
| Transparent moving subject | `matte` or `jet` | The method must suit the subject and background |

The choice is about the requested result. A still does not need artificial camera
movement, and an exact data graphic should not depend on image synthesis.

## Images and consistent references

```bash
strata generate image "A ceramic cup on a quiet blue tabletop" --aspect 16:9 -o cup.png
strata generate image "The cup from image 0, viewed from above" --reference cup.png --aspect 16:9 -o cup_top.png
strata edit image cup.png "Remove the spoon; preserve the cup and framing" -o cup_clean.png
```

Image commands accept local images or URLs. Local inputs can be sent directly
to the generation service; they do not need `strata upload`. This still sends
the image to an external service. `--colors` accepts a comma-separated hex
palette. `--reference` may be repeated when several inputs are needed.

In **image generation**, refer to inputs as `image 0`, `image 1`, and so on.
Video references use a different convention, described below.

Give each reference a clear job: identity, product details, location, style, or
composition. Describe the intended change and what should remain. Compare the
result with the source; reference conditioning is not an exact preservation
guarantee. Repeated editing can accumulate drift. Return to an approved source
when a new edit damages details that were already correct.

For recurring subjects, keep a small set of approved identity images and stable
descriptions. A character sheet, product views, location plate, or palette can
help when consistency matters. A reference board is useful if it resolves a
visual decision; it is not required for every asset. Name which image supplies
which subject, rather than asking the model to infer a collage's roles.

Choose independent generation jobs after their shared references are settled.
Run independent jobs together when the tool environment and budget allow it.
Wait for the resulting assets and inspect them before dependent work.

## Video generation

```bash
strata generate video "One continuous shot of a ceramic cup turning on a display stand" --duration 6 --ratio 16:9 -o cup_turn.mp4
strata generate video "The subject moves from the first pose to the last pose in one shot" --first-frame "<start-image-url>" --last-frame "<end-image-url>" --duration 6 --ratio 16:9 -o transition.mp4
strata generate video "The person in [Image 1] follows the staging of <Video_1>" --ref-image "<person-url>" --ref-video "<shot-plan-url>" --duration 8 --ratio 16:9 -o performance.mp4
```

| Mode | Inputs | Use |
|---|---|---|
| Text | Prompt only | A new shot without supplied visual constraints |
| Frame driven | `--first-frame`, optionally `--last-frame` | Start from a chosen composition; optionally target an ending |
| Reference driven | Repeated `--ref-image`, `--ref-video`, `--ref-audio` | Combine identity, staging, motion, and speech references |
| Older image animation | `generate fastvideo <image-or-url> --prompt "motion"` | Separate, simpler one-image animation interface |

For `generate video`, frame and reference inputs are **mutually exclusive**.
`--ref-audio` needs at least one image or video reference, and the clip needs
sound to carry the voice, so the CLI turns `--audio` on for it. These
combinations are checked by the CLI before the request. `fastvideo` is a separate interface;
do not transfer the newer command's reference, endpoint-frame, or audio flags
to it.

The current video interface uses URLs for frames and references. Reuse returned
generation URLs when suitable. If an input exists only locally, follow
[the hosting rules](#files-urls-and-public-hosting).

Video prompt reference names are:

- Images: `[Image 1]`, `[Image 2]`.
- Audio: `[Audio 1]`, `[Audio 2]`.
- Video: `<Video_1>`, `<Video_2>`.

State what each reference controls. A reference video can guide shot order,
camera motion, and cuts, but it may also transfer unwanted surfaces or styling.
If the reference supplies the edit, avoid a contradictory shot list. If only
one aspect should transfer, say which. Compare returned cuts, staging, identity,
and close details; the match is not guaranteed.

Current generation options:

| Option | Behavior |
|---|---|
| `--duration` | 4–15 whole seconds; default 5, or the length of a `--ref-video` |
| `--ratio` | `16:9`, `9:16`, `1:1`, `4:3`, `3:4`, `21:9`, `adaptive`; with a first or last frame the clip takes the image's shape |
| `--audio` | Requests sound in the generated clip, including directed dialogue |
| `--seed` | Holds the random seed; it does not guarantee identity or identical output |
| `--best` | Seedance 2.5 instead of the default fast model; retried once on Seedance 2.0 if 2.5 fails, and the output names the model that made the clip |
| `--model gemini-omni-1.1-flash-preview` | Gemini Omni instead of Seedance; see below |
| `--last-frame-out file` | Saves the returned final frame for a possible continuation |
| `--realistic-human` | Supported only with first/last frames or image references |

Gemini Omni takes a prompt and optionally `--first-frame`, `--duration` 4–10
(default 5) and `--ratio` `16:9` or `9:16`. Its clips always have sound, and
photoreal people need no `--realistic-human`. It has no references, last frame,
seed or video edit; the CLI refuses those flags, so use Seedance for them.
`--last-frame-out` still works (the CLI cuts the clip's own last frame).
*measured:* 7 clips came back in 34–50 s each at 1280x720 or 720x1280, 24 fps,
with an audio track; a first-frame shoe and a photoreal portrait kept their
look; a reference image sent anyway was ignored and the clip showed a
different shoe.

Fifteen seconds is the longest clip. For a longer shot, save the last frame
with `--last-frame-out`, host it, and start the next clip from it with
`--first-frame`, saying what continues.

Resolution is currently capped at 720p. `adaptive` chooses a supported ratio;
it does not preserve every unusual source shape. Set the target ratio explicitly
when framing matters. Inspect the actual returned frame rate and duration before
integrating a clip.

There is no `--style` option. The current model rejects `--camera-fixed`, and the
CLI refuses it. Describe style or a locked camera in the prompt, including what
does move. The CLI may retry an eligible face-related pre-filter rejection once
with `realistic_human`; inspect its message rather than repeatedly submitting
the same failing request. That option does not replace authorization to use
the supplied person or material.

Describe the subject, setting, visible action, camera behavior, and sound needed
for the shot. Keep spatial relationships and action order clear. Specify a
continuous shot or intended cuts when this distinction matters. There is no
required prompt template, number of shots, or cinematic vocabulary.

Allow enough time for the actual action or speech. For a longer result, choose
cuts or continuations according to the piece. A last frame can establish visual
continuity, but cannot describe an unfinished movement by itself; explain what
continues. Save useful local files while signed URLs remain available.

### Put a person into a reference video

To make the person in an image perform a reference video (an actor swap),
pass both and ask for a **new clip** of that person doing the video's actions:

```bash
strata generate video "The woman in [Image 1] stands behind the bakery counter and performs exactly the same actions as the person in <Video_1>: she smiles, picks up a loaf and shows it to the camera. Same camera framing, background, light and timing." --ref-image "<person-url>" --ref-video "<performance-url>" --best --ratio 16:9 -o swap.mp4
```

Leave `--duration` out so the clip takes the reference video's length; the
reference must be 15 s or shorter. Name the actions in the prompt. Do not write
"replace the man in the video": Seedance treats that as a video edit, which the
Idomoo API cannot run, and the CLI says so and shows this form.

*measured:* with a 5-second reference video of a man and a portrait of a woman,
two new-clip prompts on Seedance 2.5 gave her performing his actions frame for
frame, with the same framing, timing, background and light; the "replace the
man" prompt was refused as a video edit. On the same prompt and seed, 2.5 made
the push-in the prompt asked for where 2.0 stayed on a wide static shot.
Clips took 3 to 11 minutes each.

Check generated details at useful resolution: hands, product features, text,
unwanted marks, continuity, and missing actions. Choose a targeted edit, crop,
another take, or an authored replacement according to the defect and budget.
Do not assume `--best` guarantees all requested shots.

## Talking actors and dialogue

Talking actors belong here because they are generated media. Their placement
uses ordinary [video/audio layers](scenes.md#media-and-audio-layers); their final
speech, timing, and edges are checked during [delivery](delivery.md).

Choose between two methods:

| Method | Useful when | Inputs |
|---|---|---|
| `generate avatar` | The composition comes from one still and the presenter speaks a supplied track | Image, audio URL, optional motion prompt |
| `generate video` with references | The speaker acts in a setting, moves through shots, or exchanges dialogue | Identity/staging references and speech audio |

A fixed presenter plate can simplify repeated layouts and personalized versions.
It is not the only possible personalized media. A directed performance gives
more freedom, with more details to verify. Choose based on the desired behavior.

```bash
strata generate voices --search English
strata generate narration "Here is how it works." --voice "<voice-id>" -o speech.mp3
strata generate avatar "<presenter-image-url>" --audio "<speech-url>" --aspect 16:9 --motion "Explains with relaxed hand gestures" -o presenter.mp4
```

For avatars, supported aspects are `16:9` and `9:16`. Match the source image to
the intended framing. Visible posture and available hands affect what movement
the image can support. `--start-silence` and `--end-silence` add padding;
`--image-key` can reuse an existing avatar image key. Measure the resulting
duration rather than assuming it equals the unpadded script length.

For a directed speaker:

```bash
strata generate video "The presenter in [Image 1] speaks [Audio 1] with matching lip movement, explaining to the camera" --ref-image "<presenter-image-url>" --ref-audio "<speech-url>" --audio --duration 8 --ratio 16:9 -o speaking.mp4
```

The duration in this example is illustrative; it must cover the actual recording.
For dialogue, identify speakers, their visual references, which lines each speaks,
and the order. Listen to the output and inspect mouths during speech. Verify
the returned words, voice, pauses, speaker assignment, and sentence endings.

Use the **resulting clip's audio** with its picture. Generated delivery may alter
timing relative to the supplied recording. Layering the original narration over
the returned speech can create duplication or broken lip sync. If a visual-only
cutout is needed, extract the soundtrack from the accepted performance and
preserve its timing:

```bash
ffmpeg -i speaking.mp4 -vn -c:a pcm_s16le speaking.wav
```

Keep the performance's trim, speed, scene start, and extracted soundtrack aligned.
The final mix should contain the speech once.

## Narration, music, and audio preparation

```bash
strata generate narration "Your approved script." --voice "<voice-id>" -o narration.mp3
strata generate music "A restrained instrumental bed with a clear ending" --duration 20 -o bed.wav
strata generate music "TrackType: SFX; a short soft mechanical click" --duration 1 -o click.wav
strata beats bed.wav --fps 25 --bands 12 -o bed.beats.json
```

Choose voice IDs from `generate voices`. Measure the actual narration and check
pronunciation before locking speech-dependent timing.

Narration is multilingual: write the script in the language it should be spoken
in. It uses the `eleven_v4` model by default and retries once on `eleven_v3` if v4
fails (`--model eleven_v3` picks it directly); the command prints which model
made the file. Both models act out **audio tags**: short directions in square
brackets inside the script, which shape delivery and are not spoken.

```bash
strata generate narration "[excited] We did it! [laughs] I can't believe it. [whispers] Keep it a secret for now." --voice "<voice-id>" -o line.mp3
```

- **Emotion and tone:** `[excited]`, `[happy]`, `[sad]`, `[angry]`, `[curious]`,
  `[sarcastic]`, `[warm]`, `[serious]`, `[nervous]`, `[calm]`.
- **Delivery:** `[whispers]`, `[shouts]`, `[softly]`, `[slowly]`, `[quickly]`, `[pause]`.
- **Human sounds:** `[laughs]`, `[chuckles]`, `[sighs]`, `[gasps]`, `[clears throat]`, `[exhales]`.

Put a tag right before the words it colours, and use it where the script means
it: one or two per sentence reads as acting, a tag on every phrase reads as a
caricature. Punctuation still carries rhythm (ellipses for hesitation, a dash
for a break). Tags are written in English whatever the script's language.

*measured:* one tagged line ("[excited] We did it! [laughs] … [whispers] …
[sighs] …") on the same voice, transcribed afterwards: on `eleven_v4` (9.1 s)
and `eleven_v3` (10.3 s) the transcript held only the words, with no tag spoken.
Tagged Spanish and Hebrew lines on both models transcribed word for word, with
the tag not spoken. Music generation is for
music and effects; use narration for controlled speech. The current music output
is WAV data even if given another extension. Keep `.wav` or transcode it.

Music's requested tempo, internal structure, and level are not exact controls.
Analyze the returned file when edits depend on its onsets or loudness. Music can
drive the cut, or be fitted to an existing script or picture; choose the order
that serves the task.

Scene audio `volume` is in dB. Audio volume animation and audio `offset_frame`
are unsupported. Prepare trims, fades, and ducking in audio stems before import.
The existing paired render checks found no audible or decoded-audio change from
the scene's `ducking` flag; do not rely on it for voice clarity.

For example, this applies fades to an eight-second bed:

```bash
ffmpeg -i bed_8s.wav -af "afade=t=in:st=0:d=0.5,afade=t=out:st=7:d=1" bed_faded.wav
```

Measure and balance the actual mix. Several individually valid tracks can sum
too loudly. Use the user's delivery specification for loudness and peaks when
provided; there is no single music level suitable for every voice or piece.
See [audio-driven motion](advanced.md#audio-driven-motion) if graphics should
follow the sound.

### Separate a voice from music

```bash
strata separate interview.mp4 -o stems
```

`separate` splits speech from everything else in an audio or video file:
`stems/interview_voice.wav` and `stems/interview_background.wav` (the mix
minus the voice), 44.1 kHz stereo. Use it to re-balance a supplied mix, put new
music under an existing voice, caption or re-time a voice that has music
under it, or reuse the music without the speech. `--keep voice` writes only the
voice. The model runs on this machine, so nothing is uploaded; the first run
downloads it once (67 MB). It warns when it finds almost no speech.
*measured:* on 16 mixes of four narration takes over four music beds, the
separated voice scored a median 20.6 dB SDR with the music 6 dB under the voice
and 17.2 dB with it 6 dB over (the mix itself: 1.9 and −10.1 dB); on a cloud
render, 16.2 dB against 8.3 for the render as delivered. When the music is much
quieter than the voice, some voice stays in the background track. It took
1.75–3.5 s per second of audio.

## Captions and speech timing

```bash
strata captions "<approved-compatible-S3-url>" -o cues.json --srt subtitles.srt
```

`captions` accepts audio or video with an audio stream; extraction to a separate
audio file is unnecessary. The service requires a compatible S3 URL. The CLI can
convert its upload-store URL into the corresponding S3 address. Local or other
hosted inputs may require public rehosting; apply
[the publication boundary](#files-urls-and-public-hosting).

Caption output is **segment timing**, not word alignment. Splitting a cue evenly
or snapping to `beats` onsets does not recover accurate word boundaries. Use
actual word alignment or manually checked timings when karaoke-style highlighting
needs them. The returned raw caption format has wrapped cue text; use the CLI's
parsed JSON/SRT rather than treating every raw line as a separate cue.

Check transcript, cue start/end, line breaks, and readability against the final
edited speech. Regenerate or retime captions after timing changes.

## Transparent video and JET

JET is the alpha-video format used by these scene tools. Import it as a
`type: "video"` layer with a `.jet` source. An ordinary H.264 MP4 does not carry
the transparency needed for a cutout; use a PNG for a still or JET for motion.

| Input | Method | Check |
|---|---|---|
| Person or suitable character | `matte` | Subject coverage, hair/hands, temporal stability |
| Clearly separated screen color | `jet --method chroma --key R,G,B` | Spill, holes, edges, motion blur |
| Separable uniform background | `jet --method distance --key R,G,B` | Similar subject colors may also disappear |
| PNG sequence with existing alpha | `jet <frames-dir>` | Alpha, dimensions, frame order, and frame rate |
| Object against a complex background | Prepared alpha, manual masks, or same-source occlusion | RVM person matting is not a general object separator |

```bash
strata matte person.mp4 --width 640 -o person.jet
strata jet green_screen.mp4 --method chroma --key 0,177,64 --fps 24 --choke 1 --feather 1 -o subject.jet
strata jet rgba_frames --fps 24 -o overlay.jet
```

The dimensions and frame rates here are examples. Choose suitable dimensions and
conform source frames to the scene rate. Passing a frame rate to an encoder is
not proof that the source motion and soundtrack now have matching timing.

`matte` uses a person-trained model; some stylized characters also work. It does
not reliably isolate products, logos, or landscapes. It refuses nearly empty
results. Existing evidence found a product bottle at only 0.02% peak coverage,
while tested people and characters had substantial coverage. If the subject
class is wrong, change method rather than lowering the empty-alpha guard.

A uniform background is not automatically separable: a pale or reflective
subject may share its colors. White/black keying can remove legitimate highlights
or shadows. When generating an input for chroma extraction, request a background
color distinct from the subject and inspect the actual returned separation.
Keep essential body parts and motion inside the source frame.

Inspect the cutout over both light and dark backgrounds and at overlap points.
Adjust choke, feather, key choice, or source preparation according to the defect.
JET is lossy; quality and size choices should suit the final scale.

For a subject passing in front of a graphic, same-source occlusion can avoid
extracting the entire background: place the original plate below the graphic,
then a masked duplicate of the same plate above it. The foreground copy must
use identical timing, crop, fit, and transforms so surrounding pixels align.
The mask only needs to isolate the required overlap, but must remain correct
throughout that overlap. Keep embedded audio from being played twice.

Use source motion when its real perspective matters. Layer animation can place
or move a cutout, but cannot repair incorrect articulation or perspective inside
the source. Test source coverage through the intended visible duration; a
transparent asset can still freeze when its frames run out.

For a talking cutout, preserve the accepted performance soundtrack as described
under [talking actors](#talking-actors-and-dialogue). Recheck sync after matting,
conforming, trimming, or retiming.

## Sketches and camera references

`strata sketch` provides two different kinds of generation reference:

| Spec | Result | Purpose |
|---|---|---|
| `kind: "pen"` or an `elements` list | PNG | Pose, composition, or concept |
| An animatic spec with `shots` | MP4 | Camera path, staging, and cut timing |

```bash
strata sketch pen-pose.json -o pose.png
strata generate image "Use image 0 for the pose; render the described character" --reference pose.png -o character.png
strata sketch animatic-doorway.json -o plan.mp4
```

A pen spec has no timeline: top-level `duration`, `fps`, `camera`, `boil`, and
`shots` are rejected, as are element `animate`, `draw`, and `start` properties.
An animatic must declare `shots`; each shot defines a cut.
Its world coordinates use Y up, unlike scene canvas coordinates. Use the actual
example structure instead of mixing the two formats.

| Example | What it demonstrates | Dependencies / proof |
|---|---|---|
| [Pen pose](../examples/pen-pose.json) | Character pose construction | Local sketch input; offline fixture |
| [Pen composition](../examples/pen-composition.json) | Composition with grouped drawing elements | Local sketch input; offline fixture |
| [Doorway animatic](../examples/animatic-doorway.json) | Shot timing and moving camera | ffmpeg for MP4; offline fixture |

These are reference assets for generation, not polished scene layers. Feed a pen
drawing to an image-reference input and an animatic to `--ref-video`, using an
authorized URL when required. Tell the model to take staging/camera motion from
an animatic and replace its blocks and labels with the specified real subjects.
Inspect for leaked sketch appearance and differences in cuts or actor placement.

## Direct video editing

Use direct editing for a requested edit. Inspect streams before and after:

```bash
ffprobe -v error -show_entries stream=codec_type,codec_name,width,height,r_frame_rate,duration -show_entries format=duration -of json input.mp4
ffmpeg -i input.mp4 -ss 2 -t 4 -map 0:v:0 -map "0:a?" -c:v libx264 -c:a aac cut.mp4
ffmpeg -i input.mp4 -vf "crop=720:720:280:0" -c:v libx264 -c:a copy square.mp4
strata retime input.mp4 --ramp "0:1,2:0.5,3:1" -o ramp.mp4
strata grade input.mp4 --match reference.mp4 -o graded.mp4
```

The crop assumes a source large enough for those bounds. Choose the crop from
the subject and target frame, not from this example. Re-encoding supports precise
cuts; stream copying is useful when its keyframe restrictions are acceptable.

When an ffmpeg command uses `-map`, only mapped streams survive. Map the intended
audio or the audio filter's output alongside the video. A complex video filter
with only `-map "[v]"` silently produces a silent video. If audio is lost, rebuild
the edit from the retained original; do not regenerate an intact performance.

For concatenation, use the concat demuxer with a list file and `-c copy` only
when clip codecs and stream parameters are compatible. Otherwise normalize
dimensions/frame rate/audio layout and re-encode through the concat filter.
Handle silent clips explicitly if the joined result expects audio.

For compatible clips, a `clips.txt` beside them contains:

```text
file 'first.mp4'
file 'second.mp4'
```

Then run `ffmpeg -f concat -safe 0 -i clips.txt -c copy joined.mp4`.

Speed changes require corresponding audio treatment when sync matters. A video
`setpts` operation alone does not retime speech. After edits, check duration,
aspect, subject coverage, color, transitions, sound, and frame-rate alignment
with any scene or tracking data that uses the result.

## Files, URLs, and public hosting

These are separate operations:

1. Local processing, such as trimming, scene compilation, and JET encoding.
2. Sending an input to an authorized cloud generation or rendering service.
3. Publishing an input through the `strata upload` store.

`strata upload` is **public permanent hosting**, with no expiry or deletion
facility. Only use it for non-sensitive material with informed authorization
covering that publication. Existing permission to generate or render does not
by itself permit public hosting. Never place private/customer/confidential
material in this store. Apply the same rule when another command offers to
upload on the user's behalf.

```bash
strata upload approved_reference.png --allow-public-upload
```

Use that flag only after the publication is authorized. A preference, reusable
recipe, or unattended mode does not create consent. Existing authorization for
the specific material and operation remains valid; do not ask again unnecessarily.

Prefer a suitable existing URL or directly supported local input. When a service
requires a URL for sensitive material, use an authorized compatible hosting path
if one exists; otherwise explain the actual blocker. Do not quietly make a public
copy. A generated URL being reachable is not proof that every recipient or
downstream use is authorized.

Keep source assets, accepted outputs, and useful metadata locally. Verify that
returned files decode and URLs work when a downstream step depends on them.
Do not infer file format from its extension or assume signed URLs stay available.
