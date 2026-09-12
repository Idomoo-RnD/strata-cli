# Anti-slop — defaults are not choices

**Mandatory. I read this before every storyboard and run its check before every render.**

Agent-composed work clusters around a small set of moves — and a single tempo — that appear
**regardless of subject**. Each is legitimate for some briefs. **None is legitimate as a
reflex.** The failure is not technical: the frame renders, the text is legible, nothing is
broken. It is that the piece could have come from any brief. That is what a viewer reads as
"AI". This file is the one place the patterns are named.

---

## Contents

- [The one rule that overrides this file](#the-one-rule-that-overrides-this-file)
- [The test (run at storyboard time, per element)](#the-test-run-at-storyboard-time-per-element)
- [The patterns](#the-patterns)
- [Before the render — the composition-tells checklist](#before-the-render--the-composition-tells-checklist)
- [Where the rest of the skill points here](#where-the-rest-of-the-skill-points-here)

## The one rule that overrides this file

**The brief's words always win.** If the brand document, or the user, asks for a scrim, a
rule under the title, a glow, a colour bar — that is a *choice*, and I make it exactly as
asked. This file governs only the axes the brief leaves free. Where it leaves an axis free,
I do not spend that freedom on a default.

---

## The test (run at storyboard time, per element)

For every decorative or structural element in the frame — anything that is not the
subject, the copy, or the media — **and for every number that sets the piece's tempo** — ask:

> **Would I have produced this for any similar brief?**

If yes, it is a default. Cut it, or replace it with a choice from *this* subject's own
world — its materials, instruments, vernacular — and say what changed. A bakery promo, a
fintech explainer and a sports hype reel must not share furniture. Then two disciplines:

- **Spend boldness in one place.** One signature element per piece. Two cancel each other.
- **Remove one thing before rendering.** Take an element out of the snapshot; if the frame
  still reads, it was decoration. Keep going until the next removal would hurt.

---

## The patterns

Named so they can be pointed at in a review. Each entry: the tell → why it reads as
generated → what to do instead.

### The tempo (the pattern that costs the most)

Use [design-contract.md](../make/design-contract.md) to judge rhythm against intent. The tell is
not a particular average or stillness value; it is unrelated briefs receiving the same treatment.
Let beat duration follow information and emotion. A wide shot-length range is not inherently
better than a narrow one; a one-shot ident need not acquire extra cuts to pass a metric.

For a payoff/end card, distinguish a purposeful locked hold from an intended live hold that stopped
or a clip that ran out. Both locked and live holds can be strong. Never add camera drift or noise
merely to change the diagnostic. Name what the hold gives the viewer, then inspect whether it does.

### Text backing

- **Scrim confined to the text box** — a dark rectangle behind the words, on every text
  block, by reflex. *Reads as a patch, not a design.* Order of answers: (1) put the text
  where the plate is quiet or where the layout reserves a safe band; (2) hold it on a solid
  panel that is **part of the layout** (a colour block, a card, a split); (3) grade the
  **whole** slot — a `solid` at 20–30 % over the entire image reads as a look; (4) let weight
  and size carry it. A scrim on the box alone is the last answer, used when the brand
  specifies one, brightness-matched to the plate ([blueprints.md](../make/blueprints.md)).
- **Caption bar on every shot** — a full-width band under the title on every cut. Use it once
  as a device, or make it the piece's signature; never as default furniture.

### Lines and bars

- **Rule under the title** — a thin bar under every heading. A rule is a *separator*; under
  a lone title it separates nothing. Mark a title with weight, size, colour or position.
  (The engine cannot render `underline`, and the workaround in [format.md](../format.md) is how
  to draw one when a brief asks — not an invitation.)
- **Side accent bar** — an 8 px colour bar on the left edge of a card, lower-third or
  panel. The single most recognisable agent tell in generated UI. Add one only when the
  brand's motion system has it, in the brand's colour.
- **Hairline dividers as texture** — rules between every row and column of a grid. Keep them
  where they encode a real boundary; otherwise let spacing do the work.

### Light and atmosphere

- **Glow on the hero word** by default. Glow means *emits light*; a word on a corporate
  slide does not. Use it when the subject is luminous (neon, a screen, a flare) or when it is
  the chosen signature.
- **Light-leak / lens-flare over the cut** as a reflex transition. It is one designed
  transition among many ([motion-design.md](motion-design.md) §8); pick it for a reason.
- **Grain + vignette = "film"**. Neither makes footage cinematic; the camera and the edit do.
  Grain belongs to a chosen analogue look ([video-styles.md](video-styles.md)); a vignette to
  a shot that needs the eye pulled in.
- **Stacking them** — glow + flare + grain + vignette + scrim on one frame is *the*
  generated look. **At most one atmospheric effect per piece**, and it is the signature —
  unless the piece declared a maximal position and says so in the bible, in which case the
  stack is the look and every element in it is named.
- **Unchosen surface treatment** — a flat frame where the direction promised physical depth, or
  obligatory gradients/shadows where it promised flat graphics. Judge coherence with the treatment,
  not the presence of lighting effects ([light-depth.md](light-depth.md)).

### Structure as decoration

- **Eyebrow / kicker label** — a tracked uppercase label above every headline. It borrows
  authority it has not earned. Use one only where it carries information (a section of a
  real series, a category the viewer needs).
- **Numbered markers (01 / 02 / 03)** over a list that is not a sequence. Number only a real
  process or a timeline where order carries meaning.
- **Icon tile above heading** — the small rounded-square icon container over every feature
  card. The universal feature-card template.
- **Identical card grids** — same-sized cards, icon + heading + text, repeated. Vary size or
  treatment so the hierarchy is visible.
- **Arrays and draw-ons because they are one line to author.** `repeat` makes 24 ticks free and
  `trim` makes any line write itself, which is exactly why both turn up carrying nothing: a tick
  ring around a logo that measures nothing, a dial with no value on it, a stroke that draws on
  under a static layout and only delays the read. A tick scale is a **scale** — something must be
  read against it. A line that draws itself says *this was made, follow it*: right for a route, a
  signature, a diagram being explained, a value filling a ring; wrong as trim on a card. Test:
  name what the array measures, or what the drawing hand is pointing at. No answer, no array.

### Colour when no brand exists

- **Cream ground + serif display + terracotta accent** — the default "tasteful" surface.
- **Near-black + one acid-green / vermilion / cyan pop** — the default "cool" surface.
- **Purple-to-blue gradient** on anything; **gradient text** anywhere.
- **Hero metric layout** — big number, small label, three supporting stats, gradient accent.

These are not banned colours. They are the palettes that appear when nobody chose one.
Derive the palette from the subject's world instead — the product's own material, the
place, the season, the medium — and name why.

### Motion

- **`outBack` / `outElastic` on body text or data.** Overshoot belongs to toys, mascots and
  deliberately playful brands — and there once, on the hero, at the brand's stated 10–20 % of
  the move ([craft.md](../craft.md), *Polish*, has the allowance). Text and numbers land clean.
- **Everything pulses / breathes.** Ambient motion on one thing keeps the frame alive; on
  everything it reads as nervous. The "web-animation tells" checklist in
  [motion-design.md](motion-design.md) covers the rest of motion slop — this file covers
  composition slop; run both.

### Sameness

The tell a clean render hides best: nothing is wrong with any frame, and every frame is the same
frame. The agent's own review of a 30 s gym ad named it — *"text just labelled footage with
rise-and-fade"*, cuts near the beats but no lift, impact or reveal choreographed to one. The
[design contract](../make/design-contract.md)'s three creative commitments exist so this is caught
at the storyboard, not after the render.

- **One text treatment for the whole piece** — every line enters the same way, at the same
  speed, as the same unit (always the whole line; never a word or a character), and nothing is
  emphasised. One entrance, one exit and one emphasis is the rule
  ([typography](motion/03-typography.md), *Craft rules*) — and the emphasis has to exist: the
  hero word lands differently from the rest, on the beat it belongs to.
- **Opacity is the only thing that animates** — nothing moves, scales, wipes or reveals; things
  fade. A fade is a transition, not a treatment. The declared typographic idea names what else
  moves, and the hero beat is where it moves.
- **One layout family on every cut** — full-bleed clip, copy bottom-left, scrim: layout 5 of 24
  ([layouts-catalogue.md](layouts-catalogue.md), *title-over-media*) on every beat. The beats
  that carry information get a layout of their own, declared in the contract.
- **Cuts timed to nothing** — hand-placed on round numbers, or "near" the beats. The spine is
  declared before the shot list is timed ([music.md](../shoot/music.md), *Video-specific craft*):
  onsets from `strata beats`, a voice's phrases, or a stated hand rhythm with its reason.

### Copy

- **Marketing buzzwords** — streamline, empower, supercharge, world-class, seamless,
  next-level. Instant tell. Say what the thing does.
- **Aphoristic contrast** — "Not X. Y." as a cadence, repeated. One is a device; three is a
  tic.
- **Em-dash overuse** in on-screen copy; more than a couple in a piece reads as generated.

---

## Before the render — the composition-tells checklist

If two or more are true of a frame, it will read as generated. Fix before spending a render.

**`strata validate` runs the mechanical half for me** — it warns, naming the pattern and this
file, on a box-shaped scrim under text, a rule under a title, a side colour bar, three-or-more
identical list markers, stacked atmospheric effects and bullet glyphs. A `.brand/brand.md` beside
the scene exempts the scrim warning, because a brand can own one. **The warnings are the checklist
firing, not noise**: I fix each or say why the brief earns it. *Measured:* **the scrim check has no
time awareness** — it pairs a text layer with any solid behind its box, including one that is never
on screen at the same time, so check the two layers' `start`/`duration` before removing anything.
What validate cannot see — the flat frame, eyebrow labels, numbered markers, the no-brand palettes,
buzzwords — I check by eye.

- [ ] Text sits on a box-shaped scrim that is not part of the layout
- [ ] There is a rule under a title that separates nothing
- [ ] A card or lower-third has a side colour bar the brand did not ask for
- [ ] More than one atmospheric effect (glow / flare / grain / vignette) is on the frame
- [ ] Surface treatment contradicts the approved flat/illustrative/spatial direction
- [ ] Type has insufficient contrast or inconsistent integration for its intended role
- [ ] A label, number or icon sits above a heading without carrying information
- [ ] The palette is cream+terracotta or black+one-neon with no brand behind it
- [ ] A decorative element would survive unchanged in a different brief
- [ ] Nothing was removed after the first snapshot
- [ ] Diagnostics have invented precision or no source/confidence label
- [ ] Shot timing is repetitive without an information or rhythmic reason
- [ ] A live hold lacks its promised motion, a locked hold has no purpose, or a clip ran out
- [ ] Spatial lighting contradicts the plate; do not apply this test to intentionally flat marks
- [ ] Every text layer has the same treatment, or opacity is the only thing that animates
- [ ] Every cut is title-over-media, with no declared layout for the information beats
- [ ] Cuts are hand-timed rather than placed on the declared rhythmic spine

---

## Where the rest of the skill points here

[SKILL.md](../SKILL.md), [craft.md](../craft.md), [blocks.md](../engine/blocks.md) (which ships without
accent bars for this reason), [format.md](../format.md), [recipes.md](../recipes.md),
[motion-design.md](motion-design.md) and
[motion/09-production-qa.md](motion/09-production-qa.md) all defer here. **Add new patterns to
this file only** — everywhere else links.
