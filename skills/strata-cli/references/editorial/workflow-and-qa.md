# Editorial workflow, review, integrity, and quality control

> **Engine anchors.** Our own gates already exist and come first: the storyboard sign-off in
> SKILL.md, `strata preview --grid` and `strata snapshot` before any render, `strata validate`
> (which also flags composition tells - [anti-slop.md](../anti-slop.md)), and the Definition of
> Done. Use this file's passes for what those do not cover: cut notes, factual provenance,
> accessibility as a design constraint, and delivery QC. Technical delivery (audio drift,
> retimes, concat) is measured in [video-editing.md](../video-editing.md).

Use this reference to turn style direction into an edit process, review a cut systematically, or prepare deliverables. Scale the process to the risk and size of the project; a 20-second creator clip does not need feature-film paperwork, while a sensitive documentary needs more than a creative timeline.

## Contents

- [Editorial artifacts](#editorial-artifacts)
- [Footage intake and discovery](#footage-intake-and-discovery)
- [Build passes](#build-passes)
- [Style bible](#style-bible)
- [Give and resolve cut notes](#give-and-resolve-cut-notes)
- [Audience and pacing evaluation](#audience-and-pacing-evaluation)
- [Factual integrity and provenance QA](#factual-integrity-and-provenance-qa)
- [Accessibility as editorial design](#accessibility-as-editorial-design)
- [Delivery and technical QC](#delivery-and-technical-qc)
- [Handoff summary](#handoff-summary)

## Editorial artifacts

Create only the artifacts the project needs:

| Artifact | Use | Minimum useful fields |
|---|---|---|
| Editorial brief | Align purpose before cutting | audience, promise, runtime, mode, POV, objective, constraints |
| Footage inventory | Avoid inventing coverage and reveal gaps | source, date, people, content, quality, rights, notes |
| Factual chronology | Separate real order from dramatic order | date/time, event, source, certainty, discrepancies |
| Transcript/paper edit | Build dialogue-led structure | source timecode, speaker, selected text, context, status |
| Selects/string-outs | Compare material by function | scene/theme/character, rating, take, performance, issue |
| Style grammar | Keep formal choices consistent | shot rule, rhythm, transition, sound, text, exceptions |
| Rhythm map | Shape intensity and recovery | section, story job, density, sound, viewer state |
| Rights/provenance log | Track authorized use | asset, owner/source, license, territory, term, status |
| Cut note log | Make revisions traceable | version, timecode, symptom, request, decision, status |
| Delivery matrix | Prevent variant mistakes | platform, runtime, ratio, fps, audio, captions, slate, deadline |

## Footage intake and discovery

### Inventory before interpretation

- Inspect folder structure, metadata, camera/card continuity, audio channels, frame rates, resolutions, color metadata, and timecode.
- Preserve originals and work from managed copies, proxies, or linked media according to the production workflow.
- Sync and group sound and multicamera material before performance judgments.
- Identify missing, corrupt, offline, clipped, out-of-focus, unstable, or rights-unknown material.
- Keep generated, stock, archive, reenacted, and original material distinguishable in bins and logs.

### Log for editorial retrieval

Log decisions another editor can search:

- Who, what, where, when
- Action, topic, claim, emotion, and change
- Strong beginning and ending points
- Visual proof, inserts, reactions, room tone, wild tracks, and transitions
- Performance quality, continuity limitations, and technical issues
- Rights, consent, sensitivity, and fact-check flags

A marker named "good" is not reusable knowledge. A marker named "03:14 subject stops before answering layoffs question; visible hesitation" is.

### Separate evidence from interpretation

For factual footage, label:

- **Observed:** directly visible or audible
- **Stated:** claimed by a participant or source
- **Verified:** checked against reliable evidence
- **Inferred:** editorial interpretation
- **Unknown/disputed:** unresolved or contested

This distinction prevents a dramatic hypothesis from silently becoming a fact.

## Build passes

### 1. Backbone pass

- Dialogue-led: make a paper or radio edit with source links.
- Action/event-led: assemble intention, decisive events, results, and consequences.
- Music-led: map song form and performance sync.
- Animation: time dialogue, boards, and essential effects in the story reel.

Do not solve coverage, graphics, color, or effects before the piece has a coherent change.

### 2. Structure pass

- Test opening promise, progression, turns, climax/payoff, and ending.
- Remove scenes or beats that repeat function even when they contain attractive material.
- Separate necessary context from information that can arrive later.
- Create contrast between sections in pace, scale, sound, or point of view.

### 3. Performance and POV pass

- Track who owns each beat and whose reaction matters.
- Compare takes in surrounding context.
- Preserve thought, listening, physical behavior, and subtext.
- Check that reaction chronology remains truthful in factual work.

### 4. Picture grammar pass

- Refine eye trace, geography, action continuity, shot scale, focal hierarchy, and transition motifs.
- Replace coverage that merely hides edits with images that add story or evidence.
- Review visual density at intended display size.

### 5. Sound pass

- Repair intelligibility and continuity before adding score.
- Establish scene ambience and sound perspective.
- Add motivated effects, pre-laps, tails, contrast, and silence.
- Shape music around story turns using licensed or clearly temporary material.
- Listen without picture to catch unclear structure and artificial dialogue.

### 6. Text, graphics, and access pass

- Add titles, lower thirds, source labels, captions, diagrams, disclaimers, and CTA.
- Resolve competition among speech, captions, graphics, and picture.
- Plan audio description or integrated description when essential visual information is not spoken.
- Review safe zones, reading time, contrast, and localization expansion.

### 7. Fine cut and finishing handoff

- Refine frame-level timing after structure is stable.
- Remove temporary effects that have no defined story purpose.
- Lock or clearly flag shots needed for VFX, mix, grade, animation, licensing, and fact-check.
- Keep handles and turnover metadata required by the actual finishing workflow.

## Style bible

For a series, campaign, or team edit, document a small formal system:

```markdown
Editorial thesis:
Viewer promise:
Dominant POV:

Picture grammar:
- Establishing rule
- Default shot-scale behavior
- Action and reaction rule
- Allowed discontinuity
- Recurring visual motif

Rhythm:
- Baseline behavior
- Escalation behavior
- Recovery behavior
- Ending behavior

Transitions:
- Default
- Named exceptions and their meaning

Sound:
- Dialogue treatment
- Ambience and effects
- Music role
- Silence rule

Text and graphics:
- Caption behavior
- Lower thirds / sources
- CTA / title cards
- Platform-safe placement

Integrity and access:
- Factual boundaries
- Rights and disclosure
- Caption, description, contrast, and flash checks
```

Do not specify a device merely because it is available. A useful style bible explains what each device means and when it should not appear.

## Give and resolve cut notes

### Strong note anatomy

Use:

`[timecode/beat] observation → viewer effect → requested outcome → optional experiment`

Example:

`01:12 — We cut to the key before the character notices it, so the later glance is no longer a discovery. Preserve surprise by holding on her through the sound cue, then reveal the key on her eyeline.`

Avoid notes that prescribe style without identifying the experience, such as "more dynamic," "make it pop," or "speed this up."

### Triage

| Priority | Meaning | Response |
|---|---|---|
| Must fix | Wrong fact, broken story, missing requirement, rights/access/safety issue | Resolve before approval |
| High leverage | Major clarity, emotion, pacing, or brand problem | Test the smallest structural change |
| Polish | Frame trim, mix nuance, graphic alignment, continuity | Address after structure |
| Experiment | Plausible alternative with a tradeoff | Version separately; compare in context |

When notes conflict, restate the shared outcome and compare alternatives against it. Do not average two incompatible ideas into a weak compromise without testing.

### Version comparison

- Change one major hypothesis per version when possible.
- Label versions by editorial question, not only `v12` and `v13`.
- Watch versions from before the changed beat through its consequence.
- Preserve a decision log for reversals and rejected experiments.

## Audience and pacing evaluation

### Observable tests

Ask viewers to answer without prompting:

- Who wants what?
- What changed in this scene or piece?
- What information was confusing or late?
- Where did attention drop, and what did they already understand then?
- What was the strongest image, line, sound, joke, scare, proof, or product benefit?
- What do they expect or want to happen next?
- What action, if any, should they take?

Do not ask only whether they "liked it." Preference does not diagnose structure.

### Retention data

When analytics are available:

- Use drop-offs and replays as questions, not automatic edit commands.
- Compare the moment with audience source, device, placement, promise, and surrounding information.
- A replay can signal delight, confusion, dense information, or accidental looping.
- A drop can reflect fulfilled intent, wrong audience, technical issue, weak value, or deliberate discomfort.
- Test a focused hypothesis before generalizing a convention to all future videos.

## Factual integrity and provenance QA

Before approval, verify:

- Every consequential quote links to source and surrounding context.
- Translations and subtitles preserve meaning and uncertainty.
- Dates, locations, names, titles, statistics, and on-screen documents are correct.
- Archive and stock are labeled when viewers could mistake their time or place.
- Reactions and event sound are contemporaneous or clearly disclosed.
- Music, grading, slow motion, and sound effects do not create a materially false implication.
- Allegations, disputes, and responses follow the applicable editorial standard.
- Generated or synthetically altered material is disclosed and handled according to the publisher's policy.
- Consent, privacy, safety, and participant-impact issues have named owners.
- Rights, licenses, releases, and restrictions are reflected in the final cue/provenance log.

For news and current affairs, the applicable newsroom policy overrides stylistic preferences. For documentary, retain a review path that includes ethical accountability, not only legal clearance.

## Accessibility as editorial design

### Captions and transcripts

- Caption meaningful speech and non-speech audio needed to understand the piece.
- Synchronize captions to meaning and speaker changes; correct automatic transcription.
- Keep captions readable against changing backgrounds and away from critical picture, lower thirds, and platform UI.
- Break lines by phrases and meaning, not arbitrary character counts alone.
- Identify speakers when the image or audio does not make them clear.
- Provide a transcript when required by the delivery context or useful for access, search, and review.

### Audio description

- Identify essential visual information not available in dialogue or main sound: actions, characters, scene changes, charts, and on-screen text.
- For training and explainer work, integrate critical visual description into the main narration when natural.
- For narrative and observational work, plan pauses or an alternate described mix rather than crowding dialogue.
- If all essential visual information is already spoken, separate description may not be needed; verify against the applicable standard and delivery requirement.

### Cognitive and visual legibility

- Avoid simultaneous narration, dense captions, fast graphics, and critical action.
- Give viewers time to read unfamiliar names, numbers, instructions, disclaimers, and CTAs.
- Check text size, contrast, motion, and localization at intended display size.
- Do not communicate critical distinctions through color alone.

### Flash safety

- Avoid flashing content where possible.
- For web content, W3C WCAG 2.2 Success Criterion 2.3.1 requires that content not flash more than three times in any one-second period unless it remains below defined general and red-flash thresholds.
- Analyze the final highest-dynamic-range version at the largest expected viewing scale with an appropriate flash-analysis tool when intense flashes, strobes, lightning, muzzle flashes, or rapid exposure changes exist.
- A warning is not a substitute for meeting an applicable safety threshold.

## Delivery and technical QC

Obtain the current destination specification. Confirm:

- Runtime and edit duration
- Frame size, aspect ratio, pixel aspect, and orientation
- Frame rate and scan/progressive requirements
- Codec, container, bitrate or mezzanine requirement
- Color space, transfer function, range, HDR/SDR, and legal levels
- Audio layout, channel order, sample rate, loudness, true peak, stems, and mix versions
- Caption/subtitle format, language, forced narrative, and burn-in/sidecar requirements
- Slate, bars/tone, countdown, handles, textless elements, clean versions, and naming
- Thumbnail/poster, title, metadata, and platform UI safe zones

Do not guess current platform, broadcaster, festival, or distributor specifications when they can be verified.

### Full-watch QC

Watch the exported deliverable, not only the timeline:

- Correct start/end, no unintended black, freeze, flash, or missing frames
- Sync remains stable throughout
- No offline media, placeholders, watermarks, temp score, guide narration, or disabled effects
- Titles, captions, lower thirds, credits, and disclaimers are spelled, timed, positioned, and legal
- No clipped dialogue, abrupt ambience holes, clicks, phase problems, or channel mistakes
- Graphics and reframes remain correct in every aspect-ratio variant
- Color transforms and levels match the intended display
- Flash safety and accessibility assets pass the required checks
- File name, checksum if required, version, and delivery manifest match

### Variant QC

Review each deliverable independently. Common adaptation failures include:

- Text or faces hidden by platform controls
- Captions duplicated by burned and sidecar versions
- 16:9 reframes that break 9:16 composition or vice versa
- CTA or legal copy removed by runtime trim
- Music licensed for organic use but not paid media
- Wrong mix, language, date, price, product, end card, or campaign code

## Handoff summary

At completion, report:

- Creative status and approved editorial direction
- Major unresolved choices and their audience effect
- Missing/offline/proposed material
- Factual, consent, rights, compliance, and accessibility status
- Finish work still required: VFX, grade, mix, online, captions, QC, exports
- Deliverables verified and any specs still assumed

Never call a cut "final" while a known must-fix integrity, rights, accessibility, or delivery issue remains.
