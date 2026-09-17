# Moodboard — one generated board that shows the whole campaign

A single 16:9 image, made with `strata generate image`, that puts three finished keyframes and a study strip in front of the user before any scene exists.

Part of the asset-generation reference — the index, and every other part, is in [assets.md](../assets.md). The prompt flags are in [images.md](images.md); the board's place in the workflow is the director's styleframe step ([director.md](../direct/director.md), *4. Key styleframes*) and the plan's style step ([plan.md](../make/plan.md), *2½. Style*).

## What it is for

A moodboard is a direction artifact. It answers, in one picture, the questions a brief leaves open:
what the campaign looks like, how its type behaves, what the photography is, which four colours it
owns, and whether three different compositions still read as one campaign. The user compares a
campaign, not a frame. Once approved, the board is the **campaign concept and visual style** in
BRIEF.md and a reference for every later generation (`--reference` on stills, `--ref-image` on
clips). It is never a layer in the delivered piece.

## The prompt — in this order

The order is the model's reading order; each part narrows the next.

| Part | What it says | One line of the worked prompt |
|---|---|---|
| **Board format** | the aspect, and the parts of the board by name | *a polished 16:9 moodboard featuring three finished 9:16 portrait keyframes and a compact typography, palette, and texture study strip* |
| **Concept** | the campaign line, in quotes | *Build the concept around “Make space for you,”* |
| **Type system** | the display face's character, the detail face's | *oversized expressive serif typography, clean sans-serif details* |
| **Photography** | the treatment, named | *natural studio photography* |
| **Palette** | the colours by name, four or five, in the order of dominance | *butter yellow, deep plum, dusty clay, and pale lilac* |
| **Each frame** | one sentence per frame: its copy verbatim in quotes, its picture, its one compositional idea | *Frame two uses an overhead photograph of the same woman on a yoga mat, with asymmetrical typography reading “Find your own rhythm.”* |
| **Coherence** | distinct compositions, one campaign | *Give each frame a distinct composition within one coherent campaign.* |
| **Board rules** | flat, spacious, aligned; anatomy; legibility | *Keep the board flat, spacious, and carefully aligned, with believable anatomy, readable copy* |
| **Exclusions** | what the model must not add | *no invented branding or generic wellness symbols* |

Two rules the first board taught, so they are now part of the format:

- **A phrase that is one line says so.** *"Find your own rhythm."* came back set as *"FIND YOUR OWN. / RHYTHM."*, a period landing mid-phrase, because the prompt did not say the line is unbroken. Write *the single line “Find your own rhythm.”* when the break matters.
- **Typefaces are unnamed unless the brand names them.** The study strip invented two typeface names. Say *unnamed typefaces* in the strip, or name the brand's real faces; a made-up name in a board becomes a spec someone tries to buy.

## The worked prompt

Passed verbatim to `strata generate image … --aspect 16:9`; the result is the board described above.

> Create a polished 16:9 yoga ad moodboard featuring three finished 9:16 portrait keyframes and a compact typography, palette, and texture study strip. Build the concept around “Make space for you,” using oversized expressive serif typography, clean sans-serif details, natural studio photography, and butter yellow, deep plum, dusty clay, and pale lilac. Frame one combines “Make space for you.” with a woman stretching, her body overlapping selected letterforms while keeping the words readable. Frame two uses an overhead photograph of the same woman on a yoga mat, with asymmetrical typography reading “Find your own rhythm.” Frame three uses a plum background, the headline “Come as you are.”, an oval photograph of a welcoming yoga group, and “Join our yoga class” with “All levels welcome”. Give each frame a distinct composition within one coherent campaign. Keep the board flat, spacious, and carefully aligned, with believable anatomy, readable copy, and no invented branding or generic wellness symbols.

```bash
strata generate image "<the prompt>" --aspect 16:9 -o moodboard_v1.png
strata generate image "<the prompt>" --aspect 16:9 --colors "#F6D96B,#4A1F3D,#B9705A,#E6D3EA" -o moodboard_v2.png   # a brand's palette, biased
```

## With a brand, and without one

- **A brand exists** ([brand.md](../brand/brand.md)): the palette, the type faces and the photographic treatment come from its atoms, by name, and the prompt says so — the board proves the campaign inside the brand, it does not audition a new one.
- **No brand exists**: the board *is* the design of the campaign identity. The palette is derived from the subject's own world, never a default surface ([anti-slop.md](../craft/anti-slop.md), *Colour when no brand exists*); the type pairing and treatment are chosen for this subject; and once approved they are written to `.brand/brand.md` marked *designed for this campaign, not supplied*, so every later piece uses the same identity.

## After the board

- The user picks a frame or the board; the choice is recorded in BRIEF.md under *Campaign* and in `decisions.md` when unattended.
- Each keyframe becomes a real styleframe in the engine — `strata preview --grid` for the boxes, `strata snapshot` for the type and plates — and the board's frame is cited as `--reference` when a plate or a clip is generated from it.
- The board's copy is the approved copy only if the user says so; a moodboard line is a proposal.

## Checklist

- [ ] Board format, concept, type, photography, palette, one sentence per frame, coherence, rules, exclusions — in that order
- [ ] Every line of copy in quotes, verbatim; a one-line phrase declared as one line
- [ ] Typefaces unnamed, or the brand's real ones
- [ ] Brand atoms by name when a brand exists; a designed identity written to `.brand/brand.md` and marked as such when none does
- [ ] Approved board recorded in BRIEF.md *Campaign*; the frames rebuilt as styleframes, not shipped as the board
