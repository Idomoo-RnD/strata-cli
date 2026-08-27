---
name: strata-cli
description: Makes motion-design videos with the strata CLI — scene JSON compiled to .idm and rendered to MP4 via Idomoo. Use for any strata / IDM / VASCO video, motion graphics, kinetic type, explainer, promo, logo sting, personalized or data-driven video; for generating images, AI video clips, reference images, talking presenters, narration and music; for brand documents, Figma-to-video, and ffmpeg edits (trim, join, reframe, grade). Not for the Idomoo cloud briefs API.
---

# Strata CLI — cinematic motion design, authored as IDM/VASCO

Make **bold, cinematic, story-driven motion graphics** — not slideshows. VASCO is a real 3D
motion-design engine: 3D layers with depth, a moving camera, masks, effects, per-character text
animators, a keyframe tween engine. Use that power; the default is *great*, not adequate.

This file is the **procedure and the routing**. The craft is [craft.md](references/craft.md), the
syntax is [format.md](references/format.md), the patterns are [recipes.md](references/recipes.md),
the commands are [commands.md](references/commands.md).

## Contents

- [Quick start](#quick-start)
- [Before authoring — five checks](#before-authoring--five-checks)
- [Workflow](#workflow)
- [The traps that cost a render](#the-traps-that-cost-a-render)
- [The craft, in one screen](#the-craft-in-one-screen)
- [References — condition → file](#references--condition--file)

## Quick start

```bash
strata version                                  # installed? if not: commands.md, Setup
strata route "<the brief in a sentence>"        # the reference files to read — read every one
#   ... storyboard, sign-off, then author the scene JSON ...
strata preview scene_v1.json --at 2 --grid      # free wireframe; fix composition here
strata validate scene_v1.json                   # offline: bad keys, traps, composition tells
strata compile  scene_v1.json -o scene_v1.idm
strata render   scene_v1.json --library <id> -o scene_v1.mp4     # ask which library first
strata review   scene_v1.mp4 --scene scene_v1.json -o review/    # then watch the MP4, four ways
```

Renders take minutes — run them in the background. Every command takes `--json`. Exit codes:
0 ok · 1 compile/schema · 2 missing file · 3 auth · 4 render timeout.

## Before authoring — five checks

Run these in order before anything else; each one changes what the job *is*.

- **A0 · Material supplied?** A storyboard, script, PDF, URL, sheets, footage, a voice → read
  [intake.md](references/intake.md) **first** and treat that material as the brief: transcribe it
  into the shot table (scenes, timecodes, first/last frames, dialogue, notes verbatim), route each
  scene to its generation mode, present *that* as the storyboard with the execution plan attached.
  A supplied storyboard is executed, not redesigned. And when two or more clips share a person,
  product or set — or consistency is asked for — build the
  [production bible](references/production-bible.md) before the first clip: one sheet and one
  canonical identity block per principal, reused byte-identical in every prompt.
- **A · Layout and motion not dictated?** Then four references are read **before authoring, every
  time**: [anti-slop.md](references/anti-slop.md) (the defaults not to reach for, and the test that
  catches them), [layouts.md](references/layouts.md) (the named frame layout + the grid),
  [video-layouts.md](references/video-layouts.md) (video as a design element), and
  [motion-design.md](references/motion-design.md) (make it read as After Effects, not web
  animation). Without them the default is a flat fade-and-slide slideshow — the failure this skill
  exists to prevent. `strata route` prints these plus whatever the brief's kind adds.
- **B · An EDIT, not a design job?** Footage handed over to cut, trim, join, crop/reframe, speed
  up, loop, mute or swap audio is mechanical: do it with ffmpeg per
  [video-editing.md](references/video-editing.md) and hand back an MP4. Never burn titles in with
  `drawtext` (no easing, no animators — it reads as a watermark). Graphics on top → edit the clip
  first, then use it as a `video` layer.
- **C · Brand first.** Check for `.brand/brand.md` at the start of every job. If it exists, read it
  and author everything from it — that is what makes a user's videos a set instead of one-offs. If
  it does not exist and brand material was supplied (logo, brand book, style guide, existing
  videos, a website, a Figma file), or the user says "on brand" / "like the last one" / a series,
  offer to build it first and get sign-off before storyboarding:
  [brand.md](references/brand.md).
- **D · From Figma?** Read [figma.md](references/figma.md) first — the mapping is full of traps
  (canvas-absolute boxes, one uniform scale, fonts as real file paths, vectors exported to PNG,
  `visible:false` skipped, repeated names crashing the render). Compare `strata preview --grid`
  against the Figma screenshot before rendering: an imported frame is a layout, not a video.

## Workflow

Copy this checklist and tick it off; the gates (4, 5, 7) are not optional.

- [ ] 1. Assets · [ ] 2. Storyboard + sign-off · [ ] 2½. Style · [ ] 3. Scene JSON (four passes)
- [ ] 4. Preview · [ ] 5. Validate + compile · [ ] 6. Render · [ ] 7. Review the MP4

**Before anything — run `strata route "<the brief in a sentence>"` and read every file it prints.** It maps the brief's kind (TV ad, promo, explainer, personalized, presenter, supplied storyboard, edit, brand, Figma…) to the exact reference files for that job — including the `editorial/` and `motion/` chapters — so the read list is a command output, not a table to interpret. The same map is the *Route by brief kind* table below. The storyboard's first line then states **References read:** with those paths; the Definition of Done checks it. **Route a second time once the concept exists** — a short brief cannot say "kinetic type", "music-led" or "thriller", but the direction chosen for it can, so re-run `strata route` on the brief **plus the chosen direction** before scene JSON and read whatever is new. (*Measured:* a "super AAA motion design" brief routed to *premium* only; the piece then became type-led and `motion/03-typography.md` was never opened.)

**Before step 1 — four checks, in this order:**

A0. **If the request comes WITH material — a storyboard image, a script, a shot list, reference sheets, footage, a voice — I read [references/intake.md](references/intake.md) FIRST and treat that material as the brief.** I transcribe it into the shot table (scenes, timecodes, first/last frames, dialogue, the notes verbatim), route each scene to its generation mode, and present *that* as the storyboard with my execution plan attached — I execute a supplied storyboard, I do not redesign it. And whenever two or more clips share a person, product or set — or the user asks for consistency — I build the **[production bible](references/production-bible.md)** before the first clip: one sheet and one canonical identity block per principal, reused byte-identical in every prompt.

A. **If the user hasn't dictated the exact layout and motion, I READ FOUR REFERENCES BEFORE AUTHORING — every time, not optionally:** **[references/anti-slop.md](references/anti-slop.md)** (the defaults I must not reach for, and the test that catches them), **[references/layouts.md](references/layouts.md)** (which frame layout + the grid), **[references/video-layouts.md](references/video-layouts.md)** (video as a design element — footage in shapes, type-as-window, split-screen, tracked graphics, subject cut-outs), and **[references/motion-design.md](references/motion-design.md)** (make it read as After-Effects, not web animation — custom easing, overshoot, offset/stagger, designed transitions, camera). These are what make output *designed* instead of vanilla; the default without them is a flat fade-and-slide slideshow, which is the failure this skill exists to prevent. I pick a named layout, apply the motion techniques, and run the "web-animation tells" checklist before I ship.
B. **Is this actually an EDIT, not a design job?** If the user hands me footage and asks to **cut, trim, join, crop/reframe, speed up, loop, mute or swap the audio**, that is a mechanical edit: I do it with **ffmpeg** per **[references/video-editing.md](references/video-editing.md)** and hand back an MP4 — I don't build a scene, and I never burn titles in with `drawtext` (no easing, no animators — it looks like a watermark). If they want graphics/titles/transitions on top, I edit the clip first, then use it as a `video` layer in a scene.
C. **BRAND FIRST — is there a `.brand/brand.md`?** I check at the very start of any video job. **If it exists I read it and author everything from it** (canvas, palette, type ramp with real font paths, components, motion language) — that is what makes a user's videos look like a set instead of unrelated one-offs. **If it does not exist and the user gave me brand material** (logo, brand book, style guide, existing videos, a website, a Figma file) — or asks for "on brand" / "like the last one" / a series — **I offer to build it first**: *"want me to capture this as a brand document so every video matches?"* I write it to `.brand/brand.md` from their assets and get sign-off before storyboarding. Full build + use workflow and the template: **[references/brand.md](references/brand.md)**.
D. **Working from a Figma design?** If the layout comes from Figma (via the Figma MCP), I read **[references/figma.md](references/figma.md)** first and follow it — the mapping is full of traps: Figma boxes are **canvas-absolute** (subtract the frame origin) and web-sized (**one uniform scale factor**, never stretch x/y differently); `"font": "Inter"` doesn't compile (fonts must be real `.ttf`/`.otf` **paths**); icons/vectors/gradients must be **exported to PNG**; `visible:false` nodes are skipped; and Figma's repeated names (`Rectangle 1`, `Text`) **crash the render** — I rename every layer unique. Then I **compare `strata preview --grid` against the Figma screenshot** before rendering, and author motion on top: an imported frame is a layout, not a video.
1. **Sort out assets first.** If any person, product or set recurs across clips, the **production bible** comes first — sheets + identity blocks, [production-bible.md](references/production-bible.md) — and every generated frame or clip cites them. Clip length is decided here, not fixed later: a generated clip that comes back **shorter than its scene is never retimed to fit** — I cover the scene with **more shots of the same subject** (a companion clip from a reference frame) or **extend** off the last frame ([video-generation.md](references/video-generation.md)); a longer one is trimmed, never sped up. **I generate in waves, in the background — every asset with no unmet dependency at once (sheets, plates, TTS, music), then every clip whose inputs exist, then only continuations — and I author the scene while they render; a 10-scene piece is ~10 min in waves, ~60 serially ([assets.md](references/assets.md)). Parallel renders need unique scene filenames.** For each visual element I decide: (a) do they have a file or should I `generate` it; (b) **is it a background/full-frame plate, or an element that sits OVER other layers?** — anything composited over another layer (plane, mascot, product cut-out, logo sting, person) needs **alpha, so it must be a `.jet`** — this is a format fact, not a style choice, and it applies **only when a layer really does sit over another**. A full-frame background, or footage that nothing overlaps, stays an ordinary MP4: `matte` is the slowest step in the pipeline and `.jet` is lossy, so I do not cut out a subject that has nothing to composite against (`strata matte clip.mp4` for ordinary footage, or generate it on a green screen and `strata jet --method chroma`); an `.mp4` there arrives as an opaque rectangle; and (c) **every image becomes a video** (see above) unless it is an icon/logo. I ask about **narration**/music too. Text layers need a real `.ttf`/`.otf`.
2. **Present a STORYBOARD and get sign-off — before any scene JSON.** (**No user available?** In an
   automated or unattended run, do not stall: make each call that would have been a question, write
   the storyboard and a short `decisions.md` recording every choice made in the user's place, and
   carry on. The gates that are not about taste — validate, preview, review — still apply in full.) Apply the craft ([craft.md](references/craft.md)) to plan the piece, then show the user a storyboard they can read and approve. I do **not** start authoring until they confirm; I revise the storyboard with them first (cheap to re-cut, expensive to re-render). Format:
   - **References read:** the paths from `strata route` that were actually opened — first line, every storyboard.
   - **Title** + one-line **Style** (palette, motion feel, type).
   - **For premium work** (AAA, cinematic, broadcast, launch film, hero animation — or when the brief says "premium"): **three meaningfully different motion directions** with **key styleframes** for the hook, the hero moment and the end card, and a **motion bible** (focal order, timing scale, easing family, type behaviour, camera rule, transition palette, sound motif, signature device, forbidden moves) — before any scene JSON. What stays *still* is decided as carefully as what moves. The protocol: [director.md](references/director.md).
   - The **editorial contract** — format / dominant mode / modifiers, the one-sentence **viewer promise**, and the **information-release** plan (what the viewer knows before, during, after each beat) — from [editing-director.md](references/editing-director.md). One dominant mode, at most two modifiers; never a generic fast montage.
   - A **shot list** — `Time | Visual / Motion | Voiceover | Sound` — one row per shot (~2–4s), covering the full duration.
   - **A LAYOUT SKETCH for the key frames** — a small ASCII wireframe showing *where things sit*, named from [references/layouts.md](references/layouts.md) (`hero-center`, `split-media-left`, `three-up`, `stat-hero`, `title-over-media`…). A shot list says what happens; only the sketch says what it will **look** like — so the user can move the logo or resize the stat for free, before any JSON.
   - **End frame** (logo/CTA text) and **Motion notes** (transition timing ~300–500ms; transform-based — scale/position/opacity/masks; the intended feel; **one dominant motion idea per shot, at most two transition families in the piece, and the job of every move named**).

   **When the framing could go more than one way, I offer 2–3 layout options as side-by-side wireframes and let the user pick** — people recognise the layout they want far better than they can describe it. And I **ask once, up front: "want me to show layout preview grids as I go?"** — if yes, I run `strata preview … --grid` at each key frame and show it before moving on.

   Whenever a layout is shown — wireframe, options, or a `preview` grid — ask in the same breath whether the user wants to lay it out themselves in `strata studio` (a local browser designer that writes a `*.guide.json` to author from). Launch it only on a yes, at the piece's real canvas: [layouts.md](references/layouts.md), *Hand the layout to the user*.

2½. **Pick the STYLE** — what kind of video is this? [video-styles.md](references/video-styles.md) has 50, each with its construction, timing values and the tells that sell it. I take the **technique** (shot order, stagger, easing, layer construction) and **never the sample's look**: if `.brand/brand.md` exists, the brand's palette, type, radii and motion language override the style entirely — style is composition, brand is atoms.
3. **Write the scene JSON** to the approved storyboard (compact format — `format.md` is the spec). **Build in four passes — blocking (boxes, cuts, holds, on `preview --grid`) → primary motion (the one idea per shot) → secondary motion (stagger, follow-through, counters) → finishing (effects, grade, sound)** — and never add finishing effects during blocking; passes 1–3 are judged on previews and snapshots, finishing on the rendered MP4. I pick a **frame layout** from [layouts.md](references/layouts.md) and **snap boxes to its grid** rather than inventing coordinates; I **reuse blocks** (`strata add <block>` — see [blocks.md](references/blocks.md)) and follow a [blueprint](references/blueprints.md) for the video type instead of building from scratch; **unique name on every layer**; iterate the timeline. **Hand-write vs generate:** I write scene JSON **by hand by default** — it stays the readable source of truth. I reach for a generator script when the scene contains **computed or heavily repeated values** (polygon paths, keyframes snapped to `strata beats` onsets, per-glyph offsets, big grids of near-identical layers, audio-envelope arrays) — code gets that math right where hand-typing silently doesn't. A script for a simple scene (a handful of layers with hand-picked boxes) is overengineering. Mixed scenes can mix: hand-write the scene, generate just the computed part (a path, a keyframe list) and paste it in. ⚠ **Write scene JSON with the file-writing tool, never through a shell heredoc.** Real copy contains apostrophes, `$`, backticks and backslashes — *measured:* a heredoc broke on the apostrophe in `BIG JOE'S` and silently produced malformed JSON. The shell is not a text editor; the same applies to any file whose content is user-facing copy.
4. **PREVIEW the layout locally — before spending any render.** ⚠ Preview draws **boxes, not glyphs**, so it cannot show where text sits *inside* its box — and vertical text anchors to the box **bottom** (`align "… top"` is not honoured; verified). Compute vertical positions from `box_y + box_h`, and confirm real type placement with a cheap `snapshot` — **`strata snapshot scene.json --library <id> --at <sec>`**: the default is t=1 s, so a piece that builds to a reveal has nothing there. Pick the moment the type is on screen rather than building a throwaway probe scene (which also litters the user's library). `strata preview scene.json --at <sec> --grid` draws a free, instant wireframe (every layer's box, the 12-column grid, title-safe + bottom safe line, thirds/centre). **I look at it and fix the composition** — balance (no dead third), alignment to the grid, one clear focal point, no overlaps, CTA on the safe line — and I re-preview each key shot (`--at`) until it reads right. This is where design gets fixed; renders are for confirming, not discovering. If the user asked for preview grids, I show each one.
5. **Validate, then compile:** `strata validate scene.json` (free, offline — names any bad key/layer, warns about the known exporter traps, **flags composition tells**, and with `--data rows.json` runs the **localisation guard**: per-row auto-fit shrink + per-row glyph coverage — scrims under text, rules under titles, side bars, list markers, effect stacks — each naming its [anti-slop.md](references/anti-slop.md) pattern; I fix them or say why the brief earns them) → `strata compile scene.json -o out.idm`.
   - **VERSION THE FILENAME ON EVERY NEW CUT — never overwrite the scene I just rendered.** The scene's **filename is what the user sees in their Idomoo library** (the upload is named `basename(<scene>)`), and the MP4 takes its name from it too. So each round of edits gets its own file: `promo_v1.json` → `promo_v2.json` → `promo_final.json` (`_v3`, `_rc`, `_15s`, `_9x16` — whatever names the change). I keep the descriptive stem and add the version, so the library sorts them together: `spring_sale_v1`, `spring_sale_v2`. **Why it matters:** overwriting means the library fills with identical names the user can't tell apart, the previous MP4 is clobbered, and there's no way back to a cut they preferred. I state the new filename when I report a render.
   - **Version history is AUTOMATIC and I use it.** Every `compile`/`render` snapshots the scene into `.strata/versions/` beside it (unchanged content makes no new version), and every `.idm` I produce carries an **embedded version stamp** (`strata.meta` - version, parent, scene hash; survives the Idomoo exporter, `strata inspect` prints it). So: **(a)** when the user asks to redo, iterate or "go back", I run `strata versions <scene>` and **name the version I am continuing from** ("continuing from v4"); **(b)** to restore one: `strata revert <scene> --to N` - the current state is snapshotted first, so revert never loses anything (with `-o other.json` it branches instead); **(c)** when handed a bare `.idm`, `strata inspect` tells me exactly which version it is and what it descended from. **A previous cut is never gone** - I never tell a user their earlier version is unrecoverable without checking `strata versions` first. Details: [format.md](references/format.md).
6. **Render:** `strata render scene.json --library "<id>" -o out.mp4`.
   - **Library — ASK before the first render, never pick one.** Renders land in the user's Idomoo workspace and stay there. Run `strata library list`, offer reuse or create (`library create` is get-or-create), persist the printed id, and pass that same `--library <id>` every time. `render` refuses to guess: [commands.md](references/commands.md), *Libraries — ask, never pick*.
   - Renders take minutes — I run them in the **background** and report the `video_url`/`poster_url`.
   - **Only if the user asks for the scene to be TAGGED** (a reusable template / catalog entry, not a one-off): add `--tags manifest.json` here and on `compile`, after reading **[tagging.md](references/tagging.md)**. The manifest rides inside the `.idm`, so the library copy is self-describing. I never invent tags — both vocabularies are closed.
7. **Review the rendered video — the critic pass.** A poster frame proves composition; it proves nothing about time. `strata snapshot scene.json --library <id>` (a fast poster-only frame, cheaper than a full MP4) is the check *before* the render; after the first render **I review the MP4 itself**, per [review.md](references/review.md):
   - `strata review out.mp4 --scene scene.json [--reference ref.mp4] -o review/` — cuts detected, a contact sheet, frames ±3 around every cut and at every settle, freeze frames, loudness, motion energy per shot; a `report.md` with timecodes.
   - Then watch it **four ways**: at 1× with sound, muted, at phone size, and frame-stepped around every cut and settle.
   - Score each category **pass / must-fix, with a timecode and a frame as evidence** — art direction, hierarchy, weight & easing, typography, editing, compositing, sound, originality, brand fidelity. Numbers alone are not a verdict; a must-fix without a timecode is not a finding.
   - **Revise while any must-fix remains.** Compiling, validating, or using advanced features (3D, camera, `.jet`, occlusion) is never itself a reason to approve.
   The **Definition of Done**, still every item:
   - **References read** listed on the storyboard match `strata route` for this brief?
   - Message clear in the first 3 seconds? Every shot earns its place? Stillness *and* energy — and every move has a job?
   - Text legible **muted** and inside the safe area — legible by placement, panel or grade, not by a scrim patched under it?
   - **Ran the composition-tells checklist in [anti-slop.md](references/anti-slop.md) and removed one thing?** If two tells are true the frame reads as generated.
   - **Every layer name unique ACROSS EVERY COMP** — and did I read any `⚠ renamed … duplicate layer name(s)` line? The compiler fixes duplicates silently-ish by renaming (`label`→`label_2`), which **changes the personalization key**, so I fix the scene rather than accept the rename.
   - Holds on the CTA; nothing loops cheaply; motion blur on moving layers? Settles land (no element stops dead, none overshoots without a reason)? Cuts land on the audio?
   - When references exist, the render was compared against their measured traits (`strata deconstruct`)?
   - **Would I ship this with my name on it?** If not, I fix it before delivering.
   Debug with `--vasco` or `strata inspect out.idm`.

## The traps that cost a render

Each of these produces a *wrong video* rather than an error. The full text, with the measurement
behind each, is [traps.md](references/traps.md) — read it before the first compile of any piece.

- **If anyone speaks in a clip, that clip's audio IS the voice** — never lay a TTS of the same line
  on top (*measured:* lines land 1.3 / 3.8 / 6.3 s in, so no offset repairs it). A TTS file is an
  input to the generation **or** a layer in the scene, never both.
- **A talking clip is two references and a job line:** `generate video --ref-image <portrait>
  --ref-audio <tts url> --audio`; in the prompt, `USE [Audio 1] AS THE CHARACTER'S VOICE FOR THE
  ENTIRE VIDEO.`, demand the lip-sync, put the line in `{curly braces}` byte-identical to the TTS,
  hold a close-up on the mouth. A bare `--ref-audio` is refused.
- **References are cited by index, with a job.** `--ref-image` order = `[Image 1]`, `[Image 2]`…
  (`generate image --reference` counts from `image 0`); each gets a JOB line, the identity block is
  restated ×3, ≤4 references, frames and references never mix.
- **Editing an AI clip with audio: the first `-map` silently drops the track** — pair every
  `-map "[v]"` with `-map 0:a`, then `ffprobe` the output.
- **`position` is a DELTA from the box** (no anchor) — never repeat the box's x/y in it; with an
  anchor it is the absolute pivot point. `validate` warns on the tell-tale.
- **Overlay video needs alpha → `.jet`, never `.mp4`**; the plate and its matted overlay are the
  SAME clip; the `.jet` fps must match the scene; any video input needs ffmpeg installed.
- **Personalised data visuals must be real image files** — or `strata chart` layers, whose value
  labels personalise while heights are baked (per-viewer heights = one scene per row).
- **Layer names unique across the whole scene** — the exporter keys by name globally; the compiler
  auto-renames duplicates, which silently changes the personalization key.
- **Fonts must cover every glyph** (`strata glyphs`) — a missing glyph is tofu or a cloud-render
  crash. Hebrew/Arabic already lay out correctly; `rtl` is a no-op and never the fix.
- **Camera is a measured pinhole** — park it at `z = −focal` (−935 for 1080p at fov 60); negative z
  comes toward the camera; anchor is 2D, depth goes on `position` z.
- **Misc:** comp max 1920/axis · motion blur on for moving layers · keyframe times are relative to
  the layer's `start` · a sub-comp is declared before the comp that uses it · `validate` before
  every render, and read the `⚠` lines it prints.

## The craft, in one screen

The manifesto — story, shots, composition, occlusion, tracking, 3D and camera, motion principles,
kinetic type, transitions, atmosphere, rhythm and sound, polish, and the design rules — is
[craft.md](references/craft.md). Read it when planning any piece the user has not fully dictated.
The spine:

- **Find the arc and the tension**; one idea per shot; align on the concept before building.
- **Think in shots** — establishing → push in, hard cuts, follow shots, reveals; name the shot
  size; change size *and* camera character at every cut.
- **Compose, don't fullscreen** — splits, grids, framed media, solids as design elements; use the
  whole frame; occlusion and tracking only when their conditions hold.
- **Defaults are not choices** — the anti-slop test per element; spend boldness once; remove one
  thing before rendering.
- **Use the 3D camera** — parallax, push-ins, orbits; move the camera, not each layer — when the
  move has a job. A frame that is *designed* flat is a decision, not a flaw.
- **Something is always moving, and every move has a named job**; every image becomes a video;
  hold text for read time. One dominant motion idea per shot, at most two transition families.
- **Easing is the craft** — `outExpo`/`outCubic` settle, `outBack` sparingly, `inOutSine` for
  drifts, `linear` only for loops.
- **Cut to the audio with `strata beats`**, never by ear; sound design is three layers — voice,
  bed, SFX.
- **Design** — hierarchy (the stamp test), ≤2 typefaces, brand colour, contrast matched to the
  plate, safe areas, no overlaps, personalization keyed by layer name.

# References — read these, don't skip them

## Route by brief kind — the read list is a lookup, not a judgement

`strata route "<brief>"` prints exactly this. Rows add up: a personalized TV ad reads the TV-ad row **and** the personalized row. Every piece the user did not fully dictate also reads the four **ALWAYS** files (anti-slop, layouts, video-layouts, motion-design). Paths are relative to `references/`.

| The brief is… | Read before the storyboard |
|---|---|
| a **TV / broadcast ad, promo, launch, sale, hero film** | `editing-director.md`, `editorial/short-form-performance.md`, `editorial/editing-grammar.md`, `blueprints.md`, `video-styles.md`, `video-prompts.md`, `motion/04-transitions.md`, `motion/07-narrative-sound-emotion.md` |
| **social / vertical / UGC / trailer-style** | `editing-director.md`, `editorial/short-form-performance.md`, `blueprints.md`, `video-styles.md`, `motion/04-transitions.md` |
| an **explainer, tutorial, testimonial, internal comms, recap, sports** | `editing-director.md`, `editorial/nonfiction-formats.md`, `editorial/editing-grammar.md`, `blueprints.md`, `motion/07-narrative-sound-emotion.md` |
| **personalized / data-driven / a chart or a stat** | `personalization.md`, `motion/06-ui-data-brand.md`, `editorial/nonfiction-formats.md` (data is a claim) |
| a **presenter, spokesperson, talking head, dialogue** | `avatar.md`, `video-generation-advanced.md`, `production-bible.md`, `captions.md` |
| **supplied material** (storyboard, script, PDF, URL, sheets, footage, voice) | `intake.md` first, then `production-bible.md`, `video-generation.md`, `video-generation-advanced.md` |
| a **logo sting, brand ident, product hero** | `blueprints.md`, `video-styles.md`, `motion/05-shape-effects-texture.md`, `motion/06-ui-data-brand.md`, `recipes.md` |
| **kinetic type / typographic / captions-heavy** | `motion/03-typography.md`, `recipes.md`, `layouts.md` |
| **music-led / beat-synced / audio-reactive** | `generative-fx.md`, `music.md`, `motion/07-narrative-sound-emotion.md`, `motion/04-transitions.md` |
| a **genre feel** (thriller, comedy, trailer, documentary) | `editorial/narrative-genres.md`, `reference-styles.md`, `motion/08-styles-index.md` |
| **"make it feel like X"** (a film, director, campaign) | `reference-styles.md`, `motion/08-styles-index.md`; `strata deconstruct` on any supplied clip |
| **premium / AAA / cinematic / broadcast quality** (in addition to the kind above) | `director.md`, `motion/01-foundations.md`, `motion/02-choreography-space-camera.md`, `motion/09-production-qa.md` |
| **any generated video clip** | `video-generation.md`, `video-prompts.md`; consistent cast → `production-bible.md` |
| **an edit** of supplied footage (cut, join, reframe, retime) | `video-editing.md`, `editorial/workflow-and-qa.md` |
| **brand material** supplied, "on brand", a series | `brand.md` |
| **Figma** | `figma.md` |
| **after the first render — every piece** | `review.md`, `motion/09-production-qa.md`, `editorial/workflow-and-qa.md` |

Every reference below is a real file that ships with this skill. The **When** column is a
rule, not a suggestion: if the row's condition is true I **read that file before I author**,
even if I think I know the answer — skipping it is how output turns out vanilla or broken.
The three marked **ALWAYS** are read on every non-trivial piece where the user didn't dictate
the design (see workflow check A). The `motion/` and `editorial/` chapters have their own rows
so no file is two hops away.

| Reference | Read it to… | When (do NOT skip) |
|---|---|---|
| **[craft.md](references/craft.md)** | the craft manifesto — story and shots, composition, occlusion, tracking, 3D and camera, motion principles, kinetic type, transitions, atmosphere, rhythm and sound, polish, and the design/layout rules | when planning any piece the user has not fully dictated; the hub carries only its one-screen spine |
| **[commands.md](references/commands.md)** | the CLI itself — setup, every asset-generation command with the rules that go with it, every other command in one line each, libraries, flags and exit codes | before generating any asset, and whenever a command's exact flags matter |
| **[traps.md](references/traps.md)** | the engine traps in full, each with the measurement behind it — doubled voices, dropped audio, `position` vs `box`, alpha overlays, personalised data, duplicate names, glyphs, sub-comps | before the first compile of any piece, and whenever a render came back wrong while the compile was clean |
| **[director.md](references/director.md)** | run the **creative-direction pass** for premium work: thesis, reference analysis, three genuinely different motion directions | for AAA, premium, cinematic, broadcast, launch-film or hero-animation briefs, before any scene JSON |
| **[review.md](references/review.md)** | the **critic pass** on the rendered MP4: `strata review`, the four viewings, the nine pass/must-fix categories with timecoded | **after every first render**, before calling anything done |
| **[music.md](references/music.md)** | prompt `generate music` (Stable Audio 3) so the track fits: the tag+genre+mood+instruments+BPM formula, writing the ARC, ducking | before generating music — a vibe-only prompt returns generic stock-sounding audio |
| **[avatar.md](references/avatar.md)** | build a **talking presenter** from a still + audio (`generate avatar`): the image prompt that makes it look filmed rather than | whenever a piece has a presenter, spokesperson or talking head — read the decision first, then the mechanics of whichever route |
| **[intake.md](references/intake.md)** | what to do when the user HANDS OVER material: reading a storyboard image or script into a shot table (scenes, timecodes | **first, whenever the request comes with inputs** — a storyboard, script, sheets, footage, a voice; a supplied storyboard is |
| **[production-bible.md](references/production-bible.md)** | the consistency layer for a multi-clip piece: rank the principals (**the brief's ranking wins**), one sheet each (**supplied sheets are used as-is**), one canonical identity block pasted **byte-identical ×3** in every prompt, references in one order, one voice per speaker, per-clip verification | before the first clip of anything with ≥2 clips sharing a person/product/set, and whenever a consistent actor is asked for |
| **[video-editing.md](references/video-editing.md)** | **edit an existing video** with ffmpeg — trim/cut, concat, reframe to 9:16, speed, audio strip/replace, fades, loops, thumbnails | when the user hands over footage to cut/join/crop/retime rather than a video to design; also before using any supplied clip as a |
| **[brand.md](references/brand.md)** | capture a reusable visual identity as **`.brand/brand.md`** (palette from code-extracted hexes, type ramp with real font paths | **Check for `.brand/brand.md` at the start of every job** — read it if present |
| **[layouts.md](references/layouts.md)** | pick a named frame layout (hero-center, split-media, three-up, stat-hero, title-over-media, quote, list-reveal, lower-third) and | **ALWAYS** before authoring, unless the user dictated exact boxes — then verify with `strata preview --grid` |
| **[video-layouts.md](references/video-layouts.md)** | compose *with* video — footage in shapes (masks), type-as-window (track matte), split-screen/grid, device frames, PiP, subject | **ALWAYS** when the piece uses any video — the difference between a designed composition and one full-bleed clip + caption |
| **[motion-design.md](references/motion-design.md)** | make it read as After-Effects not web animation — custom easing, overshoot+settle, offset/stagger, anticipation, follow-through | **ALWAYS** before deciding how anything moves — the single biggest quality lever |
| **[anti-slop.md](references/anti-slop.md)** | **MANDATORY** — the defaults agent-composed frames fall into regardless of subject (scrim behind every text block, rule under | **before every storyboard and before every render** — the brief's words always win; this governs only the axes it leaves free |
| **[motion-atlas.md](references/motion-atlas.md)** | the vocabulary layer — ~700 named motion concepts across 9 chapters. Names and taxonomy only: **where it and motion-design.md give a number for the same thing, motion-design.md wins** | as a lookup, not by default — naming a technique, wanting options, critiquing motion, turning adjectives into decisions |
| **[editing-director.md](references/editing-director.md)** | the **editorial** layer — it routes into `references/editorial/` - why this cut, here, now: the editorial contract (viewer promise, information release), the six control | **when planning the storyboard**, whenever a brief names a kind of video, when cutting or critiquing real footage, and whenever |
| **[generative-fx.md](references/generative-fx.md)** | sync the edit to music (`strata beats` → snap keyframes to onsets), WRITE generator scripts for overlays (confetti, light leaks | when the piece has music or audio (beat-syncing is mandatory, never hand-timed), and whenever the design calls for |
| **[assets.md](references/assets.md)** | generate media: image (+**reference images**, indexed in the prompt, for art style / same-character), image-to-video, narration | before generating **any** asset, or using a reference image |
| **[recipes.md](references/recipes.md)** | paste an engine-correct pattern (kinetic text, transitions, motion, masks, FX, 3D/camera, data-viz, **track & corner-pin**) | before hand-writing any animation — start from a recipe |
| **[blocks.md](references/blocks.md)** | reuse a sub-comp block (lower-third, stat-card, end-card, logo-sting, device-frame, search-bar, quote-card) via `strata add | before building a common component from scratch |
| **[blueprints.md](references/blueprints.md)** | pick a whole-video structure by type (product launch, explainer, social promo, data story, logo reveal, website/app showcase | before writing the storyboard |
| **[format.md](references/format.md)** | look up exact syntax — every layer key, 3D/camera, effects, masks (rect/ellipse/path), track mattes, sub-comps, rich-text | for **any** syntax question, and to confirm a key exists before inventing one |
| **[figma.md](references/figma.md)** | import a Figma design correctly — scale/origin math, node→layer map, fonts→paths, export vectors, unique names | whenever the layout comes from Figma |
| **[personalization.md](references/personalization.md)** | build one template → many personalized videos: placeholder naming, `--emit-timeline` for the contract, `render --data rows.json` for the batch, `--emit-timeline` for the contract, and **`render --data | for any personal / data-driven video |
| **[video-styles.md](references/video-styles.md)** | pick the **kind** of video and copy its construction — 50 styles, each with its shot grammar, stagger/easing values and the tells that sell it, plus the engine gotchas. ⚠ **TECHNIQUE only: a `.brand/brand.md` overrides every style** | whenever the piece has a recognisable kind ("like a TikTok", "cinematic", "sale ad", "logo sting") — and for the motion-value table on any piece |
| **[video-generation.md](references/video-generation.md)** | how to prompt `strata generate video` — the five modes and the hard rule that frames and references never mix | **every time I generate a video clip** — a one-line prompt wastes a 3–9 minute render |
| **[captions.md](references/captions.md)** | speech timing from an asset — `strata captions` transcribes audio **or video** into `{t0,t1,text}` cues; hosting is handled. ⚠ **segment-level, not per-word** (measured — no cheap way to subdivide a cue) | whenever the scene needs to know when words are spoken: caption bars, timing a lower-third, sizing shots to the VO, checking a clip said its line |
| **[video-generation-advanced.md](references/video-generation-advanced.md)** | reference images (character/product/world consistency), reference video (copy a camera plan), reference audio + dialogue, `strata | when a clip needs a **consistent character**, an **authored camera move**, spoken dialogue, or a change to existing footage |
| **[video-prompts.md](references/video-prompts.md)** | the **prompt cookbook** - a worked, measured prompt per KIND of video (product hero, logo sting, food, vertical social hook | **before writing any generate-video prompt** - pick the entry nearest the job, keep its skeleton, swap the subject |
| **[reference-styles.md](references/reference-styles.md)** | "make it feel like X" - ~25 named films, directors and campaign styles mapped to a **measurable motion signature** and the | when a brief names a reference; a reference is a technique source, never a costume |
| **[tagging.md](references/tagging.md)** | make a scene reusable: the `--tags` manifest saying what the scene is for and which layers swap — **two CLOSED vocabularies**, the same-index-means-same-content rule, and why baked decor stays undocumented | whenever the user asks to tag a scene, make a template, or add it to a catalog — never otherwise |
| **[motion/01-foundations.md](references/motion/01-foundations.md)** | the animation principles, timing and spacing, easing families, physics and weight, rhythm patterns | any timing, easing or "why does this feel wrong" question; premium work |
| **[motion/02-choreography-space-camera.md](references/motion/02-choreography-space-camera.md)** | staging and focal control, composition in motion, depth, camera language | multi-element scenes, parallax, any camera move; premium work |
| **[motion/03-typography.md](references/motion/03-typography.md)** | entrance/exit patterns for type, craft rules, numbers in type, multilingual and RTL | any text, counter, caption, Hebrew/Arabic work |
| **[motion/04-transitions.md](references/motion/04-transitions.md)** | editorial cuts, dissolves, wipes, motion-driven and textural transitions, designing a transition system (the two-family rule) | scene changes, editing rhythm, ads and social |
| **[motion/05-shape-effects-texture.md](references/motion/05-shape-effects-texture.md)** | shape and form, masks and mattes, optical effects, particles, colour/light/compositing, materials | logo builds, icon animation, effects, backgrounds, the look |
| **[motion/06-ui-data-brand.md](references/motion/06-ui-data-brand.md)** | UI and product motion, data and infographic motion (honesty rules), brand motion systems | product UI, dashboards, charts, stings, every personalized number |
| **[motion/07-narrative-sound-emotion.md](references/motion/07-narrative-sound-emotion.md)** | story structure for motion, pacing and editing, sound and music, emotion → motion parameters | briefs, scripts, music-driven pieces, tone |
| **[motion/08-styles-index.md](references/motion/08-styles-index.md)** | the 77-entry style index: graphic, retro, illustration, digital, genre conventions | "make it feel like X", genre references |
| **[motion/09-production-qa.md](references/motion/09-production-qa.md)** | formats and baselines, accessibility and safety, the QA checklist, anti-patterns, default numbers | before delivery, technical specs, reviews |
| **[editorial/editing-grammar.md](references/editorial/editing-grammar.md)** | the six control dimensions of pace, editing languages, cut and transition vocabulary, sound–picture grammar, the pacing-diagnosis | cut logic, pacing, continuity, montage, "why does this feel flat"; every ad |
| **[editorial/short-form-performance.md](references/editorial/short-form-performance.md)** | promos, ads, trailers, social: objective-specific editing, hooks, CTA dwell, failure modes | anything with a CTA — promo, ad, social, trailer |
| **[editorial/nonfiction-formats.md](references/editorial/nonfiction-formats.md)** | explainers, testimonials, corporate, sports; factual-integrity rules for real people and real data | explainers, testimonials, internal comms, recaps, personalized data |
| **[editorial/narrative-genres.md](references/editorial/narrative-genres.md)** | genre as a modifier: thriller information release, trailer escalation, comedy timing, documentary restraint | a brief with a genre feel |
| **[editorial/workflow-and-qa.md](references/editorial/workflow-and-qa.md)** | the editorial workflow, captions, flashing, rights, review passes on a cut | cutting or critiquing footage; the review pass |
| **[editorial/research-sources.md](references/editorial/research-sources.md)** | where the editorial rules come from, and what to do when one conflicts with a measured engine fact (the measured fact wins) | only when an editorial rule and a measured fact disagree |
