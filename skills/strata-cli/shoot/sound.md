# Narration, music and sound effects

Voice from text, a music bed, or a sound effect from a prompt — the three layers of a finished mix.

Part of the asset-generation reference — the index, and every other part, is in [assets.md](../assets.md).

## `strata generate narration "<text>" --voice <voice_id>`
TTS voiceover MP3 (sync). Returns the spoken **duration** in seconds — size the scene around it.
- `strata generate voices [--search <text>]` lists `voice_id  name · gender · accent · use-case`.
- `--voice <voice_id>` (required) · `--normalize <mode>` for text normalization.
**Where the MP3 goes depends on what it is for — and these two are mutually exclusive:**

| The narration is… | Then |
|---|---|
| **Narration / VO over visuals with no one speaking on camera** (motion graphics, b-roll, product shots, kinetic text) | reference the MP3 as an `audio` layer in the scene. This is the normal case |
| **Anyone speaks on camera** — a `{line}` in a text-to-video prompt, a voice fed to `--ref-audio`, an avatar, the user's footage | the **clip's own audio** is the voice. Put the clip in the scene and **do NOT add a TTS of the same line as an audio layer** — it cannot be synced and the voice doubles |

⚠️ **Never lay the source TTS over a clip that was generated from it.** *Measured:* the
spoken lines land **1.3 s, 3.8 s and 6.3 s into their clips** — never at 0 — because the
model places each line against the picture and re-paces the delivery. The MP3 at `t=0` is
seconds early and **no fixed offset fixes it**; you also get the voice twice. Details in
[video-generation-advanced.md](../video-generation-advanced.md).

## `strata generate music "<prompt>" [--duration <sec>]`
An instrumental track (default 30s). Reference as an `audio` layer at low `volume` with
`ducking: true` so it sits under narration. What the model actually delivers — 0 dBFS, BPM
not honoured, a decaying tail, no loop — is one table:
[music.md](music.md#what-generate-music-actually-does--measured).

## Sound effects — the same command with `TrackType: SFX`
A whoosh, a hit or a riser is `generate music` at 1–2 s with `TrackType: SFX` in the prompt. It
comes back at the exact length with the right envelope; a "sustained" effect does not sustain (it
decays about 24 dB over 3.4 s), so anything that must hold is a bed cut to length. The two working
prompts, the `audio` layer that spots each effect with `start`, how to place a whoosh by its own
peak, and the voice / bed / SFX levels are in
[music.md](music.md#the-sound-design-pass--sfx-levels-fades-measured).

---
