---
name: strata-cli
description: Makes motion-design videos with the strata CLI — scene JSON compiled to .idm and rendered to MP4 via Idomoo. Use for any strata / IDM / VASCO video, motion graphics, kinetic type, explainer, promo, logo sting, personalized or data-driven video; for generating images, AI video clips, reference images, talking presenters, narration and music; for brand documents, Figma-to-video, and ffmpeg edits (trim, join, reframe, grade). Not for the Idomoo cloud briefs API.
---

# Strata CLI — motion design with intent

Make the viewer understand, feel or notice something specific to this subject. Composition,
typography, motion, editing and sound are one design system. Advanced features are options,
not a quality checklist: a restrained flat typographic film can be exceptional.

## What is law and what is yours

**Engine constraints are facts, not taste.** Read the actual syntax for the features you use;
never invent a key. [traps.md](traps.md) owns costly failure modes;
[capabilities.md](engine/capabilities.md) separates native features, compiler sugar and approximations.
Existing measurements retain their scope; do not generalize one render into a universal rule.

**Craft recommendations are defaults, not bans.** Start from the subject, approved brand and viewing
context. A hard cut, dissolve, locked hold, flat surface, synchronous move or silence can be right.
Explain a deliberate exception in the storyboard. User intent cannot override an engine limit,
privacy restriction or factual claim; explain the limitation and propose an honest alternative.

**One contract, not competing checklists.** [design-contract.md](make/design-contract.md) owns
intent, hold classification, confidence in diagnostics and approvals. [review.md](qa/review.md)
owns final acceptance and revision policy. [upload.md](shoot/upload.md) owns publication consent.
[personalization.md](engine/personalization.md) owns what varies per viewer.
[Evidence index](qa/evidence-index.md) identifies tested claims and pending proofs.

## Quick start

```bash
strata brief init "<the request, verbatim>"
strata route BRIEF.md --phase intake
strata route BRIEF.md --phase direction
# concept approval → permitted styleframe/risk probes → production approval
strata route BRIEF.md --phase authoring --features text
strata preview scene_v1.json --at 2 --grid
strata validate scene_v1.json
strata compile scene_v1.json -o scene_v1.idm
strata render scene_v1.json --library <approved-id> -o scene_v1.mp4
strata route BRIEF.md --phase review
strata review scene_v1.mp4 --scene scene_v1.json -o review/
strata route BRIEF.md --phase delivery
```

Use `strata <command> --help` before guessing flags; help is offline and exits 0. `--json`
returns structured output; errors go to stderr. The [command index](cli/all-commands.md) lists
inputs, outputs, runtime dependencies and side effects. An edit-only request goes directly to
[video-editing.md](shoot/video-editing.md); no scene, storyboard ceremony or AI footage is required.

## What to read for this brief

Read every **required-now** path, not every optional catalogue. An index page is not the syntax
it links to. Re-route at phase changes, and when choosing a new technique. Explicit `--mode` and
`--features` override ambiguous keyword classification; preserve negative constraints in BRIEF.md.
Use `--skill-dir <path>` to route against the same installed/source skill you are reading.

<!-- route-table:begin — generated from src/route.mjs by scripts/sync-route-table.mjs; edit route.mjs, not this -->
Run `strata route BRIEF.md --phase <phase>` (default: `direction`). Read **requiredNow**
in full; **requiredForCapabilities** before using those techniques; **lookup** only when
needed; **beforeDelivery** before declaring completion. `read` is an alias of `requiredNow`.

Every task: `workflow.md`. Mechanical edits use only their editing packet.

| Phase | Required core pages for scene-design tasks |
|---|---|
| intake | `make/before-you-start.md` |
| direction | `make/plan.md` |
| feasibility | `make/design-contract.md`, `engine/capabilities.md`, `traps.md` |
| authoring | `make/author.md`, `traps.md`, `engine/scene.md`, `engine/layers.md`, `engine/animation.md` |
| review | `qa/review.md`, `craft/motion/09-production-qa.md`, `direct/editorial/workflow-and-qa.md` |
| delivery | `make/deliver.md`, `qa/review.md`, `craft/motion/09-production-qa.md`, `direct/editorial/workflow-and-qa.md` |

Direction additionally reads `make/design-contract.md`, `craft/anti-slop.md`, `craft/grid.md`, `craft/motion-design.md`. Matched route pages below
are required at intake/direction; their topic lists are lookups, not a read-all assignment.

| Kind | Landing page | Topic lookups |
|---|---|---|
| supplied material (storyboard, script, PDF, URL, sheets, footage, voice) | `routes/supplied-material.md` | `make/intake.md`, `make/production-bible.md`, `video-generation.md`, `video-generation-advanced.md` |
| a TV / broadcast ad, promo, launch, sale, hero film | `routes/tv-ad.md` | `direct/editing-director.md`, `direct/editorial/short-form-performance.md`, `direct/editorial/editing-grammar.md`, `make/blueprints.md`, `craft/video-styles.md`, `shoot/video-prompts.md`, `assets.md`, `craft/motion/04-transitions.md`, `craft/motion/07-narrative-sound-emotion.md`, `shoot/music.md` |
| social / vertical / UGC / trailer-style | `routes/social.md` | `direct/editing-director.md`, `direct/editorial/short-form-performance.md`, `make/blueprints.md`, `craft/video-styles.md`, `craft/motion/04-transitions.md`, `shoot/music.md` |
| an explainer, tutorial, testimonial, internal comms, recap, sports | `routes/explainer.md` | `direct/editing-director.md`, `direct/editorial/nonfiction-formats.md`, `direct/editorial/editing-grammar.md`, `make/blueprints.md`, `craft/motion/07-narrative-sound-emotion.md` |
| personalized / data-driven / a chart or a stat | `routes/personalized.md` | `engine/personalization.md`, `craft/motion/06-ui-data-brand.md`, `direct/editorial/nonfiction-formats.md` |
| a presenter, spokesperson, talking head, dialogue | `routes/presenter.md` | `shoot/avatar.md`, `video-generation-advanced.md`, `make/production-bible.md`, `shoot/captions.md` |
| a logo sting, brand ident, product hero | `routes/logo-ident.md` | `make/blueprints.md`, `craft/video-styles.md`, `assets.md`, `craft/motion/05-shape-effects-texture.md`, `craft/motion/06-ui-data-brand.md`, `recipes.md` |
| kinetic type / typographic / captions-heavy | `routes/kinetic-type.md` | `craft/motion/03-typography.md`, `recipes.md`, `layouts.md` |
| music-led / beat-synced / audio-reactive | `routes/music-led.md` | `craft/generative-fx.md`, `shoot/music.md`, `craft/motion/07-narrative-sound-emotion.md`, `craft/motion/04-transitions.md` |
| a genre feel (thriller, comedy, trailer, documentary) | `routes/genre.md` | `direct/editorial/narrative-genres.md`, `craft/reference-styles.md`, `craft/motion/08-styles-index.md` |
| "make it feel like X" (a film, director, campaign) | `routes/feel-like.md` | `craft/reference-styles.md`, `craft/motion/08-styles-index.md` |
| premium / AAA / cinematic / broadcast quality (in addition to the kind above) | `routes/premium.md` | `direct/director.md`, `craft/motion/01-foundations.md`, `craft/motion/02-choreography-space-camera.md`, `craft/motion/03-typography.md`, `craft/motion/09-production-qa.md` |
| any generated video clip | `routes/generated-clips.md` | `video-generation.md`, `shoot/video-prompts.md` |
| an edit of supplied footage (cut, join, reframe, retime) | `routes/footage-edit.md` | `shoot/video-editing.md`, `direct/editorial/workflow-and-qa.md` |
| brand material supplied, "on brand", a series | `routes/brand-kit.md` | `brand/brand.md` |
| Figma | `routes/figma-handoff.md` | `brand/figma.md` |

**Capability dependencies** — inferred from the brief or explicitly selected with
`--features text,camera,...`; included in feasibility/authoring required reads.

- **text:** `engine/text.md`, `recipes/type.md`
- **camera:** `engine/comps-camera.md`
- **masks:** `engine/effects-masks.md`, `recipes/masks.md`
- **audio:** `shoot/music.md`, `shoot/sound.md`
- **generation:** `shoot/clip-rules.md`, `shoot/clip-params.md`, `shoot/clip-length.md`, `shoot/upload.md`
- **presenter:** `shoot/avatar.md`, `shoot/ref-audio.md`, `make/production-bible.md`, `shoot/upload.md`
- **captions:** `shoot/captions.md`, `shoot/upload.md`
- **data:** `engine/personalization.md`, `recipes/data-viz.md`
- **alpha:** `shoot/alpha.md`, `shoot/clips-for-alpha.md`
- **tracking:** `recipes/tracking.md`, `engine/effects-masks.md`

`--mode edit|design|generation|template` resolves ambiguous task classification.
Re-route as the chosen techniques change. No kind matched? Choose a structure with
`make/blueprints.md`, amend the brief, then route again. Paths are relative to this skill root.
<!-- route-table:end -->

## Before authoring — four checks

- **Supplied material?** Read [intake.md](make/intake.md). Execute an approved storyboard rather
  than redesigning it. A recurring subject/set uses a [production bible](make/production-bible.md).
- **Mechanical edit or design?** Trim, join, reframe and retime alone use the editing packet.
  Add scene graphics only when the ask needs them.
- **Brand?** Read `.brand/brand.md` if present. Otherwise offer to capture supplied brand material
  using [brand.md](brand/brand.md); flag unknown atoms, do not fabricate them.
- **Figma?** Read [figma.md](brand/figma.md); layout import is not motion authoring.

Check stored preferences before repeating questions. Unattended mode permits creative decisions
within the approved scope, not permission to invent claims, choose an unapproved library or publish
sensitive data. Local or non-S3 caption sources can be re-hosted: read the upload policy first.
`--allow-public-upload` acknowledges an informed choice for non-sensitive material, not a privacy waiver.

## Place the piece on the range

Name the viewer promise, focal order, rhythm, material response and signature before optimizing
numbers. A reference contributes relationships, not a costume. Measure it with `strata deconstruct`
when available; distinguish **observed**, **estimated**, **inferred** and **uncalibrated**.

The four diagnostic lanes are shot-length range, motion energy, stillness and loudness. They are
not four mandatory invented numbers. Runtime and destination audio requirements are delivery
constraints; energy/stillness are image-dependent diagnostics. Preserve source, scale, confidence
and comparison limits. Do not interpolate a universal target from unrelated film examples.

Give each beat the time its message earns. Name why the longest and shortest shots exist; do not
pad or clip them to achieve a ratio. Each hold is **locked** (intentional stillness) or **live**
(intentional motion), with its purpose and duration. Both can pass. An exhausted clip or an
unintended stop fails; adding grain or camera drift just to change a metric is not a fix.
See [design-contract.md](make/design-contract.md) and [motion.md](craft/motion.md).

## The shape of a job

1. **Intake and provisional plan.** Establish approved copy/claims, delivery, assets, permissions,
   budget and unknowns in BRIEF.md. Sketch beats before deciding clip lengths.
2. **Direction and approval.** Show a subject-specific idea and layout. For premium work use
   [director.md](direct/director.md). Prototype JSON for styleframes and risk tests is allowed
   before final production approval, within an agreed scope/budget. Snapshots are cloud operations.
3. **Assets.** Reuse approved files. Generate footage when photographed motion/material is needed;
   choose vectors, stills or layer animation when they serve better. Clips must cover their slots
   and trim-ins. Generate approved dependencies in waves; retain source files and hosted URLs.
4. **Author.** Read [scene](engine/scene.md), [layers](engine/layers.md),
   [animation](engine/animation.md) and selected feature pages. Build blocking → primary motion →
   secondary → finish. Use [recipes](recipes.md), distinguishing runnable examples from fragments.
5. **Preflight.** Preview layout/mask outlines, validate, inspect warnings, compile. Wireframes do
   not prove glyphs, real compositing or 3D projection. Use targeted snapshots/probes where needed.
6. **Render.** Use the user's approved library. Version each cut (`promo_v1` → `promo_v2`). Track
   cloud snapshots, generation and probes as well as full renders. Foreground or poll long jobs;
   a background process does not guarantee the agent will resume.
7. **Review and delivery.** Inspect the MP4 and evidence, not only a poster. Fix consolidated
   blockers; preserve the keep list. Two full renders is a default spend allowance, not a release
   rule. Ship the first if it passes; never ship a failed second as final. Every candidate gets a
   regression check. Beyond budget, request approval or hand over an explicitly incomplete preview.
   The [Definition of Done](make/deliver.md#definition-of-done) is required before completion.

## The traps that cost a render

Read [traps.md](traps.md) before compiling any scene; feature pages carry syntax and evidence.

- No anchor: `position` is a delta from the box. With an anchor: it targets the absolute pivot.
- Layer names must be unique across the whole scene. Renaming a duplicate changes its data key.
- Camera coordinates/projection are not ordinary layer offsets; depth goes on position z.
- Set every sub-comp duration. Moving-camera blur needs its own explicit setting.
- Fonts need actual files and glyph coverage. Box previews do not prove text placement.
- Keep generated dialogue audio as the voice; do not double it with the input TTS.
- An alpha-video overlay needs `.jet`; opaque framed/plate footage may remain MP4.
- Check clip duration, trim-in and fps before assigning a slot. Source exhaustion holds a frame.
- `chart bars --data` emits geometry at author time. `render --data` substitutes content, not bar
  heights or donut sweeps. Changing geometry needs per-row emission or chart-image replacement.
- Scene assets are embedded local files, not a reason to publish them to an upload store.

## Troubleshooting — by symptom

| Question | Open |
|---|---|
| Which command / flag / side effect? | [commands](commands.md), [command index](cli/all-commands.md) |
| Can Strata actually do this? | [capabilities](engine/capabilities.md) |
| Valid compile, wrong video? | [traps](traps.md) |
| What keys are supported? | [format index](format.md), then the relevant leaf page |
| Is the motion working? | [review](qa/review.md), [case studies](craft/case-studies.md) |
| What can vary per viewer? | [personalization](engine/personalization.md) |
| Is publication allowed? | [upload policy](shoot/upload.md) |
| A specific question mid-job? | [INDEX.md](INDEX.md) — search its ownership/answer rows |

## References

[Workflow](workflow.md) · [Craft](craft.md) · [Layouts](layouts.md) · [Assets](assets.md) ·
[Video generation](video-generation.md) · [Advanced generation](video-generation-advanced.md) ·
[Recipes](recipes.md) · [Motion atlas](craft/motion-atlas.md) · [Visual lessons](craft/case-studies.md) ·
[Full topic index](INDEX.md). These are reference libraries, not an instruction to load every page.
