# Kinetic type and transitions

Type that carries motion of its own, and what happens between two shots.

Part of the craft manifesto — the index, and every other part, is in [craft.md](../craft.md).

### Kinetic typography
- **Per-character / per-word animators** make text feel alive — words rise and fade in, letters track in, cascades. That is for **headlines and kinetic type**; **captions** are the opposite case and appear in readable chunks, because reading speed on a phone wins ([editing-director.md](../direct/editing-director.md)) — decide which a text layer is before animating it. Use `animators` with `ranges` (`based_on: words|characters|lines`, `shape: ramp_up|…`), in **percentage** units so any string length cascades. (Syntax in `format.md` Text; copy from `recipes.md`.)
- **Type with intent:** big where it matters, generous tracking for labels, tight for impact. Animate the meaning (a number counts up; a key word punches in).
- **Textured type** — a hero word filled through a track matte (gradient, scrolling stripes, gold-shine sweep, footage in letterforms). **Textures are generated, not sourced** — `strata generate image` as a full-frame no-subject texture, or a script for geometric ones. One hero word per shot, never body text. [recipes.md](../recipes.md) §1 "Textured text".

### Transitions between shots
- **Match-cut / continuity:** carry a shape, colour or motion vector across the cut. Whip-pan, light-leak, iris/clock wipe, scale-through — with intent, never as decoration ([motion/04-transitions.md](motion/04-transitions.md)).
- **A flash frame goes on the two frames *before* the cut, not on it.** Placed on the cut it covers the incoming title's entrance and the title reads as already there (*measured* on a beat-cut spot); placed just before, the cut lands on the flash's decay and the entrance is seen.
- Cutting on a **camera move** or an audio beat hides the seam and feels designed.
- **A move comes from what caused it, and leaves the way it came.** A panel that arrives because a word landed enters from that word; a card that exits to make room for the next slides toward where the next one comes from; a reveal grows out of the thing that triggered it rather than fading up in place. The viewer reads cause from direction, so an entrance with no origin reads as an element being placed rather than an element arriving — and it costs nothing to point it at something.
