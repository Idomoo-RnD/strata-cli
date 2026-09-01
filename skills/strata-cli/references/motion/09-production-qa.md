# Production, formats, accessibility, QA, anti-patterns, defaults

> Our own delivery gates - the Definition of Done, `strata validate`, `strata preview` and a
> `strata snapshot` before spending a render - are in SKILL.md. Use this file for the craft checks
> those do not automate.

## Contents

- [1. Formats and technical baselines](#1-formats-and-technical-baselines)
- [2. Web and HTML-native motion](#2-web-and-html-native-motion)
- [3. Accessibility and safety](#3-accessibility-and-safety)
- [4. QA checklist](#4-qa-checklist)
- [5. Anti-patterns (the most common mistakes)](#5-anti-patterns-the-most-common-mistakes)
- [6. Default numbers (starting points; tune to the brief)](#6-default-numbers-starting-points-tune-to-the-brief)


## 1. Formats and technical baselines
- **Frame rates**: 24 (cinematic), 25 (PAL, broadcast, many pipelines), 30 (web, US), 60 (UI, gaming, smooth slow-mo); pick one per project and design in frames.
- **Shutter and motion blur**: 180° shutter (1/50 s at 25 fps) as the natural baseline; shorter for crisp, longer for dreamy.
- **Aspect ratios**: 16:9 (web, TV), 9:16 (stories, reels), 1:1 and 4:5 (feeds), 21:9 (cinematic), 4:3 (retro); design 9:16 and 16:9 layouts separately rather than cropping.
- **Safe areas**: title-safe ~90% and action-safe ~95% for broadcast; social overlays eat the bottom 20-25% and top 10-15% of vertical frames. **For a VASCO canvas use the actual grid, margin and bottom safe line in [layouts.md](../layouts.md)** rather than these percentages.
- **Resolution and scaling**: author at 1080p or 4K; vectors scale, textures don't; test at phone size.
- **Color space**: sRGB or Rec.709 for delivery; dither gradients to avoid 8-bit banding.
- **Codec and bit depth**: H.264 or H.265 for delivery, ProRes or PNG sequences for masters; 10-bit for gradient-heavy work.
- **First frame and last frame**: the first frame is the thumbnail, the last frame is the end card; both must stand alone.
- **Loop seams**: the last frame leads into the first; match position, velocity, and audio.
- **Hold times**: end cards 2-3 s; logos 1.5 s minimum at rest; disclaimers long enough to read twice.
- **Audio delivery**: stereo, 48 kHz, -14 LUFS (stream) or -23 LUFS (broadcast), true peak under -1 dB.
- **Variable-length templates**: every timing is relative to beats, not absolute, so longer VO or longer names stretch gracefully.

## 2. Web and HTML-native motion

Only for a web deliverable — VASCO renders server-side and none of this applies to a scene here.
Animate transform and opacity, never layout properties (width, top, margin), which force reflow;
keep large blurs and filters small in area or pre-render them; the frame budget is 16.7 ms per
frame at 60 fps. Drive animation from a timeline rather than frame callbacks so renders match
previews, auto-fit dynamic text before animating it and test the longest and shortest values, and
check fonts, blend modes and filters in the render path so browser and renderer agree.

## 3. Accessibility and safety
- **Reduced motion**: a variant with fades instead of moves and no parallax; honor the OS preference in web contexts.
- **Flash limits**: no more than three flashes per second; avoid large high-contrast flicker (photosensitivity). **Motion sickness**: limit zooms, parallax and rolling cameras in long pieces; keep the horizon stable.
- **Contrast**: text at least 4.5:1 against its background through the whole move, not only at rest - over footage that means a scrim matched to the plate's brightness ([blueprints.md](../blueprints.md)), not a fixed opacity. **Minimum text size**: about 4% of frame height on mobile for body, 6%+ for headlines; larger for broadcast.
- **Captions**: burned-in or sidecar; same motion rules as titles; never over the lower UI band on social. **Color-blind safety**: never encode meaning in red vs green alone; add shape or label.
- **Cognitive load**: one moving idea at a time; pause on anything that must be understood. **Localization**: plan 30% text expansion, RTL mirroring of directions and staggers, script-appropriate fonts.

## 4. QA checklist

> Three gates, and they are different. This list is the **craft** gate (is the motion well made).
> The **style** gate - does it read as broadcast motion rather than web animation - is the tells
> checklist in [motion-design.md](../motion-design.md). The **shipping** gate is the Definition of
> Done in SKILL.md. Run all three; passing one does not imply the others.

No text over text and nothing crossing another element unintentionally on any frame · dynamic text
fits at its longest, nothing clipped by the frame or a container · no orphan trailing words ·
nothing appears or disappears without an intro or outro, unless it is a deliberate cut-in on a beat
· no sub-pixel drift or shimmer on small text and thin lines · one easing system, and any linear
move that is not a loop or a mechanical element is flagged · scales and rotations pivot from the
right anchor · stacking never changes without a reason · text lands on VO words, hits on sounds,
cuts on beats · every text element survives the read-it-twice rule · no move ends abruptly, the last
20-30% decelerates · the frame is clean before the next hero arrives · loop seams and the first and
last frames checked at 1x and frame-stepped · nothing important under platform UI · gradients
dithered, blacks not crushed by accident · audio levels, ducking, sync and no clipping · **for
data-driven video, test minimum, maximum, empty and absurd values** - the longest name and the
biggest number are what break a template, which is core to every Idomoo piece
([personalization.md](../personalization.md)), and batch-render the edge rows with `render --data` ·
**watch tests**: 1x for rhythm, frame-step for errors, muted for story, phone for hierarchy.

## 5. Anti-patterns (the most common mistakes)

> These are **motion** anti-patterns. The **composition** defaults an agent falls into - scrims, rules under titles, side bars, effect stacks, decorative structure, the no-brand palettes - live in [anti-slop.md](../anti-slop.md), which is mandatory and is the only place that list is maintained.

Linear easing on entrances and exits · everything moving at once, so no focal point ·
over-animation that decorates instead of communicates · inconsistent easing or durations between
similar elements · text that moves while it should be read · overshoot on body text or data ·
unmotivated camera moves and wandering parallax · transitions that upstage content · elements
popping in with no entrance · exits slower than entrances · too many typefaces, sizes or colors in
one frame · ambiguous z-order and elements sliding through each other · ignoring the first and last
frames · **dead holds that look frozen rather than resting** · sound as an afterthought · designing
only 16:9 and cropping to 9:16 · gradient banding, sub-pixel shimmer and jitter left unchecked ·
templates that break on long names or large numbers · scale-from-zero entrances for everything
(start at 0.9-0.95 unless zero is the story).

## 6. Default numbers (starting points; tune to the brief)

> These are general-industry starting points. **Where [motion-design.md](../motion-design.md) states a number for the same thing, that one wins** - it was measured on this engine. Use these for the things it does not cover.

- Entrance 400-600 ms; UI micro 100-200 ms; hero 800-1500 ms. **Exit duration and sibling stagger: use the ratios in [motion-design.md](../motion-design.md)**, not a fixed ms figure. Within a text block the useful split is characters 15-40 ms, words 60-120 ms, lines 100-200 ms - map these to a per-character animator, not to separate layers.
- Overshoot 5-10%; spring damping 0.7-0.9 for subtle, about 0.5 for playful.
- Parallax ratios: back layers 0.1-0.6 of the camera move, front layers 1.1-1.5 - the wider end reads as drama, the narrow end as depth. (Section 3 of [02-choreography-space-camera.md](02-choreography-space-camera.md) quotes 10-30% / 100-150% — a narrower cut of the same idea; pick one range per project and stay inside it.)
- Counter 1-2 s ease-out; progress fill 600-1200 ms.
- Camera push 2-6 s ease-in-out; handheld noise under 1-2% of frame.
- Transitions 8-20 frames; hero transitions 20-40 frames.
- Shot length 1-3 s social, 3-6 s explainer, 6-10 s premium.
- Text read time: the formula is in [motion-design.md](../motion-design.md). End card 2-3 s.
- Camera shake 3-8 frames decaying; glitch bursts 3-8 frames.
- Frames per beat = fps × 60 ÷ BPM - but read real onsets with `strata beats`, never by formula.
