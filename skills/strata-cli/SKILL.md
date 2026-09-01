---
name: strata-cli
description: Makes motion-design videos with the strata CLI — scene JSON compiled to .idm and rendered to MP4 via Idomoo. Use for any strata / IDM / VASCO video, motion graphics, kinetic type, explainer, promo, logo sting, personalized or data-driven video; for generating images, AI video clips, reference images, talking presenters, narration and music; for brand documents, Figma-to-video, and ffmpeg edits (trim, join, reframe, grade). Not for the Idomoo cloud briefs API.
---

# Strata CLI — cinematic motion design, authored as IDM/VASCO

The job is a piece of motion design a good studio would put its name on — story-driven,
composed, with motion that has a reason. VASCO is a real 3D motion-design engine: 3D layers with
depth, a moving camera, masks, effects, per-character text animators, a keyframe tween engine.

This file is the shape of a job and the map to the rest: workflow
[workflow.md](references/workflow.md) · craft [craft.md](references/craft.md) · syntax
[format.md](references/format.md) · patterns [recipes.md](references/recipes.md) · commands
[commands.md](references/commands.md).

## Contents

- [Quick start](#quick-start)
- [What to read for this brief](#what-to-read-for-this-brief)
- [What is law and what is yours](#what-is-law-and-what-is-yours)
- [Place the piece on the range](#place-the-piece-on-the-range)
- [Before authoring — four checks](#before-authoring--four-checks)
- [The shape of a job](#the-shape-of-a-job)
- [The traps that cost a render](#the-traps-that-cost-a-render)
- [References](#references)

## Quick start

```bash
strata version                                  # installed? if not: commands.md, Setup
strata route "<the brief in a sentence>"        # prints the read list below for this brief
#   ... storyboard, sign-off, then author the scene JSON ...
strata preview scene_v1.json --at 2 --grid      # free wireframe; fix composition here
strata validate scene_v1.json                   # offline: bad keys, traps, composition tells
strata compile  scene_v1.json -o scene_v1.idm
strata render   scene_v1.json --library <id> -o scene_v1.mp4     # ask which library first
strata review   scene_v1.mp4 --scene scene_v1.json -o review/    # cuts, freezes, loudness, timecoded report
```

Renders take minutes — run them in the background. Every command takes `--json`. Exit codes:
0 ok · 1 compile/schema · 2 missing file · 3 auth · 4 render timeout.

## What to read for this brief

`strata route "<brief>"` prints this list for a brief; the list is here too, so it holds
whether or not the command was run. Paths are relative to `references/`.

**The string you pass is your reconstruction of the job, not a quote of the request.** Matching is
by keyword, so name the **kind**, the **length** and the **bar** — the words a user rarely says.
*Measured:* seven of ten realistic requests name no kind at all.

```bash
strata route "a nice video for our app"                                    # no kind — 13 files
strata route "a 30s product demo for our app, premium cinematic, music-led" # 5 kinds — 29 files
```

Same job. The second reaches the director pass, the typography and transition chapters, `beats`
and `music.md`; the first reaches none of them. When no kind matches, route says so and adds the
three files that help you choose one — that is an answer, not a failure, and the kind you then
pick is worth routing a second time.

**Every piece, before the storyboard:**
- `craft.md` — how a good video is made; the defaults and their reasons
- `traps.md` — the engine facts that produce a wrong video with a clean compile

**Any piece whose layout and motion are yours to decide** (the user did not dictate them):
- `anti-slop.md` — the defaults agent-composed frames fall into, and the test that catches them
- `layouts.md` — the named frame layouts and the grid
- `video-layouts.md` — composing *with* video rather than full-bleed clip + caption
- `motion-design.md` — the techniques that make motion read as designed, with real keys

**Then by the kind of brief** — rows add up (a personalized TV ad gets the TV-ad row and the
personalized row), and a brief can match several:

| The brief is… | Written for it |
|---|---|
| a **TV / broadcast ad, promo, launch, sale, hero film** | `editing-director.md`, `editorial/short-form-performance.md`, `editorial/editing-grammar.md`, `blueprints.md`, `video-styles.md`, `video-prompts.md`, `motion/04-transitions.md`, `motion/07-narrative-sound-emotion.md`, `music.md` |
| **social / vertical / UGC / trailer-style** | `editing-director.md`, `editorial/short-form-performance.md`, `blueprints.md`, `video-styles.md`, `motion/04-transitions.md`, `music.md` |
| an **explainer, tutorial, testimonial, internal comms, recap, sports** | `editing-director.md`, `editorial/nonfiction-formats.md`, `editorial/editing-grammar.md`, `blueprints.md`, `motion/07-narrative-sound-emotion.md` |
| **personalized / data-driven / a chart or a stat** | `personalization.md`, `motion/06-ui-data-brand.md`, `editorial/nonfiction-formats.md` (data is a claim) |
| a **presenter, spokesperson, talking head, dialogue** | `avatar.md`, `video-generation-advanced.md`, `production-bible.md`, `captions.md` |
| **supplied material** (storyboard, script, PDF, URL, sheets, footage, voice) | `intake.md` first, then `production-bible.md`, `video-generation.md`, `video-generation-advanced.md` |
| a **logo sting, brand ident, product hero** | `blueprints.md`, `video-styles.md`, `motion/05-shape-effects-texture.md`, `motion/06-ui-data-brand.md`, `recipes.md` |
| **kinetic type / typographic / captions-heavy** | `motion/03-typography.md`, `recipes.md`, `layouts.md` |
| **music-led / beat-synced / audio-reactive** | `generative-fx.md`, `music.md`, `motion/07-narrative-sound-emotion.md`, `motion/04-transitions.md` |
| a **genre feel** (thriller, comedy, trailer, documentary) | `editorial/narrative-genres.md`, `reference-styles.md`, `motion/08-styles-index.md` |
| **"make it feel like X"** (a film, director, campaign) | `reference-styles.md`, `motion/08-styles-index.md`; `strata deconstruct` on any supplied clip |
| **premium / AAA / cinematic / broadcast quality** (in addition to the kind above) | `director.md`, `motion/01-foundations.md`, `motion/02-choreography-space-camera.md`, `motion/03-typography.md`, `motion/09-production-qa.md` |
| **any generated video clip** | `video-generation.md`, `video-prompts.md`; consistent cast → `production-bible.md` |
| **an edit** of supplied footage (cut, join, reframe, retime) | `video-editing.md`, `editorial/workflow-and-qa.md` |
| **brand material** supplied, "on brand", a series | `brand.md` |
| **Figma** | `figma.md` |

**After the first render, every piece:** `review.md`, `motion/09-production-qa.md`,
`editorial/workflow-and-qa.md`.

Read the list again once the concept exists: a short brief cannot say "kinetic type" or
"music-led", but the direction chosen for it can, and that adds rows.

## What is law and what is yours

Two kinds of rule live in this skill, and they are not equal.

**The engine facts are law.** The traps below, the API invariants in the generation references, the
syntax in format.md — each one exists because a render came back wrong with a clean compile, and
the measurement is written next to it. Follow them exactly; they are not taste.

**Everything else is a default, and you are expected to have a better idea.** The craft references
describe what produces good work when nobody has a stronger one — read time, transition families,
one atmospheric effect, two typefaces. Every one of them is written for the *middle* of the range
below and scales with the position the piece declares; they are the floor, not the ceiling. Where
the brief leaves an axis free, make the choice a strong director would make *for this subject*,
and break any craft default when you can say why in one line on the storyboard. The failure this
skill exists to prevent is the generic piece — the frame that would fit any brief — and a rule
followed without a reason produces that as reliably as no rule at all. The brief's own words
always win over both.

## Place the piece on the range

World-class work is not one tempo. `strata deconstruct` on reference video:

| Reference | Shots | Mean shot | Energy | Stillness | Loudness |
|---|---|---|---|---|---|
| showreel, 10 s | 10 | 1.00 s | 1.49 | 0.46 | −9.0 LUFS |
| film, quiet | 5 | 6.00 s | 1.06 | 0.57 | −28.0 LUFS |
| film, action | 12 | 2.50 s | 11.43 | 0.00 | −14.0 LUFS |

Energy spans 10×, mean shot length 6×, stillness 0.57 to nothing. The middle of that spread is a
position to take on purpose; it is also where a piece lands when nobody takes one — our last three
measured 2.31 / 10.00 / 2.50 s mean shot, energy 3.25 / 1.31 / 2.61 and −16.9 / −18.7 / −16.8 LUFS:
three briefs, one band on every axis.

**A mean is itself a middle.** The same spread runs *inside* a piece, and an average is how a piece
ends up cutting everything at that average. Ten seconds of each:

| Ten seconds of | Shots | Shortest → longest | Longest ÷ shortest | Rhythm regularity |
|---|---|---|---|---|
| reference showreel | 10 | 0.08 s → 3.80 s | 47.5:1 | 0.03 |
| reference film | 12 | — | 3.1:1 | 0.65 |
| ours, told to average 0.8 s | 12 | 0.32 s → 2.16 s | 6.8:1 | 0.37 |

A two-frame flash and a four-second hold in the same ten seconds; ours had one speed — four hero
layers, no hero.

So **name four numbers in the storyboard, above the shot list, before any shot exists**: the
**shot-length range** — the shortest cut and the longest hold, **and the job that earns each** —
plus motion energy, stillness ratio and loudness (an integrated LUFS target for where the piece will
actually play, plus the gap between its loudest and quietest beat,
[music.md](references/music.md)). Name the shot that will be longest and what it carries; name the
shortest and what those are — a transit, an impact, a beat, an item the viewer only has to register.
A hold is time the viewer is allowed to look at the thing the piece is about, a flash is time the
piece refuses to give, and shots with neither job are the middle and should be the *fewest*.

**A hold is a shot the camera stays on, not a shot that stops.** The reference film's long takes —
4.04 s and 5.96 s — measure stillness **0.16**; its showreel 0.46; a locked logotype that kept a
fill scrolling inside the letters, 0.53. Ours, told to hold the payoff, came back at **0.88** —
4.2 s whose only change was a light gradient decaying to a dead frame — and a blind critic said it
*"plays its only card at 02:12 and has 7.8 s with nothing left to say."* It lost to the version it
was meant to beat. Frame by frame the gap is starker: **the showreel's longest hold has ZERO frames
whose frame-to-frame difference falls below 0.5** (mean 2.99 across the hold), and the film
reference the same — a world-class long take is a slow move that lasts, not a static frame that
lasts. Our 30 s ad: 133 of 138 frames on the hero shot and 95 of 98 on the end card below 0.5,
**9.9 of its last 12.2 s effectively a still**, motion by thirds 4.16 / 6.90 / 0.71. The cheapest
thing that makes a hold behave like the reference is a **continuous slow push — 2–3 % across the
whole length of the hold** — and **an end card is a hold like any other**: the last shot, the frame
the viewer leaves on, the place agents stop trying. So every declared hold, the end card included,
names beside its length **what keeps moving through it**: per-frame animation on the elements, a
slow camera push, a fill animating under a mask or matte while the shapes stay put, a grade or
light shifting across a surface, secondary motion still settling after the primary landed, or
generated footage that is itself moving. A hold that names nothing is a freeze, and the fix is to
give the shot something to do, never to shorten it — the range and the frozen frame are two
failures and both must clear.

Say which row it sits near and what in *this* brief puts it there, then build to it: the
shot list is beats with a length each, not a runtime divided by an average. **The range follows the
material, and the ratio is a symptom, not a target** — one continuous idea earns a narrow range and
says so, transits and impacts earn a wide one, both reference rows are world-class, and a ratio
reached by padding one shot and clipping another is one uniform cut with two outliers glued on.
`strata review` compares the render against the declared numbers exactly as it does against a
supplied reference ([review.md](references/review.md)), reads `shortest`, `longest` and
`rhythmRegularity` against the declared range, and checks the longest shot is the one you said
would carry the idea. Landing in the middle having declared an extreme — or on one speed having
declared a range — is a must-fix, not a taste difference.

## Before authoring — four checks

Each one changes what the job is. Details and the traps of each: [workflow.md](references/workflow.md).

- **Material supplied?** (storyboard, script, PDF, URL, sheets, footage, a voice) →
  [intake.md](references/intake.md) first; the material *is* the brief, and a supplied storyboard is
  executed, not redesigned. Recurring person, product or set → the
  [production bible](references/production-bible.md) before the first clip.
- **An edit, not a design job?** Cut, trim, join, reframe, retime, mute → ffmpeg per
  [video-editing.md](references/video-editing.md), hand back an MP4.
- **Brand first.** `.brand/brand.md` present → author everything from it. Brand material supplied
  but no document → offer to build one and get sign-off ([brand.md](references/brand.md)).
- **From Figma?** [figma.md](references/figma.md) first; an imported frame is a layout, not a video.

## The shape of a job

Assets → storyboard with sign-off → scene JSON → preview → validate + compile → render → review
the MP4. Every step in full, with the commands and the measurements behind it:
[workflow.md](references/workflow.md). The points that decide whether the piece is good:

1. **Assets.** Every clip at least as long as its slot plus ~1 s (a short clip freezes on its last
   frame); generate in waves in the background; anything composited *over* another layer is a
   `.jet`, full-frame plates stay MP4; every image becomes a video unless it is an icon or logo.
2. **Storyboard, then sign-off, before any scene JSON.** The four numbers above, a direction the
   user has seen, a shot list built to the declared range, a layout sketch of the key frames, the
   end frame, what stays still, and where the light comes from. Two or three layout options when
   the framing could go more than one way — people recognise the layout they want faster than they
   can describe it. Unattended: decide in the user's place, record it in `decisions.md`, continue.
   Premium work gets the direction pass in [director.md](references/director.md).
3. **Scene JSON.** Written with the file-writing tool (a heredoc breaks on the first apostrophe),
   to a named layout on the grid, from recipes and blocks rather than from scratch, unique name on
   every layer. Block first, then primary motion, then secondary, then finishing — decoration
   creeps in when the order is mixed.
4. **Preview** with `--grid` at every key frame; this is where composition is fixed. Preview draws
   boxes, not glyphs — a `snapshot --at <sec>` proves the type.
5. **Validate**, read every `⚠` line, then compile. A new filename for every new cut
   (`promo_v1` → `promo_v2`): the filename is what the user sees in their library.
6. **Render** into a library the user chose — ask, never pick.
7. **Review the MP4**, not the poster: `strata review`, then fix every must-fix it names. A piece
   ships when nothing is left that a viewer would notice, not when it compiles.

## The traps that cost a render

Each of these produces a *wrong video* rather than an error. The full text, with the measurement
behind each, is [traps.md](references/traps.md) — read it before the first compile of any piece.

- **If anyone speaks in a clip, that clip's audio is the voice** — a TTS of the same line laid on
  top gives two voices out of sync (*measured:* lines land 1.3 / 3.8 / 6.3 s in, so no offset
  repairs it). A TTS file is an input to the generation **or** a layer in the scene, never both.
- **A talking clip is two references and a job line:** `generate video --ref-image <portrait>
  --ref-audio <tts url> --audio`; in the prompt, `USE [Audio 1] AS THE CHARACTER'S VOICE FOR THE
  ENTIRE VIDEO.`, demand the lip-sync, put the line in `{curly braces}` byte-identical to the TTS,
  hold a close-up on the mouth. A bare `--ref-audio` is refused by the server.
- **References are cited by index, with a job.** `--ref-image` order = `[Image 1]`, `[Image 2]`…
  (`generate image --reference` counts from `image 0`); each gets a JOB line, the identity block is
  restated ×3, ≤4 references, and frames and references never mix (server 422).
- **A clip shorter than its slot freezes** on its last frame, and `validate` cannot see it —
  generate longer and trim; a short clip is covered with more shots, never stretched.
- **Editing an AI clip with audio: the first `-map` silently drops the track** — pair every
  `-map "[v]"` with `-map 0:a`, then `ffprobe` the output.
- **`position` is a delta from the box** (no anchor) — repeating the box's x/y in it doubles the
  offset; with an anchor it is the absolute pivot point. `validate` warns on the tell-tale.
- **Overlay video needs alpha → `.jet`, never `.mp4`**; the plate and its matted overlay are the
  same clip; the `.jet` fps must match the scene; any video input needs ffmpeg installed.
- **Personalised data visuals must be real image files** — or `strata chart` layers, whose value
  labels personalise while heights are baked (per-viewer heights = one scene per row).
- **Layer names unique across the whole scene** — the exporter keys by name globally; the compiler
  auto-renames duplicates, which silently changes the personalization key.
- **Fonts must cover every glyph** (`strata glyphs`) — a missing glyph is tofu or a cloud-render
  crash. Hebrew/Arabic already lay out correctly; `rtl` is a no-op and never the fix.
- **Camera is a measured pinhole** — park it at `z = −focal` (−935 for 1080p at fov 60); negative z
  comes toward the camera; anchor is 2D, depth goes on `position` z. An animated camera needs
  `"motion_blur": true` written on it — the schema default is `false`, so a push renders crisp.
- **Misc:** comp max 1920/axis · motion blur stays on for moving layers · keyframe times are
  relative to the layer's `start` · a sub-comp is declared before the comp that uses it ·
  `validate` before every render.

## References

**A question mid-job?** [INDEX.md](references/INDEX.md) is one line per file saying what it *owns*
and the questions it answers, written in the words a question arrives in —
`grep -i "green screen" INDEX.md` returns the row and the filename together. Use it when routing
has already run and you need one specific answer; the table below is the same library by subject.
*(Measured: ranking these files by how often a phrase appears finds the owning file first only 3
times in 12 — several files discuss a topic, one settles it, and match count cannot tell them
apart.)*

Every file below ships with this skill; each is one hop from here. The craft files are read for
what they know; the engine files are read for what they forbid.

**Working the job**

| File | What it holds |
|---|---|
| [workflow.md](references/workflow.md) | the seven steps in full, each with the measurement behind it, and the Definition of Done |
| [craft.md](references/craft.md) | the craft — story and shots, composition, 3D and camera, kinetic type, transitions, atmosphere, rhythm and sound |
| [director.md](references/director.md) | the creative-direction pass for premium work — thesis, six traits, directions that differ in kind, styleframes, the still list, the motion bible |
| [anti-slop.md](references/anti-slop.md) | the defaults agent work falls into regardless of subject, the per-element test, the tells `validate` checks |
| [layouts.md](references/layouts.md) | the named frame layouts and the grid; handing the layout to the user in `strata studio` |
| [video-layouts.md](references/video-layouts.md) | composing *with* video — footage in shapes, type-as-window, split-screen, PiP, cut-outs, occlusion builds |
| [motion-design.md](references/motion-design.md) | the ten techniques with real keys; emotion → motion and material → easing tables |
| [editing-director.md](references/editing-director.md) | the editorial layer — viewer promise, information release, the cut-decision gate; routes into `editorial/` |
| [review.md](references/review.md) | the critic pass on the rendered MP4 — the viewings, the nine categories, the known false positives |
| [blueprints.md](references/blueprints.md) | whole-video structures by type, rescaled to the declared shot-length range |
| [video-styles.md](references/video-styles.md) | 50 kinds of video with their shot grammar and tells; technique only — a brand overrides every style |
| [reference-styles.md](references/reference-styles.md) | "make it feel like X" — ~25 films, directors and campaigns as measurable signatures; a technique source, never a costume |
| [motion-atlas.md](references/motion-atlas.md) | ~700 named motion concepts; where it and motion-design.md disagree on a number, motion-design.md wins |

**Making assets**

| File | What it holds |
|---|---|
| [assets.md](references/assets.md) | generating media — images, image-to-video, narration, music, avatars, `.jet` and matte, upload, waves |
| [video-generation.md](references/video-generation.md) | prompting `generate video` — the five modes, the model table, the frames-vs-references rule, clip length |
| [video-generation-advanced.md](references/video-generation-advanced.md) | reference images, reference video for a camera plan, reference audio and dialogue, sketch animatics |
| [video-prompts.md](references/video-prompts.md) | the cookbook — a worked, measured prompt per kind of clip; keep the skeleton, swap the subject |
| [production-bible.md](references/production-bible.md) | consistency across clips — one sheet per principal, the identity block ×3, per-clip verification |
| [avatar.md](references/avatar.md) | a talking presenter from a still and audio — the route decision, then the mechanics |
| [music.md](references/music.md) | the mix decision — the LUFS target for where it plays and the range inside the piece; then prompting `generate music` — the tag string, the arc, ducking, SFX |
| [captions.md](references/captions.md) | `strata captions` — speech timing from audio or video as `{t0,t1,text}` cues (segment-level, not per-word) |
| [generative-fx.md](references/generative-fx.md) | beat-syncing with `strata beats`, generator scripts, audio-reactive graphics |
| [intake.md](references/intake.md) | reading supplied material into a shot table and executing it |
| [video-editing.md](references/video-editing.md) | ffmpeg edits — trim, concat, reframe, speed, audio strip/replace, fades, loops; keeping the audio track |
| [brand.md](references/brand.md) | capturing a visual identity as `.brand/brand.md` and authoring from it |
| [figma.md](references/figma.md) | importing a Figma design — scale and origin math, node → layer map, fonts as paths, unique names |

**The engine**

| File | What it holds |
|---|---|
| [format.md](references/format.md) | the scene format — every layer key, 3D and camera, effects and the four layer styles, masks, track mattes, sub-comps, rich text, versioning |
| [recipes.md](references/recipes.md) | engine-correct patterns to start from — kinetic text, transitions, masks, FX, 3D/camera, data-viz, track and corner-pin |
| [blocks.md](references/blocks.md) | reusable sub-comp blocks via `strata add` — lower-third, stat-card, end-card, logo-sting, device-frame, quote-card |
| [traps.md](references/traps.md) | the engine traps in full with the measurement behind each |
| [commands.md](references/commands.md) | the CLI — setup, every command in one line, libraries, flags, exit codes; every command answers `--help` |
| [personalization.md](references/personalization.md) | one template → many videos — placeholder naming, `--emit-timeline` for the contract, `render --data rows.json` for the batch, charts per viewer |
| [tagging.md](references/tagging.md) | the `--tags` manifest for reusable scenes — two closed vocabularies; only when the user asks |

**The chapters** — `motion/` is the animation textbook, `editorial/` is the editing one. Routed by
brief kind; read a chapter when its question is in front of you.

| File | What it holds |
|---|---|
| [motion/01-foundations.md](references/motion/01-foundations.md) | principles, timing and spacing, easing families, physics and weight, rhythm |
| [motion/02-choreography-space-camera.md](references/motion/02-choreography-space-camera.md) | staging, focal control, depth, camera language |
| [motion/03-typography.md](references/motion/03-typography.md) | type entrances and exits, numbers, multilingual and RTL |
| [motion/04-transitions.md](references/motion/04-transitions.md) | cuts, dissolves, wipes, motion-driven and textural transitions |
| [motion/05-shape-effects-texture.md](references/motion/05-shape-effects-texture.md) | shape, masks and mattes, optical effects, particles, light, materials |
| [motion/06-ui-data-brand.md](references/motion/06-ui-data-brand.md) | UI and product motion, data motion (honesty rules), brand motion systems |
| [motion/07-narrative-sound-emotion.md](references/motion/07-narrative-sound-emotion.md) | story structure, pacing, sound and music, emotion → motion parameters |
| [motion/08-styles-index.md](references/motion/08-styles-index.md) | the 77-entry style index |
| [motion/09-production-qa.md](references/motion/09-production-qa.md) | formats, accessibility and safety, the QA list, default numbers |
| [editorial/editing-grammar.md](references/editorial/editing-grammar.md) | the six control dimensions of pace, editing languages, cut vocabulary, pacing diagnosis |
| [editorial/short-form-performance.md](references/editorial/short-form-performance.md) | promos, ads, trailers, social — hooks, CTA dwell, failure modes |
| [editorial/nonfiction-formats.md](references/editorial/nonfiction-formats.md) | explainers, testimonials, corporate, sports; factual integrity |
| [editorial/narrative-genres.md](references/editorial/narrative-genres.md) | genre as a modifier — thriller release, trailer escalation, comedy timing, documentary restraint |
| [editorial/workflow-and-qa.md](references/editorial/workflow-and-qa.md) | the editorial workflow, captions, flashing, rights, review passes |
| [editorial/research-sources.md](references/editorial/research-sources.md) | where the editorial rules come from; a measured engine fact beats any of them |
