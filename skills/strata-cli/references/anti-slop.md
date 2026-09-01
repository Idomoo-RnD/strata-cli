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

- **The middle of the range.** Shots around 2.5 s, moderate motion, some stillness, a mix that
  lands near −17 LUFS — on every brief, whatever the brief was (the measurements are in SKILL.md,
  *Place the piece on the range*). The most reliable tell there is, because it is what every unset
  default adds up to, and it survives a frame that passes every other check on this page. The fix
  is not "be more extreme" — it is to declare the position before the shot list and hit it. The
  middle is a legitimate answer, once.
- **Every piece a mix.** A little stillness *and* a little energy in everything, because both are
  "good craft". A quiet film measured stillness 0.57, an action reel 0.00; neither hedged.
- **One speed inside the piece.** Every shot the length of every other — the middle of the range one
  level down, and what a declared *mean* produces when treated as a value to hit everywhere. *When
  every shot gets the same time, none of them is the point: four hero layers, no hero.* The tell is
  a narrow spread — 0.32–2.16 s, 6.8:1, regularity 0.37 — where ten seconds of reference showreel
  runs 0.08–3.80 s, 47.5:1 and 0.03. Not fixed by bolting on a long shot and a short one: hold the
  shot carrying the idea, cut the transits before the eye settles.
- **The frozen payoff — the tell the range fix creates.** The one shot the piece is about, given the
  length it deserves and then nothing to do: a locked frame, or one gradient decaying to a dead
  frame at the cut. Measured: stillness 0.88 on our hold against 0.16 on the reference film's long
  takes and 0.53 on a locked logotype that at least kept its fill scrolling. **The end card is where
  it happens most:** 4 s of static wordmark with the product absent measures 95 of 98 frames below
  0.5 frame-to-frame difference, where the reference showreel's longest hold has none. Every
  declared hold names what moves through it; a hold that names nothing is dead air with a long
  duration on it.

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
  generated look. **At most one atmospheric effect per piece**, and it is the signature —
  unless the piece declared a maximal position and says so in the bible, in which case the
  stack is the look and every element in it is named.
- **The flat frame** — every authored surface a single fill with a hard edge, type at 100 %
  white over whatever is behind it, one grade across the whole piece. This is the default
  with no name, and it is what the last three pieces from this skill did. Light has a
  direction, type has a depth in the frame, the look changes between beats
  ([craft.md](craft.md), *Depth, light & atmosphere*).

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
- [ ] Every authored surface is one flat value — no falloff, no gradient, no shadow where planes meet
- [ ] Type is the same colour and opacity on every plate, and nothing ever passes in front of it
- [ ] A label, number or icon sits above a heading without carrying information
- [ ] The palette is cream+terracotta or black+one-neon with no brand behind it
- [ ] A decorative element would survive unchanged in a different brief
- [ ] Nothing was removed after the first snapshot
- [ ] The four numbers were never declared — or were declared and the render missed them
- [ ] Every shot is about as long as every other, or the longest is one nothing asked to be held
- [ ] The held shot (the end card included) has nothing moving, or stops changing before the cut
- [ ] The wordmark has no contact shadow, or its fill band runs level across every letter

---

## Where the rest of the skill points here

[SKILL.md](../SKILL.md), [craft.md](craft.md), [blocks.md](blocks.md) (which ships without
accent bars for this reason), [format.md](format.md), [recipes.md](recipes.md),
[motion-design.md](motion-design.md) and
[motion/09-production-qa.md](motion/09-production-qa.md) all defer here. **Add new patterns to
this file only** — everywhere else links.
