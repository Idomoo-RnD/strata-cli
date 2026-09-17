# Cartoon / animated character — route

**This is the route when** the brief says cartoon, animated, animation, 2D animation, anime, mascot, animated character, an illustrated story, a talking animal or object. Against [generated-clips.md](generated-clips.md): every visual here is generated too, but the cast is *drawn* — a style that must hold across every shot. Against [presenter.md](presenter.md): a cartoon character speaks through a referenced take, not an avatar of a real face.

**It expects** a cast, a world and a style locked before the first clip. **It delivers** generated character clips — sheets first, then Seedance against the sheets — composited over graphics as alpha where a character sits on a layout, with the engine doing the type, the layout and the motion of everything that is *not* a body. **A character is never built from solids**: shapes are for geometry, not anatomy ([what-to-generate.md](../shoot/what-to-generate.md), *Drawn or generated?*).

## Settle these before the storyboard (into BRIEF.md; unattended → decide and record in decisions.md)
- The cast: every principal, its one-line description, and whether a sheet is supplied or generated.
- The style, in words the prompts will repeat verbatim: line, fill, palette, shading, the era or school it belongs to ([motion/08-styles-index.md](../craft/motion/08-styles-index.md)).
- Who speaks, and how: dialogue as `--ref-audio` on the character's clips, or narration over the picture.
- Which shots are full-frame clips and which are a character over a scene layout (the `.jet` route).

## Where it usually sits on the range
Cartoon pacing is set by the story, not the medium: a gag cuts fast, a fable holds. Generated cartoon clips arrive slow and even, so the range comes from the edit — more shots, shorter slots — and the model's own idle motion is not a beat. Label energy and stillness as uncalibrated until a reference exists.

## Read, in this order
- [production-bible.md](../make/production-bible.md): *2. Make one sheet per principal*, *3. One canonical identity block*, *5. Consistency across the two clip families* — the sheets are `generate image`, the style is locked in every identity block.
- [video-generation.md](../video-generation.md): the one hard rule, the prompt, chaining; [video-prompts.md](../shoot/video-prompts.md) for the prompt nearest each shot.
- [ref-image.md](../shoot/ref-image.md): *carry a character across clips* — the sheet is the reference on every clip.
- [clips-for-alpha.md](../shoot/clips-for-alpha.md): a character that sits over a layout is one continuous shot on a keyable ground, then a `.jet` ([alpha.md](../shoot/alpha.md)).
- [ref-audio.md](../shoot/ref-audio.md): a line spoken by a character is generated into the clip.
- [video-styles.md](../craft/video-styles.md): **character-animation** construction.

## Watch for
- a character drawn from `solid` layers and masks — a diagram, not a cartoon: [what-to-generate.md](../shoot/what-to-generate.md), *Drawn or generated?*
- the style drifting between clips because the identity block was paraphrased: [production-bible.md](../make/production-bible.md), *3. One canonical identity block*
- a talking character plus a narration layer with the same line: [traps.md](../traps.md), *The traps, in full*
- a cartoon clip prompted with cuts and camera moves when it must key as an overlay: [clips-for-alpha.md](../shoot/clips-for-alpha.md)
- a `matte` on a stylised character: it works on cartoon and stylised figures ([alpha.md](../shoot/alpha.md)), but for a flat style generate on a green ground and key it — the surer edge

## Done when
- every principal has a sheet and an identity block, and every clip cites the sheet
- `strata review` reports no freeze inside any character clip; the style reads as one hand across all shots
- characters over layouts are `.jet` overlays with clean edges at the delivery size
- no body, face or creature in the piece is a `solid`
