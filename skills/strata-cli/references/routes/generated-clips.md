# Generated video clips — route

**This is the route when** the brief says generate, generated, AI footage, text-to-video, image-to-video, b-roll, footage of, a clip of. Against [footage-edit.md](footage-edit.md): the footage does not exist yet. Against [presenter.md](presenter.md): nobody speaks; if they do, add that route.

**It expects** a shot list with a job per clip, and slot lengths from the storyboard. **It delivers** clips as `video` layers or `.jet` overlays inside the kind's structure: **footage-only**, **brand-film**, **aerial-flyby** or **character-animation** in [video-styles.md](../video-styles.md).

## Settle these before the storyboard (into BRIEF.md; unattended → decide and record in decisions.md)
- Per clip: frame-driven (exact pictures) or reference-driven (consistent cast).
- What recurs across clips; then [production-bible.md](../production-bible.md) before the first one.
- Each clip's slot length and the margin above it; which clips composite over other layers.
- Whether a clip carries its own audio; the comp fps.

## Where it usually sits on the range
The model's own pacing is slow, so generated footage pulls toward the *film, quiet* row; the range comes from the *edit*: more shots, not longer clips. The brief decides; declare the four numbers from a measurement (review.md §7).

## Read, in this order
- [video-generation.md](../video-generation.md): *The one hard rule*; *Writing the prompt*; *Chaining*; the *Checklist*.
- [video-prompts.md](../video-prompts.md): *Shot budget by duration*, then the worked prompt nearest the clip's kind.
- [production-bible.md](../production-bible.md): when a person, product or set appears twice.
- [traps.md](../traps.md): the clip-length, fps and overlay entries, before the first compile.

## Watch for
- a short clip retimed to fit its slot: [video-generation.md](../video-generation.md), *A clip must be ≥ its scene slot*
- an overlay clip prompted with shots and cuts: [video-generation.md](../video-generation.md), *Clips destined for a `.jet` alpha overlay*
- a rejection for a face, logo or product: [video-generation.md](../video-generation.md), *the answer to content rejections*

## Done when
- `ffprobe` duration of every clip beside the storyboard's slot column: none shorter than its row plus the declared margin
- `strata review` reports no freeze inside any video layer
- each clip checked against its storyboard row as it landed; audio stream intact on every `--audio` clip
