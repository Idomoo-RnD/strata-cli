# Before you start — the four checks and the routing

The four questions that change what the job actually is, and how to find the references written for this brief.

Part of the production workflow — the index, and every other part, is in [workflow.md](../workflow.md).

## The four checks that change what the job is

Run these first; each one turns the request into a different job (the one-line version is in
[SKILL.md](../SKILL.md#before-authoring--four-checks)).

- **A0 · Material supplied?** A storyboard image, a script, a shot list, reference sheets, footage,
  a voice — read [intake.md](intake.md) first and treat that material as the brief. Transcribe it
  into the shot table (scenes, timecodes, first/last frames, dialogue, the notes verbatim), route
  each scene to its generation mode, and present *that* as the storyboard with the execution plan
  attached. A supplied storyboard is executed, not redesigned. Whenever two or more clips share a
  person, product or set — or consistency is asked for — build the
  [production bible](production-bible.md) before the first clip: one sheet and one canonical
  identity block per principal, reused byte-identical in every prompt.
- **A · Layout and motion not dictated?** Then four references do most of the work of making the
  piece *designed* rather than assembled: [anti-slop.md](../craft/anti-slop.md),
  [layouts.md](../layouts.md), [video-layouts.md](../craft/video-layouts.md) and
  [motion-design.md](../craft/motion-design.md). Without them the default is a flat fade-and-slide
  slideshow at the middle of every range, which is the failure this skill exists to prevent. Pick
  a named layout, apply the motion techniques, and run the "web-animation tells" checklist before
  shipping.
- **B · An edit, not a design job?** Footage handed over to cut, trim, join, crop/reframe, speed
  up, loop, mute or swap audio is mechanical: do it with ffmpeg per
  [video-editing.md](../shoot/video-editing.md) and hand back an MP4 — no scene. Titles are text layers, so
  they get easing and animators; burning them in with `drawtext` reads as a watermark. Graphics on
  top of footage → edit the clip first, then use it as a `video` layer.
- **C · Brand first.** Check for `.brand/brand.md` at the start of every job. If it exists, read it
  and author everything from it (canvas, palette, type ramp with real font paths, components,
  motion language) — that is what makes a user's videos a set instead of unrelated one-offs. If it
  does not exist and brand material was supplied (logo, brand book, style guide, existing videos, a
  website, a Figma file), or the user says "on brand" / "like the last one" / a series, offer to
  build it first — *"want me to capture this as a brand document so every video matches?"* — write
  it to `.brand/brand.md` from their assets and get sign-off before storyboarding:
  [brand.md](../brand/brand.md).
- **D · From Figma?** Read [figma.md](../brand/figma.md) first; the mapping is full of traps. Figma boxes are
  canvas-absolute (subtract the frame origin) and web-sized (one uniform scale factor, never stretch
  x and y differently); `"font": "Inter"` does not compile (fonts are real `.ttf`/`.otf` paths);
  icons, vectors and gradients are exported to PNG; `visible:false` nodes are skipped; Figma's
  repeated names (`Rectangle 1`, `Text`) collide — the compiler auto-renames them, silently
  changing the replacement keys — so every layer gets a unique name. Then
  compare `strata preview --grid` against the Figma screenshot before rendering, and author motion
  on top — an imported frame is a layout, not a video.

## Routing — which references matter for this brief

**Write the brief first, then route the brief.** `strata brief init "<the request, verbatim>"`
writes `BRIEF.md` — the one artifact every later step reads: the request as the user said it, the
reconstruction line routing matches on, the four numbers still to declare, the constraints
(material, brand, recipe, library, attended or not, the render budget) and the read list. Fill its
blanks before the storyboard; an unattended run decides them and records the reason in
`decisions.md`. It is written once and **amended** as the direction adds a kind — never rewritten
— and re-routed with `strata route BRIEF.md`, which matches on the reconstruction line, not the
whole file. A brief kept in one place is what lets a reviewer, a second render, or the next piece
in the series answer "what was this supposed to be?" without re-reading the conversation.

`strata route "<the brief in a sentence>"` (or `strata route BRIEF.md`) prints the read list; the
same map is *What to read for this brief* in `SKILL.md`. **Each matched kind's group opens with its
route page**, `routes/<kind>.md` — the landing for that kind: what it is, what to settle before
the storyboard, where it sits on the range, the files in reading order with what to take from
each, the kind's traps as pointers, and the acceptance test. Read the page, then the files it
points at. Run `strata route` a **second** time once the concept exists — a short brief cannot say
"kinetic type", "music-led" or "thriller", but the direction chosen for it can. (*Measured:* a
"super AAA motion design" brief routed to *premium* only; the piece then became type-led and
`motion/03-typography.md` was never opened — the premium row now includes it, since premium
pieces are so often carried by one word.)

**Before asking the user anything, check what they already answered.** `strata prefs list` holds
the answers that repeat across projects — library, aspect, fps, loudness target, attended or
unattended, a brand document, a voice, the working music prompt, the render budget — recorded
with `strata prefs set <key> <value>` the moment a user gives one. `brief init` fills the brief
from them; `render` falls back to the `library` preference when neither `--library` nor a project
`.idm-library` names one. A preference the user set is not a guess; a question they have already
answered is not asked again. **A piece in a series starts from the last one:** `strata recipe save
<name> --from <that project>` keeps its brief, storyboard, decisions and brand document by name;
`strata recipe use <name> --into <new project>` copies them into `.recipe/<name>/` (and installs
the brand document when the new project has none) to read before the storyboard. A recipe fills
answers, not approvals — the storyboard still gets its sign-off, and `BRIEF.md` names the recipe.
