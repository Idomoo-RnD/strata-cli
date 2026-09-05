# Presenter / dialogue — route

**This is the route when** the brief says presenter, spokesperson, talking head, host, avatar, dialogue, speaks, voice-over, narration, lip-sync. The test: a mouth on screen saying the words; then this route joins the piece's own kind. Voice-over with nobody on camera is a narration layer, not this route.

**It expects** a script; sometimes a portrait, a voice sample or a real person's likeness (ask whose). **It delivers** a filmed-looking speaking shot, or a fixed avatar plate, inside the kind's structure: **avatar-presenter** or **testimonial** in [video-styles.md](../video-styles.md).

## Settle these before the storyboard (into BRIEF.md; unattended → decide and record in decisions.md)
- Filmed presenter (`generate video` with references) or avatar plate, trade-off named in the storyboard.
- One speaker or two, and who speaks in every shot.
- The voice: a supplied sample, or a `generate voices` id.
- Whether the presenter moves through a scene or holds a pose.

## Where it usually sits on the range
Speech sets the cut: sentences are the long shots, reactions and inserts the short; between the *film, quiet* and *showreel, 10 s* rows. The brief decides; declare the four numbers from a measurement (review.md §7).

## Read, in this order
- [avatar.md](../avatar.md): *When avatar, and when `generate video` instead*, the route decision.
- [video-generation-advanced.md](../video-generation-advanced.md): *`--ref-audio` — characters that speak YOUR audio*; verifying the voice landed.
- [production-bible.md](../production-bible.md): the presenter as a principal; *6. Voices are principals too*.
- [captions.md](../captions.md): cue timing from the delivered clip.

## Watch for
- a TTS layer under a clip in which the same line is spoken: [traps.md](../traps.md), *The traps, in full*
- an avatar picked silently for a non-personalized piece: [avatar.md](../avatar.md), *When avatar, and when `generate video` instead*
- `--ref-audio` on a non-speaker: [intake.md](../intake.md), *3. Route every scene to a mode*

## Done when
- `strata captions` on each clip returns the scripted line, byte-identical to the TTS text
- `ffprobe` on every edited talking clip: video and audio streams of matching duration
- `strata review` shows no freeze on the mouth shot and no doubled voice
