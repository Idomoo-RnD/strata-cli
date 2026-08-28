# Director — the creative-direction pass for premium work

**When:** the brief says AAA, premium, cinematic, broadcast, launch film, hero animation — or the
client is one whose work is judged against agency motion design. **Before any scene JSON**, after
intake and the brand check, alongside the storyboard (SKILL.md step 2). For ordinary pieces the
storyboard's *Style* line and motion notes are enough; this pass is the extra rigour premium work
earns, and it costs an hour of thinking, not a render.

The pass exists because quality is not activity. The failure it prevents is the over-directed
piece: everything moves, everything has depth, every entrance overshoots, and the result reads as
templated energy rather than confident restraint. A director decides what stays **still** as
carefully as what moves.

## Contents

- [1. Thesis](#1-thesis)
- [2. Reference analysis](#2-reference-analysis)
- [3. Three directions — genuinely different](#3-three-directions--genuinely-different)
- [4. Key styleframes](#4-key-styleframes)
- [5. Motion hierarchy — what moves, what stays still](#5-motion-hierarchy--what-moves-what-stays-still)
- [6. The motion bible](#6-the-motion-bible)
- [7. Four passes to build it](#7-four-passes-to-build-it)
- [8. Hand-off checklist](#8-hand-off-checklist)

## 1. Thesis

One sentence the whole piece serves: *what the viewer should feel at the end, and the one image
that carries it.* Not a synopsis — a thesis. "The product is the calm in a loud world; the frame
gets quieter as it arrives." Everything downstream is tested against it: a move that does not
serve the thesis is cut, however good it looks.

Write it with the editorial contract ([editing-director.md](editing-director.md)): viewer promise,
information release, and the one dominant mode. The thesis is the promise in visual terms.

**Then place the piece on six traits**, low to high, before any direction is drawn. Adjectives in a
brief ("premium", "bold", "calm") are unusable until they are positions; these six are what the
motion bible's numbers are derived from, and two pieces with the same thesis and different traits
look nothing alike.

| Trait | Low | High | What it decides |
|---|---|---|---|
| **Energy** | slow, few events | fast, many events | shot lengths, cut rate, entrance durations |
| **Weight** | light, floaty | heavy, grounded | easing curves, settle length, counter-motion |
| **Precision** | loose, organic | exact, engineered | grid discipline, stagger regularity, path straightness |
| **Warmth** | cool, clinical | warm, human | palette, light, type roundness, sound texture |
| **Density** | sparse, one thing at a time | layered, many at once | elements on screen, the ⅓ rule, negative space |
| **Playfulness** | restrained, serious | witty, elastic | overshoot policy, squash, transition wit |

Write them as a line — *energy 3, weight 8, precision 9, warmth 4, density 2, playfulness 1* — and
check every later decision against it. A move that contradicts the traits is off-direction even
when it looks good on its own.

## 2. Reference analysis

If the user supplied references (a campaign, a film, a competitor's spot, an AEPX or Lottie), or
named one ("like the Apple launch films"), turn them into **measurable tokens before designing**:

```bash
strata deconstruct reference.mp4 -o ref_tokens.json     # shot lengths, cut rhythm, motion energy per shot, palette, onsets, loudness
```

Read the tokens, then add what the tool cannot measure — easing character (snap-and-settle vs
drift), stagger feel, camera behaviour (locked / push / handheld), type rhythm (words per second,
hold lengths), transition families, texture, sound grammar. For a named reference with no file,
[reference-styles.md](reference-styles.md) has ~25 signatures already reduced to tokens. Record
the tokens in the bible (§6) so "premium like X" becomes numbers the render is later compared
against (`strata review --reference`).

A reference is a **technique source, never a costume**: take its rhythm and restraint, not its
palette or its mark ([video-styles.md](video-styles.md) rule 1; a brand overrides everything).

**Measure these nine lanes.** The tool gives the temporal and colour ones; the rest come from
watching. Say what you measured and what you inferred — an estimate labelled as a fact is how a
reference gets copied wrong.

| Lane | What to capture |
|---|---|
| Editorial | shot count, shot lengths, information release, density curve, holds, what motivates each cut |
| Composition | grid, focal placement, scale contrast, negative space, framing, safe-area behaviour |
| Typography | family and weight, size ratios, tracking, leading, line breaks, animation unit, read time |
| Motion | entrance/exit durations, stagger, overshoot *or its absence*, settle, path, counter-motion, rest |
| Camera | shot size, lens character, locked vs moving, push/orbit/pan, parallax, horizon behaviour |
| Transitions | cut / wipe / match / matte families, direction, duration, frequency, narrative job |
| Surface | palette, contrast, texture, grade, shadow, glow, grain, blend behaviour, edge treatment |
| Sound | BPM and onsets, silence, VO rhythm, SFX placement, picture-to-sound lead/lag, mix hierarchy |
| Brand | repeated tokens, logo behaviour, signature device, prohibited treatments, consistency rules |

**Write the result as a reference profile**, and keep it beside the bible:

```markdown
# Reference profile

## Control map
| Reference | Controls | Does NOT control |          <!-- rank primary vs secondary; name conflicts, never average them -->

## Observed facts
| Time / frame | Observation | Measurement | Confidence (measured / estimated / inferred) |

## Motion signature
- Rhythm:            - Easing and weight:      - Type behaviour:
- Camera:            - Transitions:            - Surface:            - Sound:

## Transfer to Strata
| Source technique | Strata-native route | Approximation / pre-render route | Known gap |

## Reuse / avoid
- Preserve:   - Adapt:   - Do not copy:   - Unknowns needing a decision:
```

Two rules the profile enforces: **match relationships before isolated numbers** (relative timing,
focal order, amplitude hierarchy and contrast carry a look further than one exact duration), and
**protect the reference's restraint** — the absence of motion, sound, texture or camera movement is
evidence too, and the easiest thing to lose. Where VASCO has no native equivalent (true camera
depth-of-field, displacement), choose deliberately between a pre-rendered alpha overlay, generated
footage, a simpler native construction, or the AE exporter for exact reproduction — and record the
trade-off in the *Known gap* column rather than pretending the feature exists.

## 3. Three directions — genuinely different

Produce **three** directions that differ in *kind*, not in palette:

| Axis | Direction A | Direction B | Direction C |
|---|---|---|---|
| Motion posture | restrained — holds, hard cuts, one move per shot | choreographed — staggered builds, one continuous camera | kinetic — cut to the beat, type-led |
| What carries it | the frame (composition, light) | the camera (space) | the edit (rhythm) |
| Stillness | most of the piece | between camera phrases | only on the CTA |
| Risk | reads as cheap if the frames are weak | reads as showreel if the camera has no job | reads as generic social if the type is default |

For each: the thesis restated in that posture, the signature device, the transition family, the
sound motif, and **what it refuses to do**. Then recommend one and say why in one line. The user
picks; a direction the user did not see cannot be signed off.

Two of the three must include real stillness. If all three are "everything moves", the pass
failed its own point.

## 4. Key styleframes

Three frames, composed as final frames, for the chosen direction (or all three when the user
wants to compare): **the hook** (first 1–2 s), **the hero moment** (the shot the thesis names),
**the end card**. Build them as real scenes — `strata preview --grid` for the boxes, `strata
snapshot` for the actual type and plates — not as prose. Each frame answers: focal point, second
read, what is deliberately empty, the one accent, the type scale. Anti-slop runs on each
([anti-slop.md](anti-slop.md)): remove one thing before showing it.

These frames are the contract the motion is built toward; motion may not change the frames'
hierarchy.

## 5. Motion hierarchy — what moves, what stays still

Rank every element in every shot:

1. **The one thing that moves with intent** — the dominant motion idea of the shot (a reveal,
   a push, a cascade). Exactly one per shot.
2. **Secondary motion** — follow-through, counter-motion, ambient drift at ≤ 30 % of the primary,
   on a softer curve ([motion-design.md](motion-design.md) §5, counter-motion table).
3. **Still** — everything else. The wordmark that never moves. The CTA that lands and holds.
   The plate that is locked because the product is the motion.

Write the still list explicitly. A still element is not "nothing to do"; it is the anchor the
eye returns to and the reason the moving element reads. **Stillness, hard cuts and flat graphic
design are valid premium decisions** — a designed flat end card beats a drifting one.

## 6. The motion bible

One page, written before the scene, pasted into the storyboard, obeyed by every keyframe:

| Field | What it fixes | Example |
|---|---|---|
| **Focal order** | where the eye goes, per shot, in order | hero → headline → CTA |
| **Timing scale** | the piece's tempo family, in frames at the scene fps | snap 6 f · settle 12 f · phrase 36 f · hold 48 f |
| **Easing family** | one entrance ease, one exit ease, one drift ease — and the overshoot policy | `outExpo` in · `[0.7,0,0.84,0]` out · `inOutSine` drift · overshoot 0 % (rigid) |
| **Material logic** | what the things are made of, and what that does to inertia, deformation and settle | stone: heavy, no overshoot, long settle, dust on impact |
| **Type behaviour** | how words arrive, how long they hold, how they leave; captions vs kinetic | per-word rise 26 px, hold 0.5 s + 0.3 s/word, exit 65 % of entrance |
| **Camera rule** | locked, one push per shot, or one continuous move — and never what | locked; one 3 % push on the hero shot only |
| **Transition palette** | at most two families, named | hard cut; mask-rect wipe from the outer edge |
| **Sound motif** | the one SFX family, the bed's arc, the hit that lands the mark | oven-door thud on the wordmark; flour whoosh on wipes |
| **Signature device** | the single bold idea the boldness is spent on | fire footage inside the word RYE |
| **Forbidden moves** | what this piece will not do, so the temptation is pre-decided | no `outBack`, no crossfades, no glow, no camera drift on still frames, no scrims |

Numbers come from [motion-design.md](motion-design.md) (emotion → motion, material → overshoot,
the ⅓ rules, shot structure) and from the deconstructed reference when there is one. Where the
bible and a style entry disagree, the bible wins; where the bible and the brand disagree, the
brand wins.

## 7. Four passes to build it

Build the scene in four passes and do not mix them — finishing effects added during blocking is
how decoration creeps in:

1. **Blocking** — boxes, cuts, holds, the still list; judged on `strata preview --grid` and a
   `snapshot`. No animation yet beyond the cut points.
2. **Primary motion** — the one idea per shot, on the bible's curves and timing scale.
3. **Secondary motion** — stagger, follow-through, counters, the camera rule.
4. **Finishing** — effects (at most one atmospheric), grade, sound design, motion blur check: every
   moving layer blurred (the compiler's default — do not opt out) **and every animated camera carrying
   an explicit `"motion_blur": true`**, which is the one the default misses.

Each pass ends with the tells checklists ([anti-slop.md](anti-slop.md), [motion-design.md](motion-design.md)).
The rendered MP4 is reviewed after pass 4 ([review.md](review.md)); a must-fix sends the fix to
the pass it belongs to, not to a patch on top.

## 8. Hand-off checklist

- [ ] Thesis in one sentence, tied to the viewer promise
- [ ] References deconstructed to tokens (or reference-styles.md used) — recorded in the bible
- [ ] Three directions of different *kind*, two with real stillness, one recommended
- [ ] Three styleframes built as scenes and shown (hook, hero, end card)
- [ ] Motion hierarchy per shot, with an explicit still list
- [ ] The motion bible, every field filled, pasted into the storyboard
- [ ] Forbidden moves named before the first keyframe
- [ ] **The highest-risk moments named, and prototyped first** — the shot most likely to fail
      (a generated clip that must travel, a matte edge, a camera move through depth, a settle the
      whole piece hangs on). Prove it at blocking, not after the finishing pass
- [ ] **Acceptance criteria written for the critic** — what, specifically, the render must
      demonstrate for this direction to have worked ("the wall separates into readable layers",
      "the wordmark lands without overshoot", "the bed is audible on a phone"). The review scores
      against these, not against taste
- [ ] Sign-off on the direction and the frames before scene JSON
