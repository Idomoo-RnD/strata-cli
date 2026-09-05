# Edit of supplied footage — route

**This is the route when** the brief says trim, cut down, join, concat, reframe, crop, speed ramp, slow-mo, mute, swap the audio, re-edit. Against [supplied-material.md](supplied-material.md): the footage *is* the piece and the job is mechanical; no scene, an MP4 back. Once the ask becomes "make it look good", or titles are wanted, it is a scene instead.

**It expects** the source files, the ask, and the destination spec (aspect, fps, platform). **It delivers** a new MP4 beside the untouched source, with the exact ffmpeg command stated.

## Settle these before the storyboard (into BRIEF.md; unattended → decide and record in decisions.md)
- Keyframe-aligned copy, or frame-exact re-encode.
- Reframe by crop, blurred fill or letterbox, and what the crop loses.
- Whether the source's audio must survive, and whether a new track replaces it.
- A flat retime, or a ramp for a deliberate beat (`strata retime`).

## Where it usually sits on the range
Wherever the source sits: an edit inherits its footage's shot lengths and energy; a recut for a new platform re-declares them from the destination's grammar. The brief decides; declare the four numbers from a measurement (review.md §7).

## Read, in this order
- [video-editing.md](../video-editing.md): *The three rules that prevent most damage*, then the section for the ask; *Recipes for common asks*; *Output rules*.
- [editorial/workflow-and-qa.md](../editorial/workflow-and-qa.md): *Delivery and technical QC*; *Full-watch QC*.
- [workflow.md](../workflow.md): check B, an edit is not a design job.

## Watch for
- an edit that loses the clip's audio with a clean exit code: [video-editing.md](../video-editing.md), *The three rules that prevent most damage*
- a clip slowed to fill a slot: [video-editing.md](../video-editing.md), *Recipes for common asks*
- titles burned in with `drawtext`: [workflow.md](../workflow.md), *The four checks that change what the job is*

## Done when
- `ffprobe` on the output: the expected streams, matching durations, even dimensions, the target fps
- a frame actually looked at, before and after
- the source untouched, the output a new file, and the exact command handed back with it
