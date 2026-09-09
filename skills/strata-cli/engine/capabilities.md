# Capabilities — intent to implementation, with honest limits

Read before promising an unfamiliar feature. This is a map of documented support, not a claim
that all effects equal After Effects or physical 3D. Exact syntax lives in the linked pages;
command usage/side effects in [all-commands.md](../cli/all-commands.md).

| Intent | Class | Strata route | Boundary / proof |
|---|---|---|---|
| Cohesive easing language | compiler sugar → native per-frame arrays | scene `eases`, Bézier/points/spring channels | [animation](animation.md); spring duration is physical, not arbitrary |
| Meaningful kinetic text | native animators + compiler tweening | ranges, tracking, styles, character offsets | [text](text.md); actual glyphs/ranges need rendered proof |
| Group motion | native sub-compositions | animate comp instance once | [comps](comps-camera.md); explicit duration; not a full per-layer parent rig |
| Depth and parallax | native 3D layers/camera | positions, calibrated projection, camera blur | [camera](comps-camera.md); layer paint order still matters |
| Extruded type | approximation | z-stepped text copies, `add extruded-title` | [blocks](blocks.md); not mesh extrusion or physical lighting |
| Depth of field | approximation | depth-aware authored blur / pre-rendered footage | [FX recipes](../recipes/fx.md); no native physical camera DOF claim |
| Draw-on / route / progress arc | compiler sugar | SVG path mask, stroke and trim | [masks](effects-masks.md); prefer for exact solid-color paths |
| Arrays / stagger | compiler sugar | `repeat` expands at compile time | [repeat](effects-masks.md); inspect expanded names and timing |
| Matte reveal / footage in type | native composition | alpha/luma track matte, mask | [masks](effects-masks.md); same-comp sources; masks move with layers |
| Cut-out footage | external/local preparation + native alpha media | `matte` or keyed `jet` | [alpha](../shoot/alpha.md); subject coverage, edge quality and fps matter |
| Surface tracking | local tool → native channels | `track`, corner pin on comp/solid | [tracking](../recipes/tracking.md); inspect drift across shot |
| Layer styles | native subset | shadow, glow, stroke, color overlay | [effects](effects-masks.md); not inner shadow/bevel/satin/gradient-overlay parity |
| Audio-reactive graphics | compiler sugar | `beats` envelope → animate channel | [animation](animation.md); audio volume itself is not an animatable channel |
| Fades/ducking/audio trim | external preparation | ffmpeg mix before scene import | [music](../shoot/music.md); remeasure delivered mix |
| Personalized content | native placeholders | `render --data`, timeline contract | [personalization](personalization.md); content substitution, not geometry |
| Changing chart geometry | author-time generation | `chart` per-row scene or exact chart-image replacement | [strategy](personalization.md#chart-strategy--choose-before-authoring); no label-only deception |
| Reference-driven footage | cloud generation | reference modes, production bible, sketch animatic | [video](../video-generation.md); clips capped at 720p; generation is not exact deterministic geometry |
| Speech timings | cloud tool | `captions` | [captions](../shoot/captions.md); segment-level, not word-accurate |
| 4K / high-bit-depth master | external or unverified delivery path | establish the actual destination workflow before promising | scene comps are limited to 1920 per axis; an upscale does not add source detail |
| Web reduced-motion behavior | external web integration | alternate rendered asset and application policy | MP4 itself does not respond to OS preferences |

## Choose the simplest sufficient route

Start with viewer intent, not available effects. Native geometry is appropriate for exact diagrams,
icons, abstractions and data. Images/footage are appropriate when photographed surfaces or subject
motion carry the message. An intentionally flat frame can pass without camera, lighting or texture.

Distinguish **source resolution**, **comp resolution** and **delivery resolution**. Generated 720p
footage in a 1080p scene is already enlarged; a further crop/push reduces useful detail. No promise
of native 4K scene authoring or a 10-bit master should be made from generic industry advice.
Probe unknown renderer behavior in an approved library and record evidence/scope in
[evidence-index.md](../qa/evidence-index.md), rather than inventing scene properties.
