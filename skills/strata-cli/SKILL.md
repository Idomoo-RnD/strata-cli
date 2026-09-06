---
name: strata-cli
description: Makes motion-design videos with the strata CLI — scene JSON compiled to .idm and rendered to MP4 via Idomoo. Use for any strata / IDM / VASCO video, motion graphics, kinetic type, explainer, promo, logo sting, personalized or data-driven video; for generating images, AI video clips, reference images, talking presenters, narration and music; for brand documents, Figma-to-video, and ffmpeg edits (trim, join, reframe, grade). Not for the Idomoo cloud briefs API.
---

# Strata CLI — cinematic motion design, authored as IDM/VASCO

The job is a piece of motion design a good studio would put its name on — story-driven,
composed, with motion that has a reason. VASCO is a real 3D motion-design engine: 3D layers with
depth, a moving camera, masks, effects, per-character text animators, a keyframe tween engine.

## Quick start

```bash
strata brief init "<the request, verbatim>"     # BRIEF.md — the artifact every step reads; prefs fill it
strata route BRIEF.md                           # the read list below; each kind opens with routes/<kind>.md
#   ... storyboard, sign-off, then author the scene JSON ...
strata preview scene_v1.json --at 2 --grid      # free wireframe; fix composition here
strata validate scene_v1.json                   # offline: bad keys, traps, composition tells
strata compile  scene_v1.json -o scene_v1.idm
strata render   scene_v1.json --library <id> -o scene_v1.mp4     # ask which library first
strata review   scene_v1.mp4 --scene scene_v1.json -o review/    # cuts, freezes, loudness, timecoded
```

Renders take minutes — background them. Every command takes `--json` and `--help`.

## What to read for this brief

`strata route BRIEF.md` (or `strata route "<brief>"`) prints this list; it is here too, so it holds
without the command. Paths are relative to `references/`. **Routing matches the brief's reconstruction line, not the user's
words** — matching is by keyword, so that line names the **kind**, the **length** and the **bar**,
the words a user rarely says (*measured:* seven of ten requests name no kind).

<!-- route-table:begin — generated from src/route.mjs by scripts/sync-route-table.mjs; edit route.mjs, not this -->
**Every piece, before the storyboard:**
- `workflow.md` — the seven steps, the Definition of Done, the unattended-run rules
- `craft.md` — how a good video is made; the defaults and their reasons
- `traps.md` — the engine facts that produce a wrong video with a clean compile
- `format.md` — the scene syntax — every key the engine accepts; open before the storyboard
- `commands.md` — the CLI — every command, its flags and exit codes

**Any piece whose layout and motion are yours to decide:**
`anti-slop.md` · `layouts.md` · `video-layouts.md` · `motion-design.md` — what makes a frame designed rather than assembled.

**Then by the kind of brief** — a menu, not a reading list; rows add up. The first file in a row is
the kind's route page: read it, then what it points at.

| The brief is… | Written for it |
|---|---|
| **supplied material (storyboard, script, PDF, URL, sheets, footage, voice)** | `routes/supplied-material.md`, `intake.md`, `production-bible.md`, `video-generation.md`, `video-generation-advanced.md`; `intake.md` first |
| **a TV / broadcast ad, promo, launch, sale, hero film** | `routes/tv-ad.md`, `editing-director.md`, `editorial/short-form-performance.md`, `editorial/editing-grammar.md`, `blueprints.md`, `video-styles.md`, `video-prompts.md`, `assets.md`, `motion/04-transitions.md`, `motion/07-narrative-sound-emotion.md`, `music.md` |
| **social / vertical / UGC / trailer-style** | `routes/social.md`, `editing-director.md`, `editorial/short-form-performance.md`, `blueprints.md`, `video-styles.md`, `motion/04-transitions.md`, `music.md` |
| **an explainer, tutorial, testimonial, internal comms, recap, sports** | `routes/explainer.md`, `editing-director.md`, `editorial/nonfiction-formats.md`, `editorial/editing-grammar.md`, `blueprints.md`, `motion/07-narrative-sound-emotion.md` |
| **personalized / data-driven / a chart or a stat** | `routes/personalized.md`, `personalization.md`, `motion/06-ui-data-brand.md`, `editorial/nonfiction-formats.md`; data is a claim |
| **a presenter, spokesperson, talking head, dialogue** | `routes/presenter.md`, `avatar.md`, `video-generation-advanced.md`, `production-bible.md`, `captions.md` |
| **a logo sting, brand ident, product hero** | `routes/logo-ident.md`, `blueprints.md`, `video-styles.md`, `assets.md`, `motion/05-shape-effects-texture.md`, `motion/06-ui-data-brand.md`, `recipes.md` |
| **kinetic type / typographic / captions-heavy** | `routes/kinetic-type.md`, `motion/03-typography.md`, `recipes.md`, `layouts.md` |
| **music-led / beat-synced / audio-reactive** | `routes/music-led.md`, `generative-fx.md`, `music.md`, `motion/07-narrative-sound-emotion.md`, `motion/04-transitions.md` |
| **a genre feel (thriller, comedy, trailer, documentary)** | `routes/genre.md`, `editorial/narrative-genres.md`, `reference-styles.md`, `motion/08-styles-index.md` |
| **"make it feel like X" (a film, director, campaign)** | `routes/feel-like.md`, `reference-styles.md`, `motion/08-styles-index.md`; `strata deconstruct` on any supplied clip |
| **premium / AAA / cinematic / broadcast quality (in addition to the kind above)** | `routes/premium.md`, `director.md`, `motion/01-foundations.md`, `motion/02-choreography-space-camera.md`, `motion/03-typography.md`, `motion/09-production-qa.md` |
| **any generated video clip** | `routes/generated-clips.md`, `video-generation.md`, `video-prompts.md`; consistent cast → `production-bible.md` |
| **an edit of supplied footage (cut, join, reframe, retime)** | `routes/footage-edit.md`, `video-editing.md`, `editorial/workflow-and-qa.md` |
| **brand material supplied, "on brand", a series** | `routes/brand.md`, `brand.md` |
| **Figma** | `routes/figma.md`, `figma.md` |

**No kind matched?** route adds `blueprints.md`, `video-styles.md`, `editing-director.md` — the files that help you choose one; then route again.

**After the first render, every piece:** `review.md`, `motion/09-production-qa.md`, `editorial/workflow-and-qa.md`.
<!-- route-table:end -->

## What is law and what is yours

**The engine facts are law.** The traps below, the API invariants in the generation references, the
syntax in format.md — each exists because a render came back wrong with a clean compile, and the
measurement is written next to it. Follow them exactly; they are not taste.

**Everything else is a default, and you are expected to have a better idea.** The craft references
describe what produces good work when nobody has a stronger one — read time, transitions, two
typefaces. Each is written for the *middle* of the range below and
scales with the position the piece declares: the floor, not the ceiling. Where the brief leaves an
axis free, make the choice a strong director would make *for this subject*, and break any craft
default when you can say why in one line on the storyboard. The failure this skill exists to
prevent is the generic piece — the frame that would fit any brief — and a rule followed without a
reason produces it as reliably as no rule at all. The brief's own words win over both.

## Place the piece on the range

World-class work is not one tempo. `strata deconstruct` on reference video:

| Reference | Shots | Mean shot | Energy | Stillness | Loudness |
|---|---|---|---|---|---|
| showreel, 10 s | 10 | 1.00 s | 1.49 | 0.46 | −9.0 LUFS |
| film, quiet | 5 | 6.00 s | 1.06 | 0.57 | −28.0 LUFS |
| film, action | 12 | 2.50 s | 11.43 | 0.00 | −14.0 LUFS |

Energy and stillness are `deconstruct`'s numbers (defined in review.md §7) — declare them from a
measurement, never by feel. Energy spans 10×, mean shot length 6×, stillness 0.57 to nothing. The middle is a position to take
on purpose — and where a piece lands when nobody takes one: unrelated briefs coming back in one
narrow band on every axis (mean shot 2.3–2.5 s, energy 2.6–3.3, −17 to −19 LUFS).

**A mean is itself a middle.** The same spread runs *inside* a piece. Ten seconds of the showreel:
10 shots from 0.08 s to 3.80 s (47.5:1, rhythm regularity 0.03); of the film: 12 shots, 3.1:1,
regularity 0.65; a piece told to average 0.8 s: 12 shots, 0.32 s → 2.16 s, 6.8:1, 0.37. A
two-frame flash and a four-second hold in the same ten seconds; cutting at the mean is the same
failure one level down — four hero layers, no hero.

So **name four numbers in the storyboard, above the shot list, before any shot exists**: the
**shot-length range** — the shortest cut and the longest hold, **and the job that earns each** —
plus motion energy, stillness ratio and loudness (an integrated LUFS target for where the piece will
actually play, plus the gap between its loudest and quietest beat,
[music.md](references/music.md)). Name the longest shot and what it carries; name the shortest and
what those are — a transit, an impact, a beat, an item the viewer only has to register. A hold is
time the viewer is allowed to look at the thing the piece is about, a flash is time the piece
refuses to give; shots with neither job are the middle and should be the *fewest*.

**A hold is a shot the camera stays on, not a shot that stops.** The reference film's long takes
(4.04 s and 5.96 s) measure stillness **0.16**, the showreel 0.46, a locked logotype with a fill
scrolling inside the letters 0.53; a hold given nothing to do measures **0.88** — 4.2 s whose only
change is a gradient decaying to a dead frame. Frame by frame: **the showreel's longest hold has
ZERO frames whose frame-to-frame difference falls below 0.5** (mean 2.99), the film the same; a
failed hold has most of its frames below 0.5 (the numbers: [craft.md](references/craft.md)). A
world-class long take is a slow move that lasts, not a static frame that lasts. The cheapest fix is
a **continuous slow push, sized in pixels per frame rather than percent** (below); **an end card
is a hold like any other** — the frame the viewer leaves on. So every
declared hold, the end card included, names beside its length **what keeps moving through it**:
per-frame animation on the elements, a slow camera push, a fill animating under a mask or matte, a
grade or light shifting across a surface, secondary motion still settling, or generated footage
that is itself moving. A hold that names nothing is a freeze, and the fix is to give the shot
something to do, never to shorten it — the range and the frozen frame are two failures and both
must clear.

Say which row it sits near and what in *this* brief puts it there, then build to it: the shot list
is beats with a length each, not a runtime divided by an average. **The range follows the material
and the ratio is a symptom, not a target** — one continuous idea earns a narrow range, transits and
impacts a wide one; a ratio reached by padding one shot and clipping another is one uniform cut
with two outliers glued on. `strata review` reads `shortest`, `longest` and `rhythmRegularity`
against the declared range ([review.md](references/review.md)) and checks the longest shot is the
one you said would carry the idea. Landing in the middle having declared an extreme is a must-fix.

## Before authoring — four checks

Each changes what the job is; the traps of each: [workflow.md](references/workflow.md).

- **Material supplied?** (storyboard, script, PDF, URL, sheets, footage, a voice) →
  [intake.md](references/intake.md) first: the material *is* the brief, and a supplied storyboard
  is executed, not redesigned. A recurring person, product or set → the
  [production bible](references/production-bible.md) before the first clip.
- **An edit, not a design job?** Cut, trim, join, reframe, retime, mute → ffmpeg per
  [video-editing.md](references/video-editing.md); hand back an MP4.
- **Brand first.** `.brand/brand.md` present → author from it. Brand material but no document →
  offer to build one and get sign-off ([brand.md](references/brand.md)).
- **From Figma?** [figma.md](references/figma.md) first; an imported frame is a layout, not a video.

## The shape of a job

Assets → storyboard with sign-off → scene JSON → preview → validate + compile → render → review
the MP4 — every step in full in [workflow.md](references/workflow.md). What decides whether the
piece is good:

1. **Assets.** Every clip at least as long as its slot plus ~1 s; generate in waves in the
   background; anything composited *over* another layer is a `.jet`, full-frame plates stay MP4;
   every image becomes a video unless it is an icon, logo or keyed cut-out. **A device, prop,
   product, icon or texture is a `generate image` asset; shapes are for simple geometry**
   (assets.md, *Drawn or generated?*).
2. **Storyboard, then sign-off, before any scene JSON.** `BRIEF.md` filled first (`strata prefs`
   holds answers the user gave before; a series starts from `strata recipe`). Then the four numbers
   above, a direction the user has seen, a shot list built to the declared range, a layout sketch
   of the key frames, the end frame, what stays still, where the light comes from. Two or three
   layout options when the framing could go either way. Unattended: decide in the user's place,
   record it in `decisions.md`, continue. Premium work gets the direction pass in
   [director.md](references/director.md).
3. **Scene JSON.** Written with the file-writing tool (a heredoc breaks on the first apostrophe),
   to a named layout on the grid, from recipes and blocks rather than from scratch, unique name on
   every layer. Block, then primary motion, then secondary, then finishing — decoration creeps in
   when the order is mixed.
4. **Preview** with `--grid` at every key frame; composition is fixed here. It draws boxes, not
   glyphs or masks, and only the comp named with `--comp` — `snapshot --at <sec>` proves the frame.
5. **Validate**, read every `⚠` line, then compile. A new filename for every new cut
   (`promo_v1` → `promo_v2`) — it is what the user sees in their library.
6. **Render** into a library the user chose — ask, never pick.
7. **Review the MP4**, not the poster: `strata review`, then fix every must-fix it names in one
   pass. **Two full renders per piece** — the first to review, the second to ship; a third means
   the plan was wrong (workflow.md). A piece ships when nothing a viewer would notice
   is left, not when it compiles.

## The traps that cost a render

Each of these produces a *wrong video* rather than an error. The full text, with the measurement
behind each, is [traps.md](references/traps.md) — read it before the first compile of any piece.

- **If anyone speaks in a clip, that clip's audio is the voice** — a TTS of the same line laid on
  top is two voices out of sync (*measured:* 1.3 / 3.8 / 6.3 s in; no offset repairs it). A TTS
  file is an input to the generation **or** a layer in the scene, never both.
- **A talking clip is two references and a job line** — `generate video --ref-image <portrait>
  --ref-audio <tts url> --audio`; in the prompt, `USE [Audio 1] AS THE CHARACTER'S VOICE FOR THE
  ENTIRE VIDEO.`, demand the lip-sync, put the line in `{curly braces}` byte-identical to the TTS,
  hold a close-up on the mouth. A bare `--ref-audio` is refused.
- **References are cited by index, with a job** — `--ref-image` order = `[Image 1]`, `[Image 2]`…
  (`generate image --reference` counts from `image 0`); identity block ×3; ≤9 images (≤4 shots per
  12 s with references); frames and references never mix (422).
- **A clip shorter than its slot freezes** on its last frame and `validate` cannot see it —
  generate longer and trim; cover with more shots, never stretch.
- **The first `-map` silently drops an AI clip's audio** — pair `-map "[v]"` with `-map 0:a`;
  `ffprobe` the output.
- **`position` is a delta from the box** (no anchor); with an anchor it is the absolute pivot.
- **Overlay video is a `.jet`, never `.mp4`**; plate and matted overlay are the same clip; the
  `.jet` fps matches the scene; any video input needs ffmpeg.
- **Personalised data visuals are `strata chart` layers or real image files** — `chart bars`
  heights grow from `--data`; `chart donut`'s sweep is baked (its value text personalises); other
  chart types are swapped image files.
- **Layer names unique across the whole scene** — the exporter keys by name globally; auto-renamed
  duplicates silently change the personalization key.
- **Fonts must cover every glyph** (`strata glyphs`) — tofu or a cloud-render crash otherwise.
  Hebrew/Arabic lay out correctly already; `rtl` is a no-op.
- **Camera is a measured pinhole** — park it at `z = −focal` (−935 for 1080p at fov 60); negative z
  comes toward the camera; depth goes on `position` z; an animated camera needs
  `"motion_blur": true` (default `false`, so a push renders crisp).
- **Misc:** comp max 1920/axis · motion blur on for moving layers · keyframe times relative to the
  layer's `start` · a sub-comp is declared before the comp that uses it · generated clips are
  24 fps, the scene default 25.

## References

**A question mid-job?** [INDEX.md](references/INDEX.md) is one line per file — what it *owns*, and
the questions it answers, in the words a question arrives in — so `grep -i "green screen" INDEX.md`
returns the row and the filename together. Every file below ships with this skill, one hop from
here; the craft files are read for what they know, the engine files for what they forbid.

**Working the job** — [workflow](references/workflow.md) · [craft](references/craft.md) ·
[director](references/director.md) · [anti-slop](references/anti-slop.md) ·
[layouts](references/layouts.md) · [video-layouts](references/video-layouts.md) ·
[motion-design](references/motion-design.md) · [editing-director](references/editing-director.md) ·
[review](references/review.md) · [blueprints](references/blueprints.md) ·
[video-styles](references/video-styles.md) (50 kinds) ·
[reference-styles](references/reference-styles.md) (~25 films, directors, campaigns) ·
[motion-atlas](references/motion-atlas.md) (~700 concepts; on a number, motion-design.md wins)

**Making assets** — [assets](references/assets.md) · [video-generation](references/video-generation.md) ·
[video-generation-advanced](references/video-generation-advanced.md) ·
[video-prompts](references/video-prompts.md) · [production-bible](references/production-bible.md) ·
[avatar](references/avatar.md) · [music](references/music.md) · [captions](references/captions.md) ·
[generative-fx](references/generative-fx.md) ·
[intake](references/intake.md) · [video-editing](references/video-editing.md) ·
[brand](references/brand.md) · [figma](references/figma.md)

**The engine** — [format](references/format.md) · [recipes](references/recipes.md) ·
[blocks](references/blocks.md) · [traps](references/traps.md) · [commands](references/commands.md) ·
[personalization](references/personalization.md) · [tagging](references/tagging.md)

**Chapters** — motion/ [01](references/motion/01-foundations.md) foundations ·
[02](references/motion/02-choreography-space-camera.md) choreography, space, camera ·
[03](references/motion/03-typography.md) typography · [04](references/motion/04-transitions.md) transitions ·
[05](references/motion/05-shape-effects-texture.md) shape, effects, texture ·
[06](references/motion/06-ui-data-brand.md) UI, data, brand ·
[07](references/motion/07-narrative-sound-emotion.md) narrative, sound, emotion ·
[08](references/motion/08-styles-index.md) styles index (77 entries) ·
[09](references/motion/09-production-qa.md) production QA — editorial/
[grammar](references/editorial/editing-grammar.md) · [short-form](references/editorial/short-form-performance.md) ·
[nonfiction](references/editorial/nonfiction-formats.md) · [genres](references/editorial/narrative-genres.md) ·
[workflow-and-qa](references/editorial/workflow-and-qa.md) ·
[sources](references/editorial/research-sources.md) (a measured engine fact beats any of them)
