# Production bible — the same actor, product and place in every clip

A piece with more than one generated clip needs its cast, props and locations **decided
once and reused verbatim**, or each clip drifts its own way: the groom gets a different jaw,
the product loses its logo, the room changes colour. This file is the procedure. The
per-clip mechanics it relies on are measured elsewhere: the identity lock and prompt anatomy
([video-generation.md](video-generation.md)), reference images and their JOB lines
([video-generation-advanced.md](video-generation-advanced.md)), the talking-person recipe
(same file), the series skeleton (same file).

**When it applies:** any job with ≥2 clips sharing a person, product or set — a commercial,
a series, a personalised template with a recurring presenter — and always when the user
asks for consistency or supplies reference sheets ([intake.md](intake.md)). A one-clip job
does not need a bible; it needs a good identity lock.

---

## 1. Inventory the principals

From the brief, script or storyboard, list every recurring visual element and rank it:

| Class | Examples | Continuity risk |
|---|---|---|
| **Principals** — people who appear in ≥2 shots | groom, preacher, agent | face, hair, wardrobe drift between calls |
| **Hero product / prop** | the veiled membership card, the watch, the bottle | the model treats it as a prop — *measured: small props vanish between shots* — so a hero object must be promoted to a principal with its own sheet |
| **Locations** — sets seen in ≥2 shots | the altar, the office | palette and dressing shift; a plate pins them |
| **Wardrobe / marks that carry meaning** | the tux, the branded polo, the logo on the card | wardrobe survives references well; logos need `--realistic-human` and a clean sheet |

The brief's own continuity ranking wins — if it names a *highest-priority element*, that
element gets the most protection below. Otherwise the ranking is: hero product → principals
→ locations.

## 2. Make one sheet per principal — or use the supplied ones

- **Supplied sheets are used as-is, never regenerated.** Regenerating "a cleaner version"
  replaces the client's actor with mine.
- **A character sheet** is the model-sheet form from
  [video-generation-advanced.md](video-generation-advanced.md): front, three-quarter, side,
  back on one flat grey ground, plus expression heads — every angle the clips will need,
  *not* one pose. `strata generate image` with a full physical description; keep the
  printed `url:`.
- **A product sheet** is the object alone, clean, flat-lit, on a neutral ground, with the
  logo legible and every distinguishing feature visible. For a *styled* hero (the veiled
  card on its easel), make the styled version the sheet — that styling *is* the continuity.
- **A location plate** has **no people in it** (say so in the prompt) — a stray figure
  fights the principal you insert.
- **Keep every sheet on disk beside the scene**, named for its role (`cast_groom.png`,
  `prop_card_bride.png`, `set_altar.png`), and record the URLs in one `bible.json` so every
  clip cites the same files. `--seed` fixed for the whole production.

## 3. Write one canonical identity block per principal — and never rewrite it

The block is the physical description the identity lock demands (detail, never adjectives),
written **once** and pasted **byte-identical** into every prompt that shows that principal,
in all three positions (character block, inside the close-up, Static Description). Ten
prompts written from memory drift ten ways; one block pasted ten times does not.

```text
GROOM — [Image 1] is the definitive reference for the groom in every shot: mid-thirties,
dark swept-back hair, close-trimmed dark stubble, strong brow, pale skin, a black tuxedo
with a black bow tie over a white wing-collar shirt and a single white rose boutonnière;
match [Image 1] exactly for face, proportions and wardrobe in every single shot.
```

Keep the blocks in `bible.md` next to `bible.json`. A prompt for a scene is assembled:
**Style & Mood → the blocks for whoever is on screen (citing their `[Image N]` in flag order)
→ location plate block → the scene's shots → Static Description restating every block's
invariants → Audio.** The `[Image N]` numbers follow the `--ref-image` order on the command
line, so **attach references in the same order in every call** (groom always `[Image 1]`,
card always `[Image 2]`, …) and the blocks never need renumbering.

## 4. Protect the hero element in every shot it appears in

The measured failure is *prop drift*: a red backpack on the sheet and named in the prompt
still vanished in the last shot. So the priority element gets, in every shot it is in:

- its own sheet cited as a reference (not folded into a character sheet);
- **its own sentence in the shot description** — *"the veiled membership card on its easel,
  exactly as [Image 2], stays in frame at the left"* — not just a mention in the Static
  Description;
- a shot of its own when it matters most (a hero insert), so it is never only background.

If it *still* drifts in a delivered clip, the fix is a companion clip or a keyframe pair
built from the sheet — never accepting the drift and never stretching another clip to hide
it ([video-generation.md](video-generation.md)).

## 5. Consistency across the two clip families

Because frames and references cannot mix in one call, a production has two kinds of clip
and consistency is carried differently in each:

| Clip family | Consistency lives in | How |
|---|---|---|
| **Reference-driven** (talking scenes, product hero, anything with `--ref-*`) | the sheets + the identity blocks | attach the sheets, paste the blocks, restate ×3 |
| **Frame-driven** (exact first/last frames, keyframe interpolation) | the **frames** | generate each frame as an **image with `--reference` to the sheets** so the actor is already consistent in the still; the video call then only needs the identity block, not references |

A talking scene and a listening scene of the same person are therefore made by different
routes and still match, because both derive from the same sheet.

## 6. Voices are principals too

One TTS voice per speaking principal, chosen once (`strata generate voices`), recorded in
the bible, used for every line that character speaks. The line in the prompt's `{braces}`
is byte-identical to the TTS text. Different lines, same voice, same face — that is what
reads as one actor. Never a TTS laid over a clip that already speaks (SKILL.md, the clip's
audio rule).

## 7. Verify the bible held, per clip

Before a clip is accepted: the principal's face and wardrobe match the sheet (compare a
frame against it, not from memory), the hero element is present in every shot that lists
it, the location reads as the plate, and speech — if any — is that character's voice
(`strata captions` on the clip returns the line). A clip that fails goes back through the
route that made it with the block restated; it does not get "fixed" in the edit.

## Checklist

- [ ] Inventory done: principals, hero prop, locations, ranked — the brief's ranking wins
- [ ] One sheet per principal; supplied sheets used as-is; product sheet for the hero element; location plates with no people
- [ ] `bible.json` (URLs, seed, voices) + `bible.md` (identity blocks) beside the scene
- [ ] Every prompt pastes its blocks byte-identical, ×3, with references attached in the same order every call
- [ ] The hero element has its own sheet, its own sentence in every shot, and a shot of its own
- [ ] Frame-driven scenes get their frames generated with `--reference` to the sheets
- [ ] One voice per speaking principal; `{line}` identical to the TTS text
- [ ] Each clip checked against the sheet and its storyboard row as it lands — the clips themselves are generated in waves, all at once per wave ([assets.md](assets.md))
