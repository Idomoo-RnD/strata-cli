# Music — prompting `strata generate music` (Stable Audio 3)

```bash
strata generate music "<prompt>" [--duration <sec>] [-o track.wav | --out-dir dir]
```

Behind it is **Stable Audio 3 (medium)** via the Idomoo AI API — verified from the returned
asset path. What that means in practice:

| | |
|---|---|
| **Output** | **44.1 kHz stereo WAV** (verified: `pcm_s16le`, 20.000 s for `--duration 20`) — *not* MP3, despite what you might name the file |
| **Max length** | ~4 min 45 s on the medium model; `--duration` default is 30 s |
| **Instrumental, English** | not designed for speech or singing (voices: `strata generate narration`); other languages underperform |

⚠️ **Extension gotcha:** the bytes are WAV. If you pass `-o track.mp3` you get WAV data under
an mp3 name (the CLI now warns). Either keep `.wav`, or convert:
`ffmpeg -i track.wav -c:a libmp3lame -q:a 2 track.mp3`.

## What `generate music` actually does — measured

Every measured behaviour of the model in one table, with the workaround beside it. The
sections below are the craft; this is the contract to plan on.

| Measured | Workaround |
|---|---|
| **Output is 44.1 kHz stereo WAV** at exactly the `--duration` asked — WAV bytes whatever the `-o` name | keep `.wav`, or convert with ffmpeg (above) |
| **0.0 dBFS, no headroom.** VASCO sums audio layers with no bus limiter; the encoder pins a hot mix near −0.5 dBFS and leaves a quieter one quieter | `loudnorm` the bed to its place under the VO **before import**; layer `volume` is balance, not a route to a level (*Decide the mix*) |
| **BPM is not honoured** — 90 BPM requested came back at ~129; 78 came back at 136 | state a BPM anyway (vague tempo is worse), then read the real onsets with `strata beats` and cut to those |
| **Arc:** intros and hard stops are honoured; a **mid-track breakdown never** (two attempts, two tracks). A timeline was honoured once; two later second-by-second timelines came back **loudest at t = 0, decaying monotonically** (one to −75 dB) | write the timeline — free, sometimes honoured — and never plan the edit on it; rebuild the arc in ffmpeg (sections, levels, cross-fades) and re-run `strata beats` on the cut track |
| **The last ~20 % of a bed decays toward silence** — an exact-fit request ends before the picture does | generate ~20 % longer than the video, trim to length, `afade` the tail |
| **A "sustained" SFX is not sustained** — it decays about 24 dB over 3.4 s | a drone or riser that must hold is a `TrackType: Music` bed cut to length, or a 1–2 s take loop-crossfaded in ffmpeg |
| **SFX at 1–2 s come back with the right envelope and exact length** — the whoosh peaks at ~60 % and tails, the hit is front-loaded and decays; 2.000 s and 1.000 s for `--duration 2` / `1` | `TrackType: SFX`, describe the shape (rise, hit, tail), say `no music` (*sound design pass*) |
| **No seamless loop** — the end never meets the start | generate the full length, or a palindrome/crossfade loop in ffmpeg ([video-editing.md](video-editing.md)) |
| **Instrumental only** — not designed for speech or singing | voices are `generate narration`; `VocalType: Instrumental` on every underscore |
| **English prompts**; other languages underperform | prompt in English whatever language the piece is in |
| **With no target it lands mid-range:** −16.9, −18.7 and −16.8 LUFS on three unrelated briefs | a LUFS target and a loud/quiet gap on the storyboard before the first generation |

---

## Contents

- [What `generate music` actually does — measured](#what-generate-music-actually-does--measured)
- [The prompt formula](#the-prompt-formula)
- [Vocabulary that works](#vocabulary-that-works)
- [Reference prompts (from Stability's own guide — good templates)](#reference-prompts-from-stabilitys-own-guide--good-templates)
- [Decide the mix before you generate the bed](#decide-the-mix-before-you-generate-the-bed)
- [Video-specific craft — this is where a track earns its place](#video-specific-craft--this-is-where-a-track-earns-its-place)
- [The sound design pass — SFX, levels, fades (MEASURED)](#the-sound-design-pass--sfx-levels-fades-measured)
- [Rules](#rules)

## The prompt formula
Stable Audio responds to **layered description in a consistent order**, not a keyword soup:

> **`TrackType` + `VocalType`** → **genre** → **mood & energy** → **instrumentation (and how each
> one sounds)** → **production / recording character** → **BPM**

```
TrackType: Music, VocalType: Instrumental, warm uplifting corporate underscore,
soft felt piano and airy synth pad, gentle pulse building to a hopeful lift,
close-mic'd and analogue, 100 BPM
```

**Lead with the AudioSparx tags** — the docs state these improve results because they match
the training metadata:

| Tag | Use |
|---|---|
| `TrackType: Music` | music (vs `Instrument` for a solo stem, `SFX` for a sound effect) |
| `VocalType: Instrumental` | **always for an underscore** — keeps vocals out of the VO's way |
| `Genre: X` | repeatable to blend: `Genre: Funk, Genre: Jazz` |
| `Instruments: Guitar, Piano, Bass` | name what should play |
| `Format: Duo` | for a small ensemble / two instruments |

## Vocabulary that works
- **Mood:** triumphant · nostalgic · brooding · euphoric · melancholic · hopeful · urgent · serene
- **Production:** analogue · tape-driven · reverb-drenched · distorted · vintage · live-recorded · lo-fi
- **Recording space:** close-mic'd · dead room · studio · cavernous
- **Motion:** pumping four-to-the-floor · syncopated · swung · half-time · driving · sparse

## Reference prompts (from Stability's own guide — good templates)
```
A triumphant and stylish UK bass-flavoured tech-house tune evoking feelings of the last tune
in a DJ set, with pumping four-to-the-floor kick, syncopated 808 bass, gliding synth leads,
playful stabs, and gospel house piano in the drop.
```
What it does: **names the instruments AND their character**, gives the track a *situation*
("the last tune in a DJ set"), and describes the production.

## Decide the mix before you generate the bed

**Two numbers go on the storyboard next to the duration, before any music exists:** an integrated
LUFS target for where the piece will play, and the gap between its loudest and quietest beat.
Without them it lands wherever the generated bed happened to sit — *measured*, three pieces from
this skill, on three unrelated briefs, came back at −16.9, −18.7 and −16.8 LUFS.

| Where it plays | Integrated target |
|---|---|
| broadcast / OTT | **−23 to −24 LUFS** |
| streaming, social, web | **−14 to −16 LUFS** |
| a room you control — showreel, trade-show loop, cinema | louder, to **−9**; say why |

A piece that ignores its delivery spec is wrong however good it sounds, and −9 is not a number to
copy off a reel mastered for a stage.

**How much of the level the scene can set — measured.** Generated audio arrives at **0.0 dBFS with
no headroom**; VASCO **sums the audio layers with no bus limiter** and cannot keyframe `volume`
(below), so the layer dB are balance, not a route to a target — three "sensible" levels simply add
up. The encoder holds a **ceiling near −0.5 dBFS** that a hot mix pins against, while a quieter mix
does come out quieter (*measured* true peaks: −0.44, −0.50, −1.01, −1.07, −1.13, −1.83, −1.87). A
mix already on the ceiling cannot be pushed from inside the scene, and `review`'s "peak above
−1 dBFS" is then clearable only by an ffmpeg pass on the delivered MP4. **The route to a declared
target is pre-mixing before import** — `loudnorm` the bed to where it should sit under the VO,
`afade` the moves, then import.

**The range inside the piece is the craft half.** Reference films measure −31.5 LUFS on a quiet
10 s beat and −14 on an action one; the impact lives in that contrast, and ours are flat because
nothing was asked to be quiet. Name the loudest and quietest beat on the shot list, and the gap.
`volume` cannot be keyframed, so the range is built three ways: **rebuild the arc in ffmpeg** —
sections at different levels, `afade` between them, a hard cut into the drop, which is the
load-bearing one because the generator's own arc is not reliable (below); **leave layers out** — a
beat with no bed and no SFX is the cheapest contrast in the piece, marked planned on the shot list
or the review reads it as a silence gap; and **ask the generator for the dynamics** in the prompt as
a timeline, free and sometimes honoured, never assumed (below).

**Measure the gap as short-term LUFS at two named timecodes, not as LRA.** EBU loudness range gates
at −20 LU relative to the programme level, so it *discards* the quiet beat that gives a piece its
contrast: taking a beat further down can leave LRA unchanged or make it smaller, which reads as
having made the mix flatter when it was made deeper. Declare the gap as two measurements — the
loudest beat and the quietest, each at a timecode — and check them directly:

```bash
# short-term LUFS around one beat: trim to it, then read the Summary block
ffmpeg -hide_banner -ss <t> -t 3 -i out.mp4 -af ebur128 -f null - 2>&1 | grep -A6 Summary:
```

`strata review` reads integrated LUFS against the target and prints loudness range, which stays a
useful sanity number — it just is not the gap ([review.md](review.md)).

## Video-specific craft — this is where a track earns its place
- **`generate music` does not reliably honour an arc or a BPM — build the arc yourself** (the
  measurements: *What it actually does*, above). Phrase the arc as a timeline — *"starts almost
  silent, swells over five seconds, the hit lands at the peak, then holds"* — because it is free and
  sometimes lands, but never plan the edit on it. **The remedy that works is ffmpeg**: cut the
  returned bed into sections, set each one's level, cross-fade them into the arc the storyboard
  asked for, then **re-run `strata beats` on the cut track** so the onsets match what plays.
- **Match BPM to the cut rhythm** you plan (or vice versa). Fast montage → 120–128 BPM; a
  considered product film → 80–100.
- **THEN hand it to `strata beats`.** Generate the track, run
  `strata beats track.wav -o beats.json`, and snap cuts and entrances to the returned onsets —
  never hand-time it ([generative-fx.md](generative-fx.md)).
- **Under a voiceover:** always `VocalType: Instrumental`, ask for a *"sparse mid-range, no
  busy melody"* so it doesn't fight speech, and reference it as
  `{ "type": "audio", "src": "./track.wav", "volume": -10, "ducking": true }`. Around −6 to
  −12 dB with `ducking: true` is the working range (the mix table below settles on −10 to −12
  when a VO sits on top).
- **Length:** generate **~20 % longer than the video** (the last fifth of a bed decays toward
  silence) and cut to length (ffmpeg trim + `afade` out — [video-editing.md](video-editing.md)); an
  exact-fit request ends abruptly. With narration, its returned duration is the target.
- **No seamless loop** — generate the full length, or a palindrome/crossfade loop in ffmpeg.
- **Brand consistency:** record the working music prompt in `.brand/brand.md`
  ([brand.md](brand.md)) so every video in a series shares one sonic identity — the audio
  equivalent of the palette.
- **Ask before assuming.** A piece may already have a licensed track, or need none (social video
  is often watched muted — the visuals must carry it regardless).

## The sound design pass — SFX, levels, fades (MEASURED)

A finished mix has **three** layers, not two: voice, bed, and **sound effects** — without them even
a beat-synced edit reads as "graphics over music". The same command makes them:

```bash
strata generate music "TrackType: SFX, a short cinematic whoosh transition, airy rising swoosh with a soft tail, no music" --duration 2 -o whoosh.wav
strata generate music "TrackType: SFX, a single deep cinematic impact hit, punchy low boom with a short reverb tail, no music" --duration 1 -o hit.wav
```

*Measured* lengths and envelopes are in *What it actually does* (above): exact at 1–2 s, so
`--duration 1–2` is fine; describe the *shape* (rise, hit, tail) and say `no music`. A "sustained"
SFX is not — it decays ~24 dB over 3.4 s — so anything that must hold is a bed, cut to length.

**Where they go** — an `audio` layer with `start` at the moment (verified: a whoosh at `start: 1`
and a hit at `start: 4` landed at 2.0 s peak and 4.0 s onset):
```json
{ "type": "audio", "name": "sfx_whoosh", "src": "./whoosh.wav", "start": 1.0, "volume": -6 },
{ "type": "audio", "name": "sfx_hit",    "src": "./hit.wav",    "start": 4.0, "volume": -3 }
```
Vocabulary: **whoosh** on a move or transition (start it ~0.15 s *before* the cut so the peak lands
on it) · **hit / impact** on a logo land or hard cut · **riser** into the climax, ending on the
downbeat · **tick** on UI and counters · **shimmer** on a reveal. One family per piece, 4–8 uses.

**Levels (dBFS, `volume`):** VO **0** · bed **−10 to −12** with `ducking: true` · SFX **−3 to
−8** (a hit louder than a whoosh) · never more than two SFX overlapping. These are *balance between
layers*, not delivered loudness — the LUFS target above is what the finished file measures. Check the result by
ear *and* by probe — `ffprobe` the rendered MP4's audio stream exists, and listen to the join.

⚠ **`volume` cannot be keyframed.** The audio layer's `animations` accept only `transform`
(schema), so a fade in/out or a swell is done **in ffmpeg before import**
(`afade=t=out:st=9.5:d=0.5`, video-editing.md). The same goes for **trimming into** a track:
there is no audio `offset_frame` — to start the music at 0:12, cut it with ffmpeg first.

## Rules
- **A LUFS target and a loud/quiet gap on the storyboard before the first generation.**
- `TrackType: Music, VocalType: Instrumental` on every underscore.
- **State a BPM** explicitly (`"100 BPM"`). Vague tempo gives generic results.
- **Name instruments and their character**, not just a genre word.
- **Describe the arc as a timeline** ("starts…, swells over…, hits at…") — then check the returned track, and build the arc in ffmpeg when it came back flat or front-loaded.
- Never use this for voice — that's `generate narration`.
- Keep the `.wav`, or convert deliberately; don't mislabel it.
- Generate long, cut short, fade the tail.
