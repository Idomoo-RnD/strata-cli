# Motion design atlas — the vocabulary layer

~700 named motion concepts: what each one is, when to use it, and its key parameter or
gotcha. **This is a lookup, not a read-by-default file.** Reach for it when you need a name
for something, want options you would not have thought of, are reviewing or critiquing
motion, or are translating a brief's adjectives into decisions.

Naming the concepts you use is the point. "Stagger the cards on a ramp, land the hero on the
downbeat, exit on a mask wipe" is reviewable; "make it feel nice" is not.

---

## How this sits next to the rest of the skill

| | Owns |
|---|---|
| **This atlas** | **names and taxonomy** — the long tail of what exists and what it is called |
| **[motion-design.md](motion-design.md)** | **execution and numbers** — the same ideas as real VASCO keys, measured on this engine |

⛔ **Where both give a number for the same thing, `motion-design.md` wins.** Its figures were
measured on this engine; the atlas quotes general-industry values. Every such collision has
already been replaced by a link — if you find a bare number here that contradicts one of ours,
that is a bug in this file, not a choice to make.

Two rules from elsewhere override anything in here, always:

- **A brand document beats every style and token suggestion** — [brand.md](brand.md).
- **Measured engine facts beat craft advice** — the pinhole camera and bottom-anchored text in
  [format.md](format.md), the no-cuts rule for `.jet` overlays in
  [video-generation.md](video-generation.md).

## Workflow — brief to spec

1. Set the six tone sliders (energy, weight, precision, warmth, density, playfulness) and map
   adjectives to parameters — [07](motion/07-narrative-sound-emotion.md) §4, then the
   emotion-to-motion table in [motion-design.md](motion-design.md).
2. Lock the timing and easing vocabulary — [01](motion/01-foundations.md).
3. Choreograph the frame: focal order, entrance order, depth, camera — [02](motion/02-choreography-space-camera.md).
4. Choose text behaviour: **one** entrance, **one** exit, **one** emphasis — [03](motion/03-typography.md).
5. Choose the transition system: one primary, one secondary — [04](motion/04-transitions.md).
6. Add surface and effect decisions only where they serve the story — [05](motion/05-shape-effects-texture.md).
7. Apply domain rules — UI, data, brand — [06](motion/06-ui-data-brand.md).
8. Check structure, pacing and sound sync — [07](motion/07-narrative-sound-emotion.md) §1–3.
9. If the brief names a look, find it in [08](motion/08-styles-index.md), then execute it from
   [video-styles.md](video-styles.md) if it has a deep entry there.
10. Run the craft QA and anti-pattern scan — [09](motion/09-production-qa.md).

Load only what the task needs; each file is self-contained.

## Reference map

| File | Contains | Load when |
|---|---|---|
| [01-foundations.md](motion/01-foundations.md) | Disney 12 for graphics, motion + UI principles, timing and spacing, easing families and bezier vocabulary, physics and weight, rhythm patterns | any timing, easing, or "why does this feel wrong" question |
| [02-choreography-space-camera.md](motion/02-choreography-space-camera.md) | staging and focal control, composition in motion, depth and 2.5D/3D, camera language | multi-element scenes, parallax, camera moves |
| [03-typography.md](motion/03-typography.md) | 40+ text entrances and exits, kinetic-type craft rules, numbers, RTL and multilingual | any text, headline, counter, caption, Hebrew or multilingual work |
| [04-transitions.md](motion/04-transitions.md) | cuts, dissolves, wipes, motion-driven and effect-driven transitions, transition-system design | scene changes, editing rhythm |
| [05-shape-effects-texture.md](motion/05-shape-effects-texture.md) | shape animation, masks and mattes, optical effects, particles, colour and compositing, materials | logo builds, icon animation, effects, backgrounds, look |
| [06-ui-data-brand.md](motion/06-ui-data-brand.md) | micro-interactions, navigation, loaders, data and chart motion, **personalized data moments**, brand motion systems and tokens | product UI, dashboards, infographics, logo stings, and every personalized piece |
| [07-narrative-sound-emotion.md](motion/07-narrative-sound-emotion.md) | story arcs, beat sheets, pacing, editing, sound and music sync, emotional mapping and tone sliders | briefs, scripts, storyboards, music-driven pieces, tone questions |
| [08-styles-index.md](motion/08-styles-index.md) | 77 styles, eras and industry conventions, one line each with its signature motion cue | "make it feel like X", genre references, style pairing |
| [09-production-qa.md](motion/09-production-qa.md) | frame rates, formats, safe areas, accessibility, QA checklist, anti-patterns, default numbers | before delivery, technical specs, reviews and critiques |

## Output patterns

- **Asked for ideas or directions** — list 5–10 concepts by name, one line each on why it fits,
  grouped by the workflow steps. Offer one bold option and one safe option.
- **Asked to critique** — name the violated concept or anti-pattern, say what the viewer
  experiences, give the fix with a parameter ("the exit is slower than the entrance; cut it to
  the ratio in motion-design.md").
- **Asked for a spec** — a table of element → concept → parameters (duration, easing, stagger,
  distance, trigger), plus the transition system and the sound cues.
- **Asked for vocabulary** — define it, give its range, and say when *not* to use it.
- **Asked to write a brief** — tone sliders, style reference, hook and arc, three to five named
  concepts that define the piece, QA commitments.

When two concepts conflict — overshoot versus legibility, density versus focal clarity — **the
one that protects comprehension wins.**
