# Reference-film styles — "make it feel like X", translated

Briefs arrive naming films, directors, studios and campaigns. This file maps the common
names to a **measurable motion signature** — what to actually type into a scene or a
prompt — and to the nearest built entry in [video-styles.md](video-styles.md), which has
the rendered mechanics.

**The anti-slop rule governs here with full force:** a reference is a *technique source*,
never a costume. Take the signature (the moves, the timing, the discipline), not the
surface (their palette on your brand's product is a costume — the brand's atoms always win,
[brand.md](brand.md)). And a signature is a starting point the brief's own subject then
bends.

| Reference named | The measurable signature | Nearest built style |
|---|---|---|
| **Wes Anderson** | dead-centre symmetry, flat frontal framing, whip-pan between tableaux, tabular/centred type with wide tracking, one pastel ground per scene, deadpan hold before every cut | `swiss-grid` + the whip pan ([motion/04-transitions.md](motion/04-transitions.md)) |
| **Apple keynote / product film** | seamless white or black void, slow orbital or lateral dolly, one product one light, type appears in stillness never over motion, generous holds | `luxury`, `product-hero` |
| **Nike / sports hype** | one word per shot on the cut, hard contrast, speed ramps into impact, aggressive letter-spacing, real texture (sweat, rain, breath) | `sports-hype` |
| **A24 / indie title card** | small type in a huge quiet frame, grain as a chosen texture, long holds, serif or mono, sound-led cuts | `footage-only` (its one letterspaced line) + `brand-film` |
| **Kurzgesagt / explainer** | flat 2.5D vector world, everything enters with purpose, constant gentle motion, saturated but disciplined palette | `motion-explainer` + `character-animation` |
| **Stranger Things / retro title** | glowing serif outlines tracking slowly apart on black, analogue flicker, synth-driven | `retro-analog` + `aurora-gradient` (the glow — the one atmospheric) |
| **Blade Runner / neon noir** | rain + neon + volumetrics, silhouettes against signage, slow push through haze | `footage-only` (cinematic dark) + `papercut-parallax` for the haze planes; the neon-rain mechanics of the *Vertical social hook* prompt ([video-prompts.md](video-prompts.md)) |
| **Studio Ghibli** | painterly stills with drifting cloud/grass motion, no cuts inside a scene, weather as emotion | `liquid-morph` (organic drift) + the *Place / establishing* prompt ([video-prompts.md](video-prompts.md)) |
| **Bond / Saul Bass titles** | flat graphic silhouettes, bold two-colour geometry, shapes that become scenes, music-locked | `kinetic-typography` + `liquid-morph` (shape morph) |
| **The Social Network / Fincher** | locked or dolly-only camera, cold grade, tabular numerals, cuts on information not emotion | `data-report` + a cool, clean grade ([video-editing.md](video-editing.md)) |
| **Super Bowl ad (celebrity spot)** | star close-up early, joke structure (setup shot / turn shot / payoff shot), product only in the last 20% | `before-after` + `testimonial` structure |
| **Old Spice / absurdist ad** | single continuous impossible shot, deadpan presenter to camera, props enter frame not cuts | `avatar-presenter` + a one-take prompt ([video-prompts.md](video-prompts.md), *Logo / brand sting* is the one-take pattern) |
| **TikTok native** | jump cuts on the beat of speech, text chunks timed to what is said, camera never still but never smooth | `ugc-selfie` + the `paid-social` hook + captions timing ([captions.md](captions.md)) |
| **NFL/ESPN broadcast package** | metallic extruded type, light sweeps, hard wipes with SFX on every transition | `logo-ident` + the `extruded-title` block ([blocks.md](blocks.md)) |
| **Terrence Malick** | handheld drift toward light, sun flare through subjects, whispered fragments, cuts mid-motion | `footage-only` + the *Place / establishing* prompt + a warm-film grade ([video-editing.md](video-editing.md)) |
| **Anime action** | speed lines, held impact frames (2–3 frozen frames on the hit), background streaks during motion | `glitch` + retime `--ramp` ([video-editing.md](video-editing.md)) |
| **Documentary vérité** | imperfect reframing mid-shot, snap zooms, available light, subjects mid-action never posed | `ugc-selfie` + `event-recap` + [editorial/nonfiction-formats.md](editorial/nonfiction-formats.md) |
| **A Wong Kar-wai mood** | step-printed motion smear (slow shutter), saturated neon interiors, obstructed framing through doorways | `liquid-morph` + retime below 0.5× |
| **A Pixar short** | staging in depth (foreground/mid/background all alive), squash-and-stretch on the hero only, emotion carried by pantomime not dialogue | `character-animation` + `orbit-dolly` (the camera push) |
| **Bauhaus / Swiss poster in motion** | grid-locked type slides on the grid lines only, primary shapes rotating in constructive assembly, hard cuts no easing flourishes | `swiss-grid`, `memphis` (bauhaus, [motion/08-styles-index.md](motion/08-styles-index.md)) |
| **Vaporwave / Y2K** | chrome type, checkerboards, deliberate artefacts, sunset gradients — the ONE palette exception, because the palette IS the reference | `retro-analog` + halftone ([recipes.md](recipes.md)) |
| **Netflix "ta-dum" style sting** | one mark, one move, one bass hit, under 4 seconds, black | `logo-ident` + the *Logo / brand sting* prompt ([video-prompts.md](video-prompts.md)) |
| **Corporate keynote opener** | wide aerial establishing → data moments → people at work → wordmark; even pacing, no shot under 2 s | `brand-film` + `aerial-flyby` (establishing) + `data-report` |
| **Music-video performance cut** | every cut on the beat, alternating performance/narrative, escalating shot rate to the chorus | `audio-reactive` + the *Music-choreographed* prompt ([video-prompts.md](video-prompts.md)) |
| **Hitchcock suspense** | the audience knows more than the subject: cut between the threat and the unaware, each return closer, hold past comfort | `footage-only` + thriller information release ([editorial/narrative-genres.md](editorial/narrative-genres.md#thriller-and-suspense)) |

When a brief names something not in this table: extract the signature yourself — symmetry
or asymmetry, camera locked or moving, cuts on beats / information / emotion, type big or
small, palette flat or photographic — write those five answers down, and build from the
nearest row. Add the new row here (this file is append-friendly; one line per reference).
