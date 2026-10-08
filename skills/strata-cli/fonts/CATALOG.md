# IDF font pack 1 — 30 animated fonts

Use one on a text layer: `"font": "idf:<name>"` (see [Animated fonts](../references/scenes.md#animated-fonts-idf)). Every font has the same characters: A–Z, a–z, 0–9 and `. , ! ? ' " & - @ : ; ( ) / # $ % + = *`; colour slots are set with `"idf": {"colors": {…}}`, timing with `"idf": {"timing": {…}}`. `strata idf preview <name> --text "…"` draws a strip locally. IDF text is fixed at compile time: never use it for a personalised field.

## Fun

| Font | What it does | Colour slots (defaults) | Timing (intro/hold/exit/stagger s) |
|---|---|---|---|
| `idf:fun-balloon` | Foil balloon letters inflate with a rubbery wobble, grow a knot and a curly string, bob while they hold, and swell and POP into shreds on exit. | main #ff2e7e, deep #b0104f, light #ffffff, rim #ffb3cf, string #4a4a5a, shadow #1a0a12 | 1 / 2.5 / 0.75 / 0.06 |
| `idf:fun-candystripe` | Hard-candy letters draw their red outline, swell in cream, slide in candy-cane stripes that keep scrolling while they hold, and spin away on exit. | edge #c8102e, cream #fff7ec, stripe #ff2847, stripe2 #22c48a, light #ffffff, shade #c8102e, shadow #5a0010 | 1 / 2.5 / 0.75 / 0.06 |
| `idf:fun-chrome` | 80s chrome letters with sky-and-ground colour bands flip in edge-on with a shine streak, twinkle with sparkles, and flip away on exit. | line #140f33, rim #bfefff, sky1 #1d3fd6, sky2 #6fa6ff, sky3 #dff1ff, horizon #ffffff, ground1 #4b1f7a, ground2 #ff5fc8, ground3 #ffd25e, shine #ffffff, shadow #0b0820 | 0.9 / 2.5 / 0.6 / 0.06 |
| `idf:fun-clay` | Orange plasticine is piped along each letter's strokes like toothpaste, settles with a squish into a glossy clay letter, and squashes flat on exit. | main #ff8a3d, edge #7a2e08, deep #d4581a, light #ffd1ad | 1.2 / 2.5 / 0.7 / 0.07 |
| `idf:fun-jellybean` | Glossy translucent jelly letters drop in, land with a big squash and jiggle, wobble while they hold, and splat flat with droplets on exit. | main #ff2e63, glow #ff9bb7, deep #a8002c, light #ffffff, shadow #3a0014 | 1 / 2.5 / 0.8 / 0.06 |
| `idf:fun-pixelrez` | 8-bit pixels zip in and snap into pixel art, flash white and sharpen into the smooth letter with a hard retro shadow, then break back into scattering pixels on exit. | main #7c4dff, accent #00e5ff, hi #ffffff, lo #1a0b4a, flash #ffffff, shadow #1a0b4a | 1.5 / 2.5 / 0.9 / 0.05 |
| `idf:fun-sticker` | Die-cut stickers with a white border slap down from a tilt with a squash, curl a corner while they hold, and peel off and fly away on exit. | main #ff4d2e, deep #c42d14, border #ffffff, cut #d6cfc3, back #ece7de, shine #ffffff, shadow #000000 | 0.9 / 2.5 / 0.8 / 0.07 |
| `idf:fun-throwup` | Pink graffiti bubble letters inflate from a seed dot on a rubber spring with loose outlines trailing behind, grow slow paint drips while they hold, and pop into a burst of dashes on exit. | main #ff6fb5, line #33105a, side #c63f93, shade #ff9bcd, light #ffe3f1, shadow #22073f | 0.9 / 2.5 / 0.6 / 0.05 |
| `idf:fun-toonpop` | Comic-book letters with ink outlines and halftone shading pop in on a spring behind a yellow POW starburst and speed lines, and vanish in a puff of cartoon smoke. | main #ff3b30, ink #121212, dots #9e1209, light #ffffff, burst #ffd400, smoke #ffffff | 0.9 / 2.5 / 0.6 / 0.07 |

## Corporate

| Font | What it does | Colour slots (defaults) | Timing (intro/hold/exit/stagger s) |
|---|---|---|---|
| `idf:corporate-anatomyduo` | Each stem, bowl and arm draws as a hairline, then grows to full weight with a blue and a cyan copy leading the navy, and thins back to hairlines on exit. | main #14213d, accent #2f7bff, accent2 #2fd6ff, shadow #0b1530 | 1.2 / 2.5 / 0.8 / 0.05 |
| `idf:corporate-blueprintpro` | Construction lines and circles taken from the letter's own shape draw in orange, the outline is traced, then the fill grows to full weight and the drafting lines retract. | main #14213d, accent #ff5a36, shadow #0b1530 | 1.6 / 2.5 / 1 / 0.05 |
| `idf:corporate-compressh` | A vertical bar stretches sideways into the letter, its counters opening from slits with a blue flash, and squeezes back into the bar on exit, so text loops seamlessly. | main #ffffff, accent #2f7bff, shadow #000000 | 1 / 2.5 / 0.8 / 0.05 |
| `idf:corporate-hairline` | A fine blue outline traces each letter, the weight fills inward from the edge, a slow light sweep crosses it while it holds, and it drains back to the outline on exit. | main #14213d, accent #2f7bff, shine #9cc3ff, shadow #0b1530 | 1.1 / 2.5 / 0.8 / 0.05 |
| `idf:corporate-morphdot` | A blue dot pops in with a ripple and morphs point by point into the exact letter as navy covers the blue, then morphs back into the dot and pops on exit. | main #14213d, accent #2f7bff, shadow #0b1530 | 1.3 / 2.5 / 0.8 / 0.05 |
| `idf:corporate-ribbon` | A blue folded ribbon sweeps through each letter in writing order, the letter grows out of it as the ribbon peels away, and the ribbon sweeps back through on exit. | main #14213d, accent #2f7bff, fold #8cc0ff, shadow #0b1530 | 1.3 / 2.5 / 0.9 / 0.06 |
| `idf:corporate-shutter` | A thin bar drops in and the letter unrolls downward from it like a roller shutter with blue leading edges, then rolls back up on exit. | main #14213d, accent #2f7bff, accent2 #7fd1ff, shadow #0b1530 | 1.1 / 2.5 / 0.8 / 0.05 |
| `idf:corporate-stencil` | Stencil parts with clean gaps slide in along their own direction with a blue lead, breathe slightly while they hold, and slide back out on exit. | main #14213d, accent #2f7bff, shadow #0b1530 | 1.1 / 2.5 / 0.8 / 0.05 |

## Unique

| Font | What it does | Colour slots (defaults) | Timing (intro/hold/exit/stagger s) |
|---|---|---|---|
| `idf:unique-bauhaus` | Each letter is split into parts in a five-colour Bauhaus palette that spin, grow or slide in on overlapping timing, then wind up and fly apart on exit. | c1 #e63946, c2 #f6bd16, c3 #1d4e89, c4 #111111, c5 #ef7b2d | 1.3 / 2.5 / 0.8 / 0.06 |
| `idf:unique-brushburst` | Pink comets with orange cores converge into a spiky burst and the brush letter slams in with splash drops, drips while it holds, and scatters back into comets on exit. | main #ff5fd2, core #ff7a1a, light #fff3fb | 0.9 / 2.5 / 0.7 / 0.07 |
| `idf:unique-constellation` | Glowing nodes pop in along each letter and connect with lines before the blue body fills in behind the network, which shimmers while it holds and fades out on exit. | node #ffffff, line #cfe0ff, fill #2f4fd8, glow #7fa8ff | 1.2 / 2.5 / 0.8 / 0.05 |
| `idf:unique-inlineloop` | Three concentric inline strokes in four colours spin in and close into rings, keep travelling around the letter in a seamless loop, and unspool on exit. | c1 #ff5e5b, c2 #ffd23f, c3 #3bceac, c4 #4361ee | 1.3 / 2.5 / 0.8 / 0.05 |
| `idf:unique-liquid` | Droplets fly in, squash on landing and fuse into a two-tone liquid letter that breathes while it holds, then bursts back into orbiting drops on exit. | face #fff1dc, side #1c2266 | 1.1 / 2.5 / 0.9 / 0.06 |
| `idf:unique-neontube` | A neon glass tube slides together from dash segments, strikes on with a flicker, glow and hot core, buzzes while it holds, and shorts out on exit. | main #ff2e63, core #fff0f4, glass #5a3346, spark #ffffff | 1.6 / 2.5 / 1 / 0.05 |
| `idf:unique-scriptink` | Script is written on behind a glowing pen tip with a pink inline and a salmon 3D shadow following, floats like a ribbon while it holds, and lifts and fades on exit. | main #ffffff, inline #ff4f79, side #ff9aa8, nib #ffffff, glow #ffd2dc | 1.4 / 2.5 / 0.8 / 0.1 |

## Extreme

| Font | What it does | Colour slots (defaults) | Timing (intro/hold/exit/stagger s) |
|---|---|---|---|
| `idf:extreme-electric` | Glowing lightning bolts trace each letter with branches and sparks until it charges up with an electric aura, crackle with arcs while they hold, and discharge into falling sparks. | main #e8f6ff, bolt #ffffff, glow #3fd0ff, aura #2a7bff, spark #bff3ff | 0.8 / 2.5 / 0.8 / 0.05 |
| `idf:extreme-glitchrgb` | Slices jump and cyan and magenta copies snap together amid blinking blocks, a scan band sweeps with glitch bursts while they hold, and the letters tear and collapse like an old TV switching off. | main #ffffff, c1 #00f0ff, c2 #ff2bd6, block #f5ff3d | 0.8 / 2.5 / 0.8 / 0.04 |
| `idf:extreme-impactcomic` | A jagged starburst explodes with speed lines as the letter slams in with a squash, shake and halftone shading, and the letter zooms away with speed lines on exit. | main #ffe600, ink #111111, shadow #1d4ed8, dots #ff8a00, light #ffffff, burst #ff2e63 | 0.75 / 2.5 / 0.7 / 0.06 |
| `idf:extreme-infernopro` | Letters ignite from the bottom behind a white-hot edge with three-tone flames swaying on top, flicker with rising sparks while they hold, and char and burn down into embers on exit. | main #ffae00, hot #fff3b8, flame #ff6a00, ember #d62411, deep #a3200d, char #24100b | 0.9 / 2.5 / 1.2 / 0.05 |
| `idf:extreme-shatterpro` | Bevelled steel letters slam in from twice their size with red trailing copies, a white flash, shake and debris, tremble while they hold, and crack into spinning shards on exit. | main #e9edf2, light #ffffff, dark #7d8796, edge #2a2f38, impact #ff2d2d, flash #ffffff, shadow #000000 | 0.7 / 2.5 / 1.1 / 0.08 |
| `idf:extreme-warp` | Letters rocket in from depth trailing cyan and violet afterimages and light streaks, flash on arrival, get scanned by a light while they hold, and warp away into a streak. | main #ffffff, c1 #00e5ff, c2 #b14cff, streak #9be8ff, flash #ffffff | 0.75 / 2.5 / 0.7 / 0.05 |

Base typefaces: Google Fonts, SIL Open Font License 1.1 except Luckiest Guy and Permanent Marker (Apache License 2.0); each family's licence is in `licenses/` beside the fonts.
