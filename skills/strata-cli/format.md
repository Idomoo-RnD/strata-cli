# Compact scene format — full reference

Compiled by `strata compile <scene.json>` into VASCO, schema-validated, then encoded to `.idm`.

## Contents

- [Scene](engine/scene.md#scene)
  - [Version control — automatic, two layers *(measured 2026-08-24)*](engine/scene.md#version-control--automatic-two-layers-measured-2026-08-24)
- [Layers (common)](engine/layers.md#layers-common)
- [Text](engine/text.md#text)
  - [Right-to-left scripts (Hebrew, Arabic) — they already work; the flag does not](engine/text.md#right-to-left-scripts-hebrew-arabic--they-already-work-the-flag-does-not)
- [Image / Video (media)](engine/layers.md#image--video-media)
- [Solid](engine/layers.md#solid)
- [Audio](engine/layers.md#audio)
- [Sub-compositions](engine/comps-camera.md#sub-compositions)
- [Camera](engine/comps-camera.md#camera)
  - [The camera is an exact pinhole — here is the calibration (MEASURED)](engine/comps-camera.md#the-camera-is-an-exact-pinhole--here-is-the-calibration-measured)
- [Tween engine (`animate`)](engine/animation.md#tween-engine-animate) — incl. [custom easing](engine/animation.md#custom-easing--four-forms-and-a-name-for-each) and [audio-driven channels](engine/animation.md#audio-driven-channels--a-property-that-follows-the-music)
- [Effects](engine/effects-masks.md#effects)
- [Masks](engine/effects-masks.md#masks) — incl. [strokes and draw-on](engine/effects-masks.md#strokes-and-draw-on--stroke--trim) and [repeat](engine/effects-masks.md#repeat--one-object-a-step-a-stagger)
- [Track mattes](engine/effects-masks.md#track-mattes)
- [Colors](engine/scene.md#colors)
- [Generating assets (Idomoo AI API)](engine/data.md#generating-assets-idomoo-ai-api)
- [Personalization — design for replaceable elements](engine/data.md#personalization--design-for-replaceable-elements)
- [Graphs & charts — dynamic images](engine/data.md#graphs--charts--dynamic-images)
- [Unpacking and repacking an existing `.idm`](engine/interop.md#unpacking-and-repacking-an-existing-idm)
  - [⛔ Two assets with IDENTICAL BYTES crash the exporter (error 3000)](engine/interop.md#-two-assets-with-identical-bytes-crash-the-exporter-error-3000)
  - [⚠️ The encoder stores only an asset's BASENAME](engine/interop.md#️-the-encoder-stores-only-an-assets-basename)
- [Raw VASCO passthrough](engine/interop.md#raw-vasco-passthrough)

Each part is self-contained. `strata validate` and `strata compile` enforce most of what is here; the parts a validator cannot see are marked ⛔ or ⚠.
