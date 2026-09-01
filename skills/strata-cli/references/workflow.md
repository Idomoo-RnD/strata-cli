# The production workflow — in full

`SKILL.md` carries the shape of a job in one screen. This file is the same workflow with every
detail, measurement and command behind each step. Read it once per piece — before the storyboard
is a good moment — and come back to a step when it is in front of you.

## Contents

- [The four checks that change what the job is](#the-four-checks-that-change-what-the-job-is)
- [Routing — which references matter for this brief](#routing--which-references-matter-for-this-brief)
- [1. Assets](#1-assets)
- [2. Storyboard and sign-off](#2-storyboard-and-sign-off)
- [2½. Style](#2-style)
- [3. Scene JSON](#3-scene-json)
- [4. Preview](#4-preview)
- [5. Validate and compile](#5-validate-and-compile)
- [6. Render](#6-render)
- [7. Review the rendered MP4](#7-review-the-rendered-mp4)
- [Definition of Done](#definition-of-done)

## The four checks that change what the job is

Run these first; each one turns the request into a different job.

- **A0 · Material supplied?** A storyboard image, a script, a shot list, reference sheets, footage,
  a voice — read [intake.md](intake.md) first and treat that material as the brief. Transcribe it
  into the shot table (scenes, timecodes, first/last frames, dialogue, the notes verbatim), route
  each scene to its generation mode, and present *that* as the storyboard with the execution plan
  attached. A supplied storyboard is executed, not redesigned. Whenever two or more clips share a
  person, product or set — or consistency is asked for — build the
  [production bible](production-bible.md) before the first clip: one sheet and one canonical
  identity block per principal, reused byte-identical in every prompt.
- **A · Layout and motion not dictated?** Then four references do most of the work of making the
  piece *designed* rather than assembled: [anti-slop.md](anti-slop.md),
  [layouts.md](layouts.md), [video-layouts.md](video-layouts.md) and
  [motion-design.md](motion-design.md). Without them the default is a flat fade-and-slide
  slideshow at the middle of every range, which is the failure this skill exists to prevent. Pick
  a named layout, apply the motion techniques, and run the "web-animation tells" checklist before
  shipping.
- **B · An edit, not a design job?** Footage handed over to cut, trim, join, crop/reframe, speed
  up, loop, mute or swap audio is mechanical: do it with ffmpeg per
  [video-editing.md](video-editing.md) and hand back an MP4 — no scene. Titles are text layers, so
  they get easing and animators; burning them in with `drawtext` reads as a watermark. Graphics on
  top of footage → edit the clip first, then use it as a `video` layer.
- **C · Brand first.** Check for `.brand/brand.md` at the start of every job. If it exists, read it
  and author everything from it (canvas, palette, type ramp with real font paths, components,
  motion language) — that is what makes a user's videos a set instead of unrelated one-offs. If it
  does not exist and brand material was supplied (logo, brand book, style guide, existing videos, a
  website, a Figma file), or the user says "on brand" / "like the last one" / a series, offer to
  build it first — *"want me to capture this as a brand document so every video matches?"* — write
  it to `.brand/brand.md` from their assets and get sign-off before storyboarding:
  [brand.md](brand.md).
- **D · From Figma?** Read [figma.md](figma.md) first; the mapping is full of traps. Figma boxes are
  canvas-absolute (subtract the frame origin) and web-sized (one uniform scale factor, never stretch
  x and y differently); `"font": "Inter"` does not compile (fonts are real `.ttf`/`.otf` paths);
  icons, vectors and gradients are exported to PNG; `visible:false` nodes are skipped; Figma's
  repeated names (`Rectangle 1`, `Text`) collide — the compiler auto-renames them, silently
  changing the replacement keys — so every layer gets a unique name. Then
  compare `strata preview --grid` against the Figma screenshot before rendering, and author motion
  on top — an imported frame is a layout, not a video.

## Routing — which references matter for this brief

`strata route "<the brief in a sentence>"` prints the read list; the same map is *What to read for
this brief* in `SKILL.md`. Run `strata route` a **second** time once the concept exists — a short brief cannot
say "kinetic type", "music-led" or "thriller", but the direction chosen for it can. (*Measured:* a
"super AAA motion design" brief routed to *premium* only; the piece then became type-led and
`motion/03-typography.md` was never opened — the premium row now includes it, since premium
pieces are so often carried by one word.)

## 1. Assets

If any person, product or set recurs across clips, the production bible comes first — sheets and
identity blocks, [production-bible.md](production-bible.md) — and every generated frame or clip
cites them.

**Clip length is decided here, not fixed later.** Every clip must be at least as long as the scene
slot it fills — generate it longer and trim, never shorter. A `video` layer that runs out before
its slot does not stop being on screen: it holds its last frame, so the shot freezes for the
remainder and the piece dies mid-scene (with `loop` it visibly restarts instead, which is worse).
Size each clip from the storyboard's slot plus ~1 s of margin, and when one still comes back
shorter than its scene it is never retimed to fit — cover the scene with more shots of the same
subject (a companion clip from a reference frame) or extend off the last frame
([video-generation.md](video-generation.md)); a longer one is trimmed, never sped up.

**Generate in waves, in the background** — every asset with no unmet dependency at once (sheets,
plates, TTS, music), then every clip whose inputs exist, then only continuations — and author the
scene while they render; a 10-scene piece is ~10 min in waves, ~60 serially
([assets.md](assets.md)). Parallel renders — and parallel `snapshot`s — need unique scene filenames: two jobs on one file collide in the exporter (error 3000).

For each visual element decide: (a) is there a file, or should it be generated; (b) does it sit
*over* another layer? Anything composited over another layer (plane, mascot, product cut-out, logo
sting, person) needs alpha and must be a `.jet` — a format fact, not a style choice. A full-frame
plate, or footage nothing overlaps, stays an ordinary MP4: `matte` is the slowest step in the
pipeline and `.jet` is lossy, so nothing is cut out that has nothing to composite against
(`strata matte clip.mp4`, or generate on green and `strata jet --method chroma`); an `.mp4` used as
an overlay arrives as an opaque rectangle. And (c) every image becomes a video unless it is an icon
or logo. Ask about narration and music too. Text layers need a real `.ttf`/`.otf`.

## 2. Storyboard and sign-off

Present a storyboard the user can read and approve before any scene JSON — re-cutting a
storyboard is cheap, re-rendering a finished video is not. Revise it with them first.

**No user available?** In an automated or unattended run, do not stall: make each call that would
have been a question, write the storyboard and a short `decisions.md` recording every choice made in
the user's place, and carry on. The checks that are not about taste — validate, preview, review —
still apply in full. **Never end a turn on a promise.** Nobody is watching, so a turn that ends on
"waiting for the snapshots" or "I'll render next" blocks the job until someone notices (*measured:*
a 30 s ad stalled twice this way, once on a background waiter that never woke the agent). Run
long steps in the foreground with a long timeout, or poll them yourself in the same turn, and end
the turn only when the deliverable exists or a real blocker needs the user.

Apply the craft ([craft.md](craft.md)) to plan the piece. A storyboard that lets the user actually
decide has:

- **Title**, a one-line **style** (palette, motion feel, type, where the light comes from), and
  **the four numbers** — the shot-length range (the shortest cut and the longest hold, each named
  with the job that earns it, and the hold also with what keeps moving through it), motion energy, stillness ratio, and loudness as an integrated LUFS
  target for where the piece will play plus the gap between its loudest and quietest beat
  ([music.md](music.md), *Decide the mix*) — with the reference row they sit near and the one line
  of *this* brief that puts them there (SKILL.md, *Place the piece on the range*). Written before
  the shot list, because the shot list is derived from them, and decided here rather than inherited
  from whatever the bed generated at.
- **For premium work** (AAA, cinematic, broadcast, launch film, hero animation — or when the brief
  says "premium"): directions that differ in kind, styleframes for the hook, the hero moment and the
  end card, and a motion bible when the piece is big enough to need one. What stays still is decided
  as carefully as what moves. The protocol: [director.md](director.md).
- The **editorial contract** — format / dominant mode / modifiers, the one-sentence viewer promise,
  and the information-release plan (what the viewer knows before, during, after each beat) — from
  [editing-director.md](editing-director.md). One dominant mode, a modifier or two; a generic fast
  montage is what a piece becomes when nobody chose.
- A **shot list** — `Time | Visual / Motion | Voiceover | Sound` — covering the full duration,
  **one row per beat with the length that beat's job needs** — the mean falls out of the rows, it
  does not generate them. Mark the declared hold — naming the motion that carries it, not just its length — and the declared
  flashes, and read the durations
  column before any JSON: rows all within a whisker of each other are one speed, whatever the mean.
- **A layout sketch for the key frames** — a small ASCII wireframe showing where things sit, named
  from [layouts.md](layouts.md) (`hero-center`, `split-media-left`, `three-up`, `stat-hero`,
  `title-over-media`…). A shot list says what happens; only the sketch says what it will look like,
  so the user can move the logo or resize the stat for free, before any JSON.
- **End frame** (logo/CTA text) and **motion notes** — transition timing ~300–500 ms, transform-based
  (scale/position/opacity/masks), the intended feel, how many motion ideas run per shot and how
  many transition families are in play (both scale with the declared energy —
  [craft.md](craft.md)), and the job of every move.

When the framing could go more than one way, offer 2–3 layout options as side-by-side wireframes —
people recognise the layout they want far better than they can describe it. Ask once, up front,
whether to show `strata preview … --grid` at each key frame as you go. Whenever a layout is shown,
offer `strata studio` in the same breath (a local browser designer writing a `*.guide.json` to
author from); launch it only on a yes: [layouts.md](layouts.md), *Hand the layout to the user*.

## 2½. Style

What kind of video is this? [video-styles.md](video-styles.md) has 50, each with its construction,
timing values and the tells that sell it. Take the technique (shot order, stagger, easing, layer
construction), not the sample's look: if `.brand/brand.md` exists, the brand's palette, type,
radii and motion language override the style entirely — style is composition, brand is atoms.

## 3. Scene JSON

Write the scene JSON to the approved storyboard (compact format — [format.md](format.md) is the
spec). A build order that keeps decoration from creeping in: blocking (boxes, cuts, holds, on
`preview --grid`) → primary motion (the one idea per shot) → secondary motion (stagger,
follow-through, counters) → finishing (effects, grade, sound). Passes 1–3 are judged on previews
and snapshots, finishing on the rendered MP4.

Pick a frame layout from [layouts.md](layouts.md) and snap boxes to its grid rather than
inventing coordinates; reuse blocks (`strata add <block>`, [blocks.md](blocks.md)) and follow a
[blueprint](blueprints.md) for the video type instead of building from scratch; a unique name on
every layer; iterate the timeline.

**Hand-write vs generate.** Scene JSON is written by hand by default — it stays the readable
source of truth. Reach for a generator script when the scene contains computed or heavily repeated
values (polygon paths, keyframes snapped to `strata beats` onsets, per-glyph offsets, big grids of
near-identical layers, audio-envelope arrays) — code gets that math right where hand-typing
silently doesn't. A script for a simple scene (a handful of layers with hand-picked boxes) is
overengineering. Mixed scenes can mix: hand-write the scene, generate just the computed part (a
path, a keyframe list) and paste it in.

**Write scene JSON with the file-writing tool, never through a shell heredoc.** Real copy contains
apostrophes, `$`, backticks and backslashes — *measured:* a heredoc broke on the apostrophe in
`BIG JOE'S` and silently produced malformed JSON. The shell is not a text editor; the same applies
to any file longer than a few lines — a storyboard, `decisions.md`, a prompt file — a quoted heredoc
has failed on a markdown storyboard in the same way (*measured*).

## 4. Preview

Preview the layout locally before spending any render. `strata preview scene.json --at <sec>
--grid` draws a free, instant wireframe (every box, the 12-column grid, title-safe and bottom safe
line, thirds/centre). Fix the composition against [layouts.md](layouts.md)'s rules and re-preview
each key shot (`--at`) until it reads right. This is where design gets fixed; renders are for
confirming, not discovering.

Preview draws boxes, not glyphs, so it cannot show where text sits *inside* its box — and vertical
text anchors to the box bottom (`align "… top"` is not honoured; verified). Compute vertical
positions from `box_y + box_h`, and confirm real type placement with a cheap snapshot —
`strata snapshot scene.json --library <id> --at <sec>`. The default is t = 1 s, so a piece that
builds to a reveal has nothing there; pick the moment the type is on screen rather than building a
throwaway probe scene (which also litters the user's library).

## 5. Validate and compile

`strata validate scene.json` is free and offline: it names any bad key or layer, warns about the
known exporter traps, flags composition tells (scrims under text, rules under titles, side bars,
list markers, effect stacks — each naming its [anti-slop.md](anti-slop.md) pattern; fix them or say
why the brief earns them, and check the scrim warning's two layers are ever on screen together),
and with `--data rows.json` runs the localisation guard: per-row auto-fit shrink and per-row glyph
coverage. Then `strata compile scene.json -o out.idm`.

**Version the filename on every new cut — never overwrite the scene just rendered.** The scene's
filename is what the user sees in their Idomoo library (the upload is named `basename(<scene>)`),
and the MP4 takes its name from it too. Keep the descriptive stem and add the version so the
library sorts them together: `spring_sale_v1` → `spring_sale_v2` → `spring_sale_final` (`_rc`,
`_15s`, `_9x16` — whatever names the change). Overwriting clobbers the previous MP4 and removes
the way back to a cut the user preferred. State the new filename when reporting a render.

**Version history is automatic — use it.** Every `compile`/`render` snapshots the scene into
`.strata/versions/` beside it (unchanged content makes no new version), and every `.idm` carries an
embedded version stamp (`strata.meta` — version, parent, scene hash; survives the Idomoo exporter,
`strata inspect` prints it). So: on "redo / go back", run `strata versions <scene>` and name the
version being continued from; to restore one, `strata revert <scene> --to N` snapshots the current
state first, so revert never loses anything (`-o other.json` branches instead); and a bare `.idm`
tells `strata inspect` which version it is and what it descended from. A previous cut is never gone
— check `strata versions` before telling a user otherwise. Details: [format.md](format.md).

## 6. Render

`strata render scene.json --library "<id>" -o out.mp4`.

- **Library — ask before the first render, never pick one.** Renders land in the user's Idomoo
  workspace and stay there: run `strata library list`, offer reuse or create (`library create` is
  get-or-create), persist the printed id, pass that same `--library <id>` every time. `render`
  refuses to guess: [commands.md](commands.md), *Libraries — ask, never pick*.
- Renders take minutes — run them in the background and report the `video_url`/`poster_url`.
- Only if the user asks for the scene to be **tagged** (a reusable template / catalog entry, not a
  one-off): add `--tags manifest.json` here and on `compile`, after reading
  [tagging.md](tagging.md). The manifest rides inside the `.idm`, so the library copy is
  self-describing. Both vocabularies are closed — tags are looked up, not invented.

## 7. Review the rendered MP4

A poster frame proves composition; it proves nothing about time.
`strata snapshot scene.json --library <id>` (a fast poster-only frame, cheaper than a full MP4)
is the check *before* the render; after the first render, review the MP4 itself, per
[review.md](review.md):

```bash
strata review out.mp4 --scene scene.json [--reference ref.mp4] -o review/
```

It detects cuts, writes a contact sheet, frames ±3 around every cut and settle, freezes, loudness,
motion energy per shot, and a timecoded `report.md`. Open it, watch the MP4 the four ways
[review.md](review.md) names, put the measured tokens beside the four declared numbers, and fix
every must-fix, citing the timecode. Compiling, validating, or using advanced features (3D,
camera, `.jet`, occlusion) is never itself a reason to approve.

Debug with `--vasco` or `strata inspect out.idm`.

## Definition of Done

- The message lands in the first 3 seconds, every shot earns its place, and every move has a job.
- **The render's measured shot-length range (`shortest`, `longest`, `rhythmRegularity`), energy,
  stillness and loudness match the four declared numbers**, or the report says which one moved and
  why. A piece that drifted back to the middle is not done, and neither is one that hit its average
  by cutting every shot at it.
- Text legible muted and inside the safe area — by placement, panel or grade, not by a scrim
  patched under it.
- `validate` is clean, or each warning is justified in the report.
- Every layer name unique across every comp — and no `⚠ renamed … duplicate layer name(s)` line
  was accepted. The compiler renames duplicates (`label`→`label_2`), which changes the
  personalization key, so the scene is fixed rather than the rename accepted.
- Clips outlast their slots; nothing loops to fill time; motion blur on every moving layer and
  `"motion_blur": true` on every animated camera; settles land; cuts sit on the audio.
- `strata review` run on the final MP4 and every must-fix fixed.
- The scene filename is versioned and the library id was the user's choice.
