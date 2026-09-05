# Figma — route

**This is the route when** the brief names Figma: a file, a frame, a link, "the design". Against [brand.md](brand.md): a Figma file can also be the brand *source* (variables and tokens); then both routes apply and the brand document is built from it first.

**It expects** access to the file or its exported node data, the fonts as files, and the frames to import. **It delivers** a scene whose key frames match the design on the strata canvas, with motion authored on top.

## Settle these before the storyboard (into BRIEF.md; unattended → decide and record in decisions.md)
- The canvas: same aspect (one uniform scale) or different aspect (re-compose on the grid).
- Which frames are imported; which groups become comps.
- Fonts mapped to files on disk, and whether they carry the copy.
- What moves, ordered by hierarchy; whether the design's tokens become `.brand/brand.md`.

## Where it usually sits on the range
A faithful import is a slideshow; the range comes from the kind the piece is, not from the file. Product-UI pieces lean toward the *film, quiet* row so the interface can be read. The brief decides; declare the four numbers from a measurement (review.md §7).

## Read, in this order
- [figma.md](../figma.md): the twelve steps in order, then the *Import checklist*; *11. Verify against the source* is the accuracy loop.
- [workflow.md](../workflow.md): check D, an imported frame is a layout, not a video.

## Watch for
- the frame origin not subtracted, everything offset: [figma.md](../figma.md), *2. Convert coordinates*
- raw Figma names kept on layers: [figma.md](../figma.md), *8. Names — this one breaks renders*
- an import shipped without motion: [figma.md](../figma.md), *12. A faithful import is still a slideshow — add motion*

## Done when
- `strata preview --grid` at each imported frame matches the Figma screenshot side by side
- `strata validate` clean; `strata glyphs` passing on every text layer; every layer renamed meaningfully
- `strata review` shows motion in every shot; nothing is a held still of the design
