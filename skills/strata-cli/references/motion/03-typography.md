# Typography in motion (kinetic type)

> Executed with **per-character/word text animators** (`based_on`, `shape`, `character_offset`) and
> real box/align rules - [format.md](../format.md) and [motion-design.md](../motion-design.md).
> ⚠ Vertical text anchors to the **bottom** of its box and digits wrap mod 10 in a count-up - both
> measured facts in format.md that no craft here overrides.

## Contents

- [1. Entrance and reveal patterns](#1-entrance-and-reveal-patterns)
- [2. Exit patterns](#2-exit-patterns)
- [3. Craft rules for kinetic typography](#3-craft-rules-for-kinetic-typography)
- [4. Numbers in type](#4-numbers-in-type)
- [5. Multilingual and RTL](#5-multilingual-and-rtl)


## 1. Entrance and reveal patterns
- **Fade**: the neutral default; pair with a 10-20 px rise for life.
- **Rise (slide up)**: text moves up 20-40 px with ease-out while fading in; the standard modern entrance.
- **Line mask wipe**: text appears from behind an invisible line; elegant, editorial, fashion.
- **Per-character stagger**: characters 15-40 ms apart; lively; keep the total under 600 ms.
- **Per-word stagger**: words in reading order 60-120 ms apart; the most legible stagger.
- **Per-line stagger**: lines 100-200 ms apart; calm, corporate.
- **Typewriter**: characters appear one by one, optionally with a cursor; tech, code, terminal.
- **Decode (scramble)**: random glyphs resolve into the final text; hacker, sci-fi, data.
- **Scale pop**: from 0.8 or 1.2 to 1 with strong ease-out; punchy headlines.
- **Blur-in**: heavy blur and low opacity to crisp; dreamy, premium, slow.
- **Tracking reveal**: letter-spacing tightens from wide (cinematic) or opens from tight (release).
- **Weight or width morph**: variable font axes animate (thin to bold); attention without movement.
- **3D flip in**: each character rotates on X or Y into place; stepped, it becomes a split-flap.
- **Split-flap board**: stepped glyph cycling; airports, retro, counters.
- **Drop**: letters fall with gravity and a small bounce; playful.
- **Baseline bounce**: characters bob on a wave; loops, music, kids.
- **Handwriting (stroke draw)**: path trim reveals script as if written.
- **Outline to fill**: stroke appears, then fills; luxury and tech.
- **Highlighter sweep**: a color bar wipes behind a word.
- **Underline or strike draw**: a line draws under or through a word on the beat.
- **Color sweep (shimmer)**: a light band travels across the text.
- **Glitch in**: RGB split and block offsets for 3-6 frames, then settle.
- **Cut-in (no animation)**: text simply appears on a beat; confident, brutalist, rhythmic; the strongest option when timed well.
- **Word swap**: a fixed phrase with one slot where words cycle (slide or flip).
- **Stack reveal**: lines slide in from alternating directions.
- **Ticker (marquee)**: continuous horizontal scroll, linear; news, sports, broadcast.
- **Kerning and leading animation**: spacing itself is the motion; tightening = focus, loosening = release.
- **Text on a path**: letters follow a curve; rotating rings and badges.
- **Text as mask**: footage or gradient visible only inside the letters.
- **Text as container**: a scene lives inside the type; zoom through a letter.
- **Oversized crop**: enormous type cut by the frame, panned slowly.
- **Physics text**: letters collide, stack, and settle; tactile.
- **Liquid type**: letters blob and merge; organic brands.
- **Extruded (3D) type**: depth with lighting; product and title sequences.
- **Letter collision**: words slam together with a shake and a hit sound.
- **Reveal from behind object**: text slides out from behind a shape or product.
- **Counter and odometer**: see section 4.

## 2. Exit patterns
- **Mirror of entrance**: reverse the entrance, about 40% faster.
- **Continue the vector**: exit in the direction the text was heading; implies a journey.
- **Blur out or fade out**: the softest exit.
- **Mask out**: the line mask closes; clean, editorial.
- **Cut out**: hard disappear on a beat; rhythmic.
- **Scale through camera**: text scales past the viewer; transition energy.
- **Collapse**: tracking tightens to zero and fades; quick and elegant.
- **Drop out**: falls off the bottom with gravity; playful.
- **Replace in place**: old text exits as new text enters in the same slot (word swap logic).

## 3. Craft rules for kinetic typography
- **One idea per beat**: one phrase on screen at a time.
- **Read time floor**: ~0.5 s + ~0.3 s/word ([motion-design.md](../motion-design.md) has the canonical formula); at minimum twice through.
- **Move or read, not both**: text is read when still; motion bridges still states.
- **Emphasis tools**: size, weight, color, and an extra beat of hold; never all at once.
- **Hierarchy in time**: headline first, support second, detail third.
- **Stagger in reading direction**: LTR or RTL; against the direction only for deliberate disruption.
- **Entrance slower, exit faster**: things leave faster than they arrive. The exact ratio to use is in [motion-design.md](../motion-design.md) (measured for this engine; it is gentler than the 1.5-2x general figure).
- **No overshoot on body text**: reserve it for single display words.
- **Don't animate everything**: pick the hero word; the rest enters as a block.
- **VO and syllable sync**: text lands on the spoken word 1-3 frames early, never late.
- **Scale reads as volume**: a bigger word is a louder word; mirror speech dynamics.
- **Lines per frame**: two or three at most; paragraphs don't belong in motion.
- **Orphan control**: no lone trailing words; edit copy to fit the frame.
- **Motion blur on text**: fine during movement, a bug at rest.
- **Punctuation as motion cue**: commas = short hold, periods = longer hold, exclamation = scale pop.
- **Consistency**: one entrance, one exit, one emphasis per project; vary only for story beats.
- **Typeface physics**: geometric sans wants snappy moves; serif wants slow elegance; display wants bold cuts.
- **Optical size**: small text needs more tracking and weight; when scaling up, tighten tracking.
- **Contrast floor**: text over footage needs a scrim, blur, or shadow.
- **Sub-pixel shimmer**: no slow drifts on small text; snap to pixels or keep it still.
- **Caps vs mixed case**: all caps reads as shout and works for short bold words; mixed case for anything longer.
- **Text box discipline**: dynamic text gets a fixed box with auto-fit; test longest and shortest strings.

## 4. Numbers in type
- **Tabular lining figures**: digits keep width so counters don't jitter.
- **Count-up easing**: ease-out, 1-2 s, decelerating last digits; round only at the end.
- **Odometer roll**: each digit column rolls independently; satisfying and clear.
- **Slot machine**: fast spin then settle; playful reveals.
- **Currency and units**: symbols stay still while digits animate; units enter after the number.
- **Threshold styling**: color or weight changes when a value crosses a goal.
- **Big number hero**: one number at hero scale, everything else small; the personalization payoff frame.

## 5. Multilingual and RTL

> ⚠ **Engine facts first:** Hebrew and Arabic render correctly in VASCO with **no special setting**, and
> `rtl: true` is a **measured no-op** - if RTL copy looks wrong the cause is almost always missing
> glyphs in the font, not direction. Check with `strata glyphs` before changing anything.
> [format.md](../format.md) is the authority; everything below is design craft on top of it.

- **RTL stagger**: Hebrew and Arabic stagger from the right; mirror slide directions and mask wipes.
- **Mixed-direction lines**: animate by word, not by character, to avoid bidi reordering artifacts.
- **Text expansion**: design for 30% growth (German, French) and 20% shrink (Chinese); test the longest language.
- **Script metrics**: Hebrew and Arabic carry different vertical rhythm; align on a shared baseline grid.
- **Font fallback matching**: weight and width must match across scripts or the stagger looks broken.
- **Numerals**: decide Western vs Eastern Arabic digits per market.
- **Punctuation mirroring**: brackets and arrows flip in RTL; check after mirroring layouts.
