# Editing director — why this cut, here, now

The **editorial** layer: structure, pacing, shot selection, transitions, sound and on-screen
text chosen to fit the genre, format, audience, footage and platform. The rest of this skill
says how things *move* ([motion-design.md](motion-design.md), [motion-atlas.md](motion-atlas.md))
and how a frame is *composed* ([layouts.md](layouts.md), [anti-slop.md](anti-slop.md)). This
layer says why the cut lands where it does.

Optimise for the viewer's experience and the piece's purpose — never for the number of cuts
or the amount of visible technique. **Read this when planning the storyboard** (SKILL.md step
2), when a brief asks for a specific kind of video (commercial, explainer, testimonial,
trailer-style, social cutdown, sports recap), when cutting or critiquing existing footage, and
when a cut "feels off" and the note would otherwise be "make it faster".

---

## Contents

- [What overrides this file](#what-overrides-this-file)
- [Establish the editorial contract](#establish-the-editorial-contract)
- [Reference map](#reference-map)
- [Make decisions in this order](#make-decisions-in-this-order)
- [The cut decision gate](#the-cut-decision-gate)
- [Output — the editorial blueprint](#output--the-editorial-blueprint)
- [Critiquing an existing cut](#critiquing-an-existing-cut)
- [Non-negotiables](#non-negotiables)

## What overrides this file

- **The brief's own words and a brand document** — [brand.md](brand.md). Editorial grammar is
  composition; the brand owns the atoms and can dictate pacing and devices.
- **Measured engine facts** — the pinhole camera and bottom-anchored text in
  [format.md](format.md), the no-cuts rule for `.jet` overlays and generated single-take clips in
  [video-generation.md](video-generation.md), audio drift and retime maths in
  [video-editing.md](video-editing.md).
- **Anti-slop** — [anti-slop.md](anti-slop.md). These files name their own "failure modes";
  transition packs, whoosh-on-every-cut and B-roll-as-wallpaper are the same family of default.

## Establish the editorial contract

Determine from the request and the materials:

- Primary format and any secondary genre or tonal influence
- **Viewer promise** — what the audience should understand, feel, anticipate or do
- Runtime, aspect ratio, platform, viewing context, language, accessibility needs
- Story point of view and **information-release strategy**
- Available coverage, sync sound, narration, music, graphics, archive; legal or factual limits
- Whether the job is a concept, a paper edit, a time-coded blueprint, a critique or a recut

Ask only for what would materially change the result; otherwise state compact assumptions
and proceed. **Never invent shots, quotes, product claims, permissions or coverage** — mark
proposed or missing material explicitly. (For us: "missing coverage" usually means a clip to
generate, and that goes in the storyboard as a named asset.)

## Reference map

| File | Contains | Load when |
|---|---|---|
| [editing-grammar.md](editorial/editing-grammar.md) | the **six control dimensions** (shot density, information density, temporal compression, spatial continuity, POV restriction, sonic continuity), the editing languages, cut and transition vocabulary, sound–picture grammar, the **pacing-diagnosis table** (symptom → cause → intervention), review passes | any cut-logic, pacing, continuity, montage or "why does this feel flat" question |
| [short-form-performance.md](editorial/short-form-performance.md) | commercials and branded film, product demo / direct response, trailers and teasers, music video, creator and podcast video, **vertical short-form** (hook → development → payoff → action/loop), platform recuts, the cross-platform matrix | promos, ads, social, anything with a CTA — most jobs |
| [nonfiction-formats.md](editorial/nonfiction-formats.md) | factual-integrity rules, documentary, **interview / profile / testimonial**, news, **corporate / internal comms / education / explainer**, reality, **sports highlights**, multicam events | explainers, testimonials, internal comms, recaps, anything showing real people or real data |
| [narrative-genres.md](editorial/narrative-genres.md) | the genre control matrix; drama, romance, comedy, action, thriller, horror, sci-fi/fantasy, musical, animation, experimental — each with default grammar, sound and rhythm, failure modes | as a **modifier** — a reveal with thriller information release, a promo with trailer escalation, comedy timing for a social spot |
| [workflow-and-qa.md](editorial/workflow-and-qa.md) | editorial artefacts, footage intake, build passes, a style bible, giving and resolving cut notes, pacing evaluation, factual provenance QA, **accessibility as editorial design**, delivery QC | cutting or critiquing existing footage; any delivery with captions, flashing or rights questions |
| [research-sources.md](editorial/research-sources.md) | provenance for the five files above | when a rule here seems to conflict with something measured elsewhere — the measured fact wins |

For hybrids choose **one dominant mode and at most two modifiers** — e.g. *product explainer +
trailer escalation + social cutdown*. Do not average every influence into a generic fast montage.

## Make decisions in this order

1. **Define the scene's job.** Name the change that must occur: revelation, decision, reversal,
   proof, escalation, instruction, joke, scare, demonstration, identification, call to action.
2. **Choose point of view.** Whose knowledge, perception or emotion organises each beat. Treat
   reactions as story events, not filler.
3. **Build the backbone.** Dialogue- or narration-led: a paper edit before decorating. Action-,
   performance-, music- or observation-led: build around decisive visual and sonic events.
4. **Map information release.** What the viewer knows before, during and after each beat. Hide
   information only when the experience benefits and the payoff is fair.
5. **Design a rhythm curve.** Vary density, shot duration, movement and sonic intensity across
   sections. Contrast and recovery; constant speed reads as flat.
6. **Apply a restrained grammar.** A small transition, sound, text and motion vocabulary; every
   recurring device has a meaning. (This is where [anti-slop.md](anti-slop.md) applies.)
7. **Test the cut without polish.** A weak story cut must not be hidden with temp score, speed
   ramps, stock overlays or transition packs. For us: `strata preview --grid` and a `snapshot`
   of each key shot *before* generating assets or rendering.
8. **Add sound, graphics and finish** to clarify, connect, counterpoint or intensify decisions
   that already work. Onsets come from `strata beats` / `strata captions`, never by ear.
9. **Review in context.** Watch continuously at intended size: with sound, muted, with captions.
   Judge the opening, major turns, ending and the joins — not shots in isolation.

## The cut decision gate

At any proposed edit, answer the highest relevant question first:

1. Does it preserve or strengthen the intended emotion?
2. Does it advance story, meaning, proof or viewer action?
3. Does it land at the right internal rhythm?
4. Does it guide attention to the right place?
5. Does it preserve enough spatial and temporal causality?
6. Does picture/sound continuity help, or intentionally rupture, the moment?
7. For factual work, is the implication truthful and properly contextualised?
8. Is the result accessible, legible, rights-cleared and deliverable?

Continuity is a tool, not the top value in every scene. **Factual integrity is never traded
for drama or efficiency** — and a personalised video showing a viewer's own data is factual work.

## Output — the editorial blueprint

Unless asked for another form, the storyboard carries this (it slots into SKILL.md step 2):

```markdown
# Editorial direction
Format / dominant mode / modifiers:
Audience and viewing context:
Runtime and deliverables:
One-sentence viewer promise:
Editorial thesis:

## Rhythm arc
| Section or time | Story job | Viewer state | Picture density | Sound strategy | Transition out |

## Grammar
- Shot-selection rule:     - Point-of-view rule:      - Continuity/discontinuity rule:
- Transition palette:      - Sound palette:           - Text/graphic rule:
- Devices to avoid:

## Beat or time-coded edit map
| Time or beat | Required material (existing / to generate) | Edit action | Audio action | Purpose | Fallback |

## Integrity and accessibility
- Claims, chronology, consent, rights, captions, flashing, safe-zone checks

## Review tests
- Observable criteria for clarity, emotion, pacing, truth, accessibility, delivery
```

Exact timecodes only when source timecodes or a fixed runtime exist; otherwise beats, sections
or proportional timing, labelled as estimates. Distinguish a creative target from a measured fact.

## Critiquing an existing cut

Lead with the largest audience-facing problem, not a chronological list. For each note:
**evidence** (timecode or symptom) → **viewer effect** (confusion, lost anticipation, emotional
distance, fatigue, mistrust) → **likely cause** → **smallest useful change** → **trade-off**.
Separate must-fix from experiments. Preserve intentional roughness, silence, long takes or
jump cuts when they serve the established grammar.

## Non-negotiables

- No universal average shot length. Pace depends on information, movement, performance, sound
  and contrast — the six dimensions, not a number.
- Retention is not nonstop stimulation; a genre is not a transition preset.
- B-roll that only repeats the spoken words adds nothing — it must add evidence, context,
  counterpoint or emotion.
- Never fabricate reaction chronology, quotes, causality or event sound in factual work; never
  re-order dialogue if it changes meaning.
- Track the source and permission status of every asset; flag legal review when needed.
- Captions, readable text, safe zones and flash safety are editorial constraints from the
  start, not export-time fixes.
