# Rhythm, polish and the craft check

Cut rhythm and continuity across the whole piece, the anti-patterns that cheapen it, and the check to run before calling it done.

Part of the craft manifesto — the index, and every other part, is in [craft.md](../craft.md).

### Rhythm & continuity
- **Cut/hit to the audio — with `strata beats`, never by guessing.** `strata beats music.mp3`, then **snap cuts, entrances and accents to the returned onsets**; hand-timed motion against music always reads slightly off. `--fps <scene fps> --bands 12` adds the per-frame envelope for audio-reactive graphics. For narration, size each scene to the **returned duration**. Transitions ~0.3–0.5s. [generative-fx.md](generative-fx.md).
- **Choose the mix target before the bed is generated** — an integrated LUFS for where the piece plays, and a named gap between its loudest and quietest beat, both on the storyboard ([music.md](../shoot/music.md), *Decide the mix*). A piece with no target lands in the middle whatever the brief (*measured:* three pieces, three briefs, −16.9 / −18.7 / −16.8 LUFS), and a piece with no gap has no impact to land. The dB levels below are balance between layers, not delivered loudness.
- **Sound design is part of the cut, not a garnish.** Three layers — voice, bed, **SFX**: a whoosh on the transition (peak on the cut), a hit when the logo lands, a riser into the climax, ticks on counters. `generate music "TrackType: SFX, …" --duration 1` makes them (measured: exact length, correct envelope) and an `audio` layer's `start` places them ([music.md](../shoot/music.md), *sound design pass*). Levels: VO 0 dB · bed −10…−12 with `ducking` · SFX −3…−8. `volume` cannot be keyframed and audio has no trim-in — fades and cut-ins are ffmpeg, before import.
- **Vary energy across the declared range, and vary it hard.** A quiet shot makes the next loud one hit harder. Design the whole arc — intro (tone) → body (escalate) → climax (biggest move/stat) → resolve (logo/CTA) — and remember that averaging to the target is not hitting it: the reference showreel's 1.49 mean sits over shots swinging far above and below it. A flat profile at the right average is still flat, and the same is true of the cut.

### Polish & anti-patterns
- **Polish:** nothing moves linearly; entrances settle rather than pop; elements have weight; one focal point per frame. **Overshoot** (`outBack`/`outElastic`) is allowed only where the material is soft or the brand is deliberately playful — toys, mascots, the memphis style — and then on the hero, once, at the brand's stated amount (the *Playful* archetype's 10–20 % of the move's distance, [brand.md](../brand/brand.md)); body text, numbers, data and anything rigid or premium settle on `outExpo`/`outCubic` and stop clean at 0 %. [anti-slop.md](anti-slop.md) governs the ban; this is the allowance.
- **Amateur tells:** everything fading in the same way at the same time; clutter with no hierarchy; looping a clip to fill time (a visible loop reads cheap — cut to another shot or `playback_mode: "hold"`); decorative motion with no meaning. The rest: [motion-design.md](motion-design.md), *web-animation tells*.

### Craft check (before I call it done)
After render I look at the poster (or extract a frame) — compile success ≠ good frame — and ask:
- Is the **message** unmistakable? Does **every shot** earn its place?
- **Did it land where it said it would?** Measure the four numbers on the render and put them beside the declared ones — including whether the longest shot is the one named to carry the idea. A piece that declared 1.0 s / energy 9 and came back at 2.3 s / energy 3 did not get "a bit slower" — it defaulted, and the fix is the edit, not a tweak.
- Does the piece have **range** in the picture too — a dark frame and a bright one, a small subject and a big one — or is every beat the same size?
- Does the **pacing** let the key shots land? Would the **first three seconds** make someone keep watching?
- Does every move have a job? Do images move; is there depth/camera where the frame felt flat — and nowhere it was flat on purpose?
- The poster answers none of the temporal questions — easing, rhythm, cuts, settles and sound are judged on the **rendered MP4** in step 7's review, never on one frame.

---
