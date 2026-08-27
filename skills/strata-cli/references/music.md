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
| **Instrumental only** | the model is **not designed for speech or singing** — voices come from `strata generate narration` |
| **English prompts** | other languages underperform |

⚠️ **Extension gotcha:** the bytes are WAV. If you pass `-o track.mp3` you get WAV data under
an mp3 name (the CLI now warns). Either keep `.wav`, or convert:
`ffmpeg -i track.wav -c:a libmp3lame -q:a 2 track.mp3`.

---

## Contents

- [The prompt formula](#the-prompt-formula)
- [Vocabulary that works](#vocabulary-that-works)
- [Reference prompts (from Stability's own guide — good templates)](#reference-prompts-from-stabilitys-own-guide--good-templates)
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

### The four elements to always cover
1. **Genre** — what style is it?
2. **Instruments** — what plays, *and how each one sounds/feels*.
3. **Mood & energy** — the emotion, not just the tempo.
4. **BPM** — state it explicitly (`"100 BPM"`).

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

A quirky alternative pop ballad instrumental with a long car journey vibe, featuring
infectious synth bass, tightly tuned acoustic drums, dreamy fuzzy synths, indie rock guitar
motifs, and 90s internet nostalgia with brooding, melancholic character.

A funky hip hop instrumental with 70s TV show theme vibes, live recorded instrumentation
including rhythm guitar, flanger-effected lead guitar, close-mic'd electric piano, flute, and
full-swing old school drums with warm, textured analogue sound.
```
Notice what they all do: **name the instruments AND their character**, give the track a
*situation* ("last tune in a DJ set", "long car journey"), and describe the production.

## Video-specific craft — this is where a track earns its place
- **Write the ARC, not just a vibe.** A 20 s ad has a shape: hook → build → lift → resolve.
  Say so — *"sparse and curious for the first bars, drums enter and build, a hopeful lift, then
  a clean resolve"* — and it lands under the shots instead of running flat underneath them.
- **Match BPM to the cut rhythm** you plan (or vice versa). Fast montage → 120–128 BPM; a
  considered product film → 80–100.
- **THEN hand it to `strata beats`.** Generate the track, run
  `strata beats track.wav -o beats.json`, and snap cuts and entrances to the returned onsets —
  never hand-time it ([generative-fx.md](generative-fx.md)).
- **Under a voiceover:** always `VocalType: Instrumental`, ask for a *"sparse mid-range, no
  busy melody"* so it doesn't fight speech, and reference it as
  `{ "type": "audio", "src": "./track.wav", "volume": -10, "ducking": true }`. Around −6 to
  −12 dB with `ducking: true` is the working range.
- **Length:** generate **slightly longer than the video** and cut to length (ffmpeg trim +
  `afade` out — [video-editing.md](video-editing.md)), rather than asking for an exact fit and
  getting an abrupt ending. If there's narration, its returned duration is the target.
- **It does not loop seamlessly.** For a looping social piece, either generate the full length
  or build a palindrome/crossfade loop in ffmpeg.
- **Brand consistency:** record the working music prompt in `.brand/brand.md`
  ([brand.md](brand.md)) so every video in a series shares one sonic identity — the audio
  equivalent of the palette.
- **Ask before assuming.** A piece may already have a licensed track, or need none at all
  (some social video is watched muted — the visuals must carry it regardless).

## The sound design pass — SFX, levels, fades (MEASURED)

A finished mix has **three** layers, not two: voice, bed, and **sound effects**. The SFX are
what make a transition feel cut and a logo feel landed; without them even a beat-synced edit
reads as "graphics over music". The same command makes them:

```bash
strata generate music "TrackType: SFX, a short cinematic whoosh transition, airy rising swoosh with a soft tail, no music" --duration 2 -o whoosh.wav
strata generate music "TrackType: SFX, a single deep cinematic impact hit, punchy low boom with a short reverb tail, no music" --duration 1 -o hit.wav
```

*Measured:* both came back as exactly 2.000 s and 1.000 s of 44.1 kHz stereo, and the
**envelopes are the right shapes** — the whoosh rises to its peak at ~60 % and tails off, the
hit is front-loaded and decays. So `--duration 1–2` is fine; describe the *shape* (rise, hit,
tail) and say `no music`.

**Where they go** — an `audio` layer with `start` at the moment (verified: a whoosh at
`start: 1` and a hit at `start: 4` landed at 2.0 s peak and 4.0 s onset in the render):
```json
{ "type": "audio", "name": "sfx_whoosh", "src": "./whoosh.wav", "start": 1.0, "volume": -6 },
{ "type": "audio", "name": "sfx_hit",    "src": "./hit.wav",    "start": 4.0, "volume": -3 }
```
Vocabulary worth having: **whoosh / swoosh** on a move or a transition (start it ~0.15 s
*before* the cut so the peak lands on it) · **hit / impact / boom** on a logo land or a hard
cut · **riser** under the build into the climax, ending on the downbeat · **tick / click** on
UI and counters · **shimmer / chime** on a reveal. One SFX family per piece, used 4–8 times,
not twenty different ones.

**Levels (dBFS, `volume`):** VO **0** · bed **−10 to −12** with `ducking: true` · SFX **−3 to
−8** (a hit louder than a whoosh) · never more than two SFX overlapping. Check the result by
ear *and* by probe — `ffprobe` the rendered MP4's audio stream exists, and listen to the join.

⚠ **`volume` cannot be keyframed.** The audio layer's `animations` accept only `transform`
(schema), so a fade in/out or a swell is done **in ffmpeg before import**
(`afade=t=out:st=9.5:d=0.5`, video-editing.md). The same goes for **trimming into** a track:
there is no audio `offset_frame` — to start the music at 0:12, cut it with ffmpeg first.

## Rules
- `TrackType: Music, VocalType: Instrumental` on every underscore.
- **State a BPM.** Vague tempo gives generic results.
- **Name instruments and their character**, not just a genre word.
- **Describe the arc** if the video has one.
- Never use this for voice — that's `generate narration`.
- Keep the `.wav`, or convert deliberately; don't mislabel it.
- Generate long, cut short, fade the tail.
