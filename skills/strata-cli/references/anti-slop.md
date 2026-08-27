# Anti-slop — defaults are not choices

**Mandatory. I read this before every storyboard and run its check before every render.**

Agent-composed frames cluster around a small set of moves that appear **regardless of
subject**. Each is legitimate for some briefs. **None is legitimate as a reflex.** This file
is the one place that names them, so the list is maintained here and echoed elsewhere only
as a link.

The failure is not technical — the frame renders, the text is legible, nothing is broken. It
is that the frame could have come from any brief. That is what a viewer reads as "AI".

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
subject, the copy, or the media — ask:

> **Would I have produced this for any similar brief?**

If yes, it is a default. Cut it, or replace it with a choice that comes from *this*
subject's own world — its materials, instruments, vernacular — and say what I changed and
why. A bakery promo, a fintech explainer and a sports hype reel must not share furniture.

Then two disciplines from the studio floor:

- **Spend boldness in one place.** One signature element per piece — the thing it will be
  remembered by. Everything around it stays quiet. Two signatures cancel each other.
- **Remove one thing before rendering.** Look at the snapshot and take one element away.
  If the frame still reads, it was decoration. Keep going until removing the next thing
  would hurt it.

---

## The patterns

Named so they can be pointed at in a review. Each entry: the tell → why it reads as
generated → what to do instead.

### Text backing

- **Scrim confined to the text box** — a dark rectangle behind the words, on every text
  block, by reflex. *Reads as a patch, not a design.* Order of answers: (1) put the text
  where the plate is quiet or where the layout reserves a safe band; (2) hold it on a solid
  panel that is **part of the layout** (a colour block, a card, a split); (3) grade the
  **whole** slot — a `solid` at 20–30 % over the entire image reads as a look; (4) let weight
  and size carry it. A scrim on the box alone is the last answer, used when the brand
  specifies one, brightness-matched to the plate ([blueprints.md](blueprints.md)).
- **Caption bar on every shot** — a full-width band under the title on every cut. Use it once
  as a device, or make it the piece's signature; never as default furniture.

### Lines and bars

- **Rule under the title** — a thin bar under every heading. A rule is a *separator*; under
  a lone title it separates nothing. Mark a title with weight, size, colour or position.
  (The engine cannot render `underline`, and the workaround in [format.md](format.md) is how
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
  generated look. **At most one atmospheric effect per piece**, and it is the signature.

### Structure as decoration

- **Eyebrow / kicker label** — a tracked uppercase label above every headline. It borrows
  authority it has not earned. Use one only where it carries information (a section of a
  real series, a category the viewer needs).
- **Numbered markers (01 / 02 / 03)** over a list that is not a sequence. Number only a real
  process or a timeline where order carries meaning.
- **Icon tile above heading** — the small rounded-square icon container over every feature
  card. The universal feature-card template.
- **Identical card grids** — same-sized cards, icon + heading + text, repeated. Vary size,
  treatment or rhythm so the hierarchy is visible.

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
  deliberately playful brands. Text and numbers land clean.
- **Everything pulses / breathes.** Ambient motion on one thing keeps the frame alive; on
  everything it reads as nervous. The "web-animation tells" checklist in
  [motion-design.md](motion-design.md) covers the rest of motion slop — this file covers
  composition slop; run both.

### Copy

- **Marketing buzzwords** — streamline, empower, supercharge, world-class, seamless,
  next-level. Instant tell. Say what the thing does.
- **Aphoristic contrast** — "Not X. Y." as a cadence, repeated. One is a device; three is a
  tic.
- **Em-dash overuse** in on-screen copy; more than a couple in a piece reads as generated.

---

## Before the render — the composition-tells checklist

If two or more are true of a frame, it will read as generated. Fix before spending a render.

**`strata validate` runs the mechanical half of this list for me.** It reads the scene's
geometry and warns — naming the pattern and this file — on a box-shaped scrim under text, a
rule under a title, a side colour bar on a panel, three-or-more identical list markers,
stacked atmospheric effects, and bullet glyphs in copy. A `.brand/brand.md` beside the scene
exempts the scrim warning, because a brand can own one. **The warnings are the checklist
firing, not noise**: I fix each one or say in my report why the brief earns it. What validate
cannot see — eyebrow labels, numbered markers in text, the no-brand palettes, buzzwords — I
still check by eye against the list above.

- [ ] Text sits on a box-shaped scrim that is not part of the layout
- [ ] There is a rule under a title that separates nothing
- [ ] A card or lower-third has a side colour bar the brand did not ask for
- [ ] More than one atmospheric effect (glow / flare / grain / vignette) is on the frame
- [ ] A label, number or icon sits above a heading without carrying information
- [ ] The palette is cream+terracotta or black+one-neon with no brand behind it
- [ ] A decorative element would survive unchanged in a different brief
- [ ] Nothing was removed after the first snapshot

---

## Where the rest of the skill points here

[SKILL.md](../SKILL.md) Part 2 carries the short form and the mandatory read. The blocks in
[blocks.md](blocks.md) ship **without** accent bars for this reason. The underline
workaround in [format.md](format.md) and the rule-wipe recipe in [recipes.md](recipes.md)
carry a one-line echo. The finish pass in [motion-design.md](motion-design.md) is
subtractive for this reason. The atlas anti-pattern list in
[motion/09-production-qa.md](motion/09-production-qa.md) defers here. **Add new patterns to
this file only** — everywhere else links.
