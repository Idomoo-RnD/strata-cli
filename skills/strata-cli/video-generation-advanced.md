# Video generation — references, dialogue, shot control, editing

Advanced modes of `strata generate video`. Read
**[video-generation.md](video-generation.md)** first — the hard rule, `--realistic-human`,
prompt anatomy and chaining all live there and all still apply.

**Reminder of the hard rule:** `--ref-image` / `--ref-video` / `--ref-audio` can never be
combined with `--first-frame` / `--last-frame`. Attaching any reference makes a frame an
instant `422`. Want a reference look *and* a controlled start frame? Generate the
reference-driven clip first, then chain off its last frame.

*(None of this applies to `strata generate fastvideo` — see the end of this file.)*

---

## Contents

- [🔑 How to address a reference — get this wrong and it is never cited](shoot/ref-image.md#-how-to-address-a-reference--get-this-wrong-and-it-is-never-cited)
- [`--ref-image` — carry a character, a product or a world across clips](shoot/ref-image.md#--ref-image--carry-a-character-a-product-or-a-world-across-clips)
  - [Give every image an explicit JOB](shoot/ref-image.md#give-every-image-an-explicit-job)
  - [Build the reference images for the job](shoot/ref-image.md#build-the-reference-images-for-the-job)
  - [Measured](shoot/ref-image.md#measured)
  - [Notes](shoot/ref-image.md#notes)
  - [Checklist](shoot/ref-image.md#checklist)
- [`--ref-video` — control the SHOTS](shoot/ref-video.md#--ref-video--control-the-shots)
  - [Use 1 — mimic footage the user gave you](shoot/ref-video.md#use-1--mimic-footage-the-user-gave-you)
  - [Use 2 — block the shot yourself with a grey-box animatic](shoot/ref-video.md#use-2--block-the-shot-yourself-with-a-grey-box-animatic)
  - [The strongest combination: sheets + plate + sketch](shoot/ref-video.md#the-strongest-combination-sheets--plate--sketch)
  - [⚠ What does NOT transfer cleanly](shoot/ref-video.md#-what-does-not-transfer-cleanly)
  - [Checklist](shoot/ref-video.md#checklist)
- [`--ref-audio` — characters that speak YOUR audio](shoot/ref-audio.md#--ref-audio--characters-that-speak-your-audio)
  - [One speaker — say all three things, or it narrates instead of acting](shoot/ref-audio.md#one-speaker--say-all-three-things-or-it-narrates-instead-of-acting)
  - [Two speakers — nail the roles down BEFORE the shot list](shoot/ref-audio.md#two-speakers--nail-the-roles-down-before-the-shot-list)
  - [Verify the voice actually landed — don't trust your ears](shoot/ref-audio.md#verify-the-voice-actually-landed--dont-trust-your-ears)
  - [🔇 NEVER lay the source TTS over the generated clip — use the CLIP'S audio](shoot/ref-audio.md#-never-lay-the-source-tts-over-the-generated-clip--use-the-clips-audio)
  - [Notes and checklist](shoot/ref-audio.md#notes-and-checklist)
  - […or use `generate avatar` instead — the decision](shoot/ref-audio.md#or-use-generate-avatar-instead--the-decision)
  - [`--ref-audio` with MUSIC — choreograph the clip to a track](shoot/ref-audio.md#--ref-audio-with-music--choreograph-the-clip-to-a-track)
  - [A series — same skeleton, swap the theme](shoot/ref-audio.md#a-series--same-skeleton-swap-the-theme)
- [`strata sketch` — author the camera plan as a 3D animatic](shoot/sketch.md#strata-sketch--author-the-camera-plan-as-a-3d-animatic)
- [Editing and extension — ⚠ LAST RESORT](shoot/extend.md#editing-and-extension---last-resort)
  - [🛑 Reach for these LAST](shoot/extend.md#-reach-for-these-last)
  - [Editing — change one thing, freeze everything else](shoot/extend.md#editing--change-one-thing-freeze-everything-else)
  - [Extension — the next N seconds of the same take](shoot/extend.md#extension--the-next-n-seconds-of-the-same-take)
  - [Checklist](shoot/extend.md#checklist)
- [Choosing a mode](shoot/choosing-a-mode.md#choosing-a-mode)
- [What `generate fastvideo` cannot do](shoot/choosing-a-mode.md#what-generate-fastvideo-cannot-do)

A reference is cited only if it is addressed correctly — read the addressing rule at the top of `ref-image.md` before any of the rest.
