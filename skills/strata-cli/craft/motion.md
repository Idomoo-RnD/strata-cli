# How much moves — and how it moves

Part of [craft.md](../craft.md). [Design contract](../make/design-contract.md) owns intent,
hold classification and diagnostic confidence; these are techniques for implementing the choice.

### How much moves — set by the declared energy

Energy in the brief describes a creative posture; the tool's energy is a pixel-change diagnostic.
Do not equate a number with a required count of moving objects. One dominant move is a useful
starting point for focal control; complex choreography is valid when attention remains deliberate.
Name the hierarchy and the role of simultaneous or staggered actions.

**Images may be still, layer-animated or footage.** Choose photographed motion when the subject
must walk, speak, turn or materially change. A collage, illustration, diagram or deliberate locked
photo may use image layers. A personalized still slot stays replaceable as an image; layer motion
can reveal it without generating a new clip per viewer. Follow the approved treatment, not a
universal ban on stills.

**A hold can be locked or live.** For a locked hold, composition and reading time carry it.
For a live hold, name the subject/camera/type/material action that carries it. Do not add dust,
grain or background motion only to manipulate a detector. An exhausted clip is not a locked hold.

### Sizing a deliberate move

Translation speed in pixels per frame is `distance / (duration × fps)`. A uniform scale change
of fraction `p` gives approximate radial travel `r × p`, where `r` is distance from the scale
center; divide by duration × fps for average speed. Name the pixel feature whose movement matters:
the exact center stays fixed during a scale, but edges of a centered word still move.

A prior 2.5 % scale change over 2.75 s at 720p measured 0.24 px/frame near the frame edge and
0.16 px/frame on the sampled wordmark feature. That explains why the move read subtly there;
it is not a universal minimum-speed rule. Choose amplitude from the desired perception, then
inspect a cropped consecutive-frame strip at the actual fps and viewing size.

Soft gradients need contrast as well as travel. Approximate grey-level change per frame is
`luminance_delta × velocity / ramp_width`. The delta is the composited change against the background,
not the layer opacity. A prior screen-blended pool at opacity 0.26 over pale plaster changed luma
by 8.2 grey levels, illustrating why opacity alone is misleading. The validator's low-change
warning is a prompt to inspect intent and visibility, not a command to brighten a correct brand.

For a sheen meant to light a word, constrain it to the word; a full-frame glow is a grade change.
Evaluate motion after the chosen grade since blur/contrast affect its appearance and the metric.
Preserve low contrast if it is intentional and legible; never damage the grade to hit an energy target.

### A clip has a liveliest window

`strata deconstruct` can identify useful intervals before choosing `offset_frame`. A previous
clip varied from energy 0.5 to 2.5 within five seconds. Use the window that serves the beat, not
necessarily the busiest one: a reaction, pause or precise product angle can matter more than
activity. Check available duration after trimming; see [layers.md](../engine/layers.md).

### Read time and rhythm

The read-time heuristic in [motion-design.md](motion-design.md) is a starting point. Prove important
copy at actual viewing size, including captions, legal text and changing data. A short cut may
carry only a glimpse; a long message needs time. Adjust copy or duration consciously rather than
letting a timing default determine meaning. Validator warnings are evidence to inspect, not a
substitute for reading the output.

### Motion principles (the fundamentals)

Timing is when; spacing is the distribution of values through a move. Anticipation, overlap,
follow-through, staging and material logic help a move communicate. Read
[foundations](motion/01-foundations.md) and [motion-design](motion-design.md) for options.
Linear motion suits constant-speed travel or mechanical motion; hard cuts and hold keys are
valid deliberate discontinuities. Curves express intent, not a universal “never linear” rule.
