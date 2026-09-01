# The craft — how a great video is made

The craft manifesto: story and shots, composition, occlusion and tracking, 3D and camera,
motion principles, kinetic type, transitions, atmosphere, rhythm and sound, polish, and the
design and layout rules. `SKILL.md` carries the one-screen spine and routes here. Read this when
planning any piece the user has not fully dictated — it is what makes output *designed* rather
than assembled.

**How to read this file.** Nothing below is an engine fact; the engine facts are in
[traps.md](traps.md) and [format.md](format.md) and those are law. Everything here is what a good
studio does when nobody has a stronger idea for *this* piece, **written for the middle of the
range in SKILL.md, *Place the piece on the range***. Every number below therefore has a hidden
first step: what did this piece declare? A 1.0 s-mean piece and a 6.0 s-mean piece take opposite
values out of the same bullet. A default overruled for a reason you can state in a line is better
craft than a default obeyed; the brief's own words win over everything here.

## Contents

- [Part 1 — How I make great video (this is the job)](#part-1--how-i-make-great-video-this-is-the-job)
  - [Story & concept first](#story--concept-first)
  - [Think in shots (cinematography)](#think-in-shots-cinematography)
  - [Compose the frame — layouts, not just fullscreen](#compose-the-frame--layouts-not-just-fullscreen)
  - [Defaults are not choices — the anti-slop rule](#defaults-are-not-choices--the-anti-slop-rule)
  - [3D & camera — VASCO's superpower (use it)](#3d--camera--vascos-superpower-use-it)
  - [How much moves — set by the declared energy](#how-much-moves--set-by-the-declared-energy)
  - [Motion principles (the fundamentals)](#motion-principles-the-fundamentals)
  - [Kinetic typography](#kinetic-typography)
  - [Transitions between shots](#transitions-between-shots)
  - [Depth, light & atmosphere](#depth-light--atmosphere)
  - [Rhythm & continuity](#rhythm--continuity)
  - [Polish & anti-patterns](#polish--anti-patterns)
  - [Craft check (before I call it done)](#craft-check-before-i-call-it-done)
- [Part 2 — Design & layout (so it reads clean and on-brand)](#part-2--design--layout-so-it-reads-clean-and-on-brand)

## Part 1 — How I make great video (this is the job)

### Story & concept first
- **Find the arc.** Beginning → tension → resolution — every piece, even 8 seconds, has one. Name the single message the viewer should leave with; if it won't fit in a sentence it is not ready.
- **Find the tension.** Product vs. the old way, user vs. friction, before vs. after. Tension holds attention.
- **Align before building.** The concept goes past the user first — re-cutting an idea is cheap, re-rendering is not.
- **Shot length comes from the declared range, not from a habit and not from an average.** The measured reference means and the spread inside them are in `SKILL.md`, *Place the piece on the range*. Give each shot the length its job needs — hold the one carrying the idea until the viewer has had it, cut a transit or an impact before the eye settles — and let the mean fall out. Cutting every shot at the mean is the middle of the range one level down: twelve shots of equal weight, no hero. Each shot still has a single job (hook → value → proof → CTA); when two things fight for the eye, stagger them, or let a montage or split-screen earn the second idea — say which.

### Think in shots (cinematography)
A scene is a sequence of deliberate shots. **Which shot, why this cut, and why here** is the editorial layer — **[editing-director.md](editing-director.md)**, read at storyboard time. For each shot I decide the framing and camera:
- **The moves.** Establishing → push in (wide to set the world, then in on the action) · hard cut or rapid intercut between two things in tension · a follow shot on a cursor, graph line, character or product · a reveal that builds the image piece by piece. Show, don't tell — titles only when they beat pictures.
- **Name the shot size.** Wide (establish) · medium (the action) · close-up (the detail or the face) · insert (the hand, the product, the number). A sequence that never changes size reads as one shot; the *double contrast* rule from video-generation.md applies to authored scenes too — change size **and** camera character at every cut.
- **Vary the shots** — sameness kills attention. And **keep it in a real context** (a desk, a phone UI, a place); elements floating in the void read as unfinished.

### Compose the frame — layouts, not just fullscreen
My default is **not** "fullscreen image/video with text on top." Every layer has a `box`, so I compose deliberately and vary it across shots:
- **Split, grid, framed and multi-media frames** — media inside a designed backdrop, two videos side by side, PiP, a video on a third of the frame with solids filling the rest. Solids and shapes are design elements, not backgrounds. The named layouts are [layouts.md](layouts.md); composing *with* footage is [video-layouts.md](video-layouts.md).
- **OCCLUSION — the hero passes IN FRONT of the type (the signature ad move).** A transparent `.jet` of the product/subject physically covers a giant wordmark as it moves. **All four must hold** or it is the wrong tool: exactly ONE hero element · large type it can physically cross · a piece where the hero is *meant* to dominate (product/brand, not explainer or data story) · movement that carries it **across** the type. Otherwise a plain layout — a pass that doesn't overlap the type is cost for nothing. Propose it in the storyboard; pipeline, elevations and the two builds are [video-layouts.md](video-layouts.md) §6.
- **Tracking — text ON a surface, or a label FOLLOWING a subject.** `strata track` writes the keyframes: `--comp WxH` tracks a **flat surface** (sign, screen, poster, wall) and emits a `corner_pin` so text sits *on* it in perspective; `--point x,y` follows a **moving element** and emits `position` keyframes so a callout travels with it. **Tracking is ALWAYS the built-in `strata track`, never by hand** — eyeballed or interpolated keyframes drift, jitter and read pasted-on every time; a weak match is fixed in the *footage* (re-prompt the clip, constrain the camera move), never by falling back to manual tracking. Offer it unprompted when footage has an obvious flat surface or one clear moving subject — *"want the headline painted onto that sign?"* **And design the clip around the effect**: for a surface, a large blank rectangular face at a three-quarter angle filling much of the frame, moved with a **slow lateral dolly or push-in — never an orbit** (measured: dolly tracked 144/144 frames, an orbit fell to 78% and the text bled off the edge); for a follow, one high-contrast subject on a plain background with the **displacement stated explicitly** (image-to-video models scale or rotate instead of translating). Recipe, flags and snippets: [recipes.md](recipes.md).
- **Multiple shots in one scene:** cut between framings/elements within a single scene using layer `start`/`duration` (and sub-comps) — a scene isn't one static composition.
- **Use the whole frame — don't leave a dead third.** Content clustered top/middle over an empty bottom third is the commonest tell here; centre the cluster, or run header / content / CTA on the bottom safe line ([layouts.md](layouts.md)). Landscape needs that deliberate vertical balance; 9:16 distributes naturally.
The strongest videos change their composition shot to shot; I avoid repeating the same fullscreen-media-plus-caption frame.

### Defaults are not choices — the anti-slop rule
Agent-composed frames cluster around moves that appear regardless of subject, and so do agent-composed *tempos*. The named list, the test and the pre-render checklist are [anti-slop.md](anti-slop.md), mandatory before any storyboard whose look is yours to decide. The test, per decorative element and per number: would I have produced this for any similar brief? Then it is a default — replace it with a choice from *this* subject's world.

### 3D & camera — VASCO's superpower (use it)
- **`is_3d: true`** on layers + a **`camera`** layer (`fov`/`field_of_view`, animated `position`/`zoom`/`rotation`) → genuine dollies, push-ins, orbits. (There is no depth-of-field on the camera — a rack-focus is an animated `blur` effect on the layers, recipes.md "Depth-of-field focus pull".)
- **Parallax with depth:** give layers different **z** and move the camera — near and far layers drift at different rates. Instant cinematic depth. **The camera is a measured pinhole** (format.md, Camera): `scale = focal / (z_layer − z_camera)` with `focal = (h/2)/tan(fov/2)`, so **park the camera at `z = −focal`** (−935 for 1080p at fov 60; −623 at 720p) or z=0 layers render small. **Negative z comes TOWARD the camera and grows; a far background goes at POSITIVE z** and is oversized by `(z_bg − z_cam)/focal`. Position semantics: with **no anchor**, `position:[0,0,z]` is a pure depth offset; **with an anchor**, every position is absolute, so write `[anchorX, anchorY, z]` — `[0,0,z]` on an anchored layer flies it off-frame (verified).
- **Camera moves, not layer moves:** when several elements travel together, move the **camera** (or a parent comp). A slow push under a settling title reads premium. Rotate 3D layers on X/Y for flips; stage elements in depth so a push travels *through* them.
- See the **Camera** and **3D** keys in `format.md`. Reach for depth/camera when a shot feels flat **and the move has a job** — a frame that is *designed* flat (a graphic end card, a typographic statement, a locked product shot) is a decision, not a flaw, and a camera drift added "for life" is the templated look.

### How much moves — set by the declared energy
- **Every move has a named job; how many moves there are is the position's decision.** Each answers "what is this doing for the shot?" (reveal, emphasis, transition, continuity, rhythm) — a move with no nameable job is decoration and comes out. But *quantity* is not a constant: a world-class quiet film measured **stillness 0.57, energy 1.06**, over half its frames essentially still; an action reel measured **stillness 0.00, energy 11.43**. Hit the ratio the storyboard declared. A frame still because nobody chose reads as a bug; the same frame, declared and composed, is often the strongest in the piece.
- **Motion ideas per shot and transition families scale with energy too.** One dominant idea and two families is the middle. A declared-high piece carries three or four at once and a third family — that is what energy 11 *is*, and rationing it there is how a high-energy brief comes back at energy 3; a declared-quiet piece runs one family and shots with no motion idea at all. Name the count either way.
- **Images are never still — and the treatment depends on FIXED vs PERSONALIZED.** A fixed image (generated or supplied, the same for every viewer) becomes a **video clip** (`generate video --first-frame`/`--ref-image`). A **personalized media slot** (`hero_photo`, a product shot swapped per viewer) must stay an **`image` layer** — the API substitutes a still — so its motion is at the **layer level**: a slow **Ken-Burns** (scale + position on an anchored layer, `fit:"fill"`, anchor at the box centre so any replacement zooms correctly), or graphics building over it. Ken-Burns is the *right* tool there, not a fallback.
- **A held shot still moves — what holds is the camera, not the frame.** Stillness is a ratio across the piece, never a licence for a dead shot on the beat it is about: the reference film's 4.04 s and 5.96 s takes measure 0.16, a frozen payoff measures 0.88. Before writing a long shot, name what carries its time, from what this engine has: a subject moving inside the generated clip (the shot is footage, not a still) · a slow push on an animated `camera` layer · a texture drifting under an alpha matte while the glyphs stay put (*Textured type*, below) · a `blur`, `glow` or colour `overlay` keyframed so light moves across a surface · secondary elements still settling after the primary landed (their keyframe ends offset past the hero's) · a mask travelling across a plate. **The cheapest thing that matches the reference is a continuous slow push — 2–3 % of scale or camera across the entire hold**, because the professional showreel's longest hold has no frame at all below 0.5 frame-to-frame difference (mean 2.99). **The end card is a hold like any other:** [blueprints.md](blueprints.md) says hold it 2–3 s past the last move — that sets its length, it does not license a frozen wordmark on the frame the viewer leaves on. **The floor: no shot ends on a value that stopped changing before the cut.** If the only candidate is a slow fade the shot is a still — but cutting the payoff short is the failure this bullet exists to prevent, so give it something to do first.
  **Size the push in px/frame, not percent — a percentage is radial and a centred element barely moves.** `px_per_frame = r × p / (duration × fps)`, where `r` is the element's distance from the push's centre. *Measured:* an end card built to a 2.5 % push over 2.75 s at 720p moved 0.24 px/frame at the frame edge and **0.16 px/frame on the wordmark itself** — `review` returned two freezes, 92 % stillness and 28 of 64 frames under 0.5. **Aim for ≈1 px/frame on the thing that must look alive**, which on that hold is 8–10 %, not 2–3 %; and scale the percentage to *the element's* radius, because a centred wordmark sits at `r ≈ 0` and a push of any size leaves it still — for that element the answer is **not a push at all**. Translation has its own arithmetic and no dead centre: `px_per_frame = distance / (duration × fps)`, so ≈1 px/frame over a 2.75 s hold at 24 fps is a **66 px drift** — a light pool crossing, a texture sliding under a matte, a plate easing sideways behind locked type. Reach for translation whenever the thing that must breathe sits near the centre, and for a push when it is the whole frame that should close in. Two corollaries: **the metric follows edges, so it is luminance-dependent** — the same card measured *worse* after gaining real footage at `blur 24`, which erased the edges whose travel it could see; and **a push is not the only answer** — a travelling light pool, a drifting matte texture or a settling secondary all move real pixels where a radial push does not reach.
- **Read time is a floor on the copy, not on the cut.** ~0.5 s + ~0.3 s per word, and text animates out only after it can be read. On a 1.0 s shot that floor buys two words, so a fast piece **cuts the copy to fit the cut** — it does not slow the whole piece to fit the copy, and it does not put a sentence on a 1 s shot. Copy that will not fit at the fast end argues for *that shot* being one of the held ones; on a 6 s hold the floor is met long before the shot ends, and the rest of the hold is the point.

### Motion principles (the fundamentals)
- **Disney basics:** anticipation, **ease in/out** (nothing starts or stops instantly), follow-through & overlap, squash & stretch, exaggeration, staging. In full: [motion/01-foundations.md](motion/01-foundations.md).
- **Timing is *when*; spacing is *how* the value distributes across the move (the easing).** Amateurs leave timing uniform and motion linear; I vary both.
- **Which ease for which job** — the vocabulary, with the curve per emotion and per material, is [motion-design.md](motion-design.md) §2. `linear` only for continuous loops; `hold` to freeze between keys; a cubic-bezier `[x1,y1,x2,y2]` for anything the named curves miss.

### Kinetic typography
- **Per-character / per-word animators** make text feel alive — words rise and fade in, letters track in, cascades. That is for **headlines and kinetic type**; **captions** are the opposite case and appear in readable chunks, because reading speed on a phone wins ([editing-director.md](editing-director.md)) — decide which a text layer is before animating it. Use `animators` with `ranges` (`based_on: words|characters|lines`, `shape: ramp_up|…`), in **percentage** units so any string length cascades. (Syntax in `format.md` Text; copy from `recipes.md`.)
- **Type with intent:** big where it matters, generous tracking for labels, tight for impact. Animate the meaning (a number counts up; a key word punches in).
- **Textured type** — a hero word filled through a track matte (gradient, scrolling stripes, gold-shine sweep, footage in letterforms). **Textures are generated, not sourced** — `strata generate image` as a full-frame no-subject texture, or a script for geometric ones. One hero word per shot, never body text. [recipes.md](recipes.md) §1 "Textured text".

### Transitions between shots
- **Match-cut / continuity:** carry a shape, colour or motion vector across the cut. Whip-pan, light-leak, iris/clock wipe, scale-through — with intent, never as decoration ([motion/04-transitions.md](motion/04-transitions.md)).
- **A flash frame goes on the two frames *before* the cut, not on it.** Placed on the cut it covers the incoming title's entrance and the title reads as already there (*measured* on a beat-cut spot); placed just before, the cut lands on the flash's decay and the entrance is seen.
- Cutting on a **camera move** or an audio beat hides the seam and feels designed.
- **A move comes from what caused it, and leaves the way it came.** A panel that arrives because a word landed enters from that word; a card that exits to make room for the next slides toward where the next one comes from; a reveal grows out of the thing that triggered it rather than fading up in place. The viewer reads cause from direction, so an entrance with no origin reads as an element being placed rather than an element arriving — and it costs nothing to point it at something.

### Depth, light & atmosphere
**A reference frame is not evenly lit and not evenly filled; ours are both.** Four decisions, made
at storyboard time and checked by putting `contact.png` beside a reference frame — this is the
picture half of the range the four numbers set:
- **Light comes from somewhere.** Name the key's direction and colour per shot and keep it across
  the piece. Every authored surface of ours is one flat fill with a hard edge — a colour band, an
  end-card ground, a wordmark — lit by nothing. A gradient across the solid, a warm bounce on the
  shadow side, a specular sheen on the edge the key catches, a shadow where two planes meet, a
  falloff into the corners: no render cost, and the whole difference.
- **Type sits at a depth, not on the glass.** A reference title carries the value and haze of the
  objects at its distance and something crosses in front of it; ours is 100 % white at one weight
  over every plate. **Four tells prove a wordmark is an alpha-matte cut-out**, all found on our best
  piece's end frame — 22 % of its runtime, the frame a client judges on: no contact shadow · a lit facet of the material behind it truncated by a letter edge instead of
  breaking across it · two light directions in one frame · a colour band inside the letters running
  perfectly horizontal across all six characters, which proves the fill is a rectangle behind a mask
  rather than something projected onto the surface. **Five moves put the title in the shot:**
  1. **Colour from the plate** — sample its light, let distance thin it, never 100 % white over a graded frame; a colour `overlay` in `softlight` or `color` grades the type *with* the shot.
  2. **A real contact shadow** — a `shadow` layer style whose `angle` is the plate's key direction,
     plus a soft blurred dark solid on the surface the type meets
     ([video-layouts.md](video-layouts.md) §6, *Ground it*). One key per frame: every shadow,
     authored or filmed, points the same way.
  3. **A fill that follows the surface** — animate the matte texture along the plate's light
     direction and let its band bend where the material behind it bends. A level band across every
     glyph is the tell above ([recipes.md](recipes.md), *Textured text*).
  4. **Something crosses in front** — occlusion (above); a `.jet` over the type is the strongest proof the words are in the scene, not on the glass.
  5. **Match grain and falloff** — the plate's depth-of-field falloff (an animated `blur` behind it,
     a touch on the type when it sits back) and one grain pass over both. A perfectly sharp, clean
     wordmark on a filmed frame is the last thing that gives it away.
- **Give a frame away.** Reference frames hold the subject at a few per cent and let the sky do the
  rest; ours centre a large subject in nearly every shot. The small empty frame is what makes the
  next close-up land.
- **Let the look change between beats.** A 30 s reference runs cool city → blown warm → white-out;
  ours grades all thirty seconds the same.
- Glow, shadow, blur, overlays, a vignette (feathered ellipse mask), a colour grade — all available. Pick one atmospheric effect as the piece's signature: stacking glow + flare + grain + vignette is what a generator does when nobody chose ([anti-slop.md](anti-slop.md)). A deliberately maximal piece can stack them — say so in the bible. A colour overlay for a coherent grade is not an effect and is always fine.
- **Motion blur is the cheapest thing that separates "rendered" from "a slideshow"** — without it every fast move strobes and the piece reads as simple even when the design is good. The compiler turns it **on for every visual layer** (text, solid, media, sub-comp), so I never have to add it and I do not switch it off; raise comp `shutter_angle` (1–1.3, default 0.5 frames) for fast moves — **except on a piece with a native occluder**: at 360° a solid that is meant to hide type goes translucent at its leading edge and the word leaks through (*measured*, cost a render); keep the default there, or blur the occluder's motion some other way.
  **⚠ The camera is the exception, and it is the one that costs the shot.** A `camera` layer's `motion_blur` defaults to **`false`** in the schema and the compiler does not override it — so on a push, dolly, orbit or whip the LAYERS are not moving, the camera is, and the whole frame renders perfectly crisp while every layer in it is nominally "blurred". Every animated camera gets **`"motion_blur": true`** written on it. `strata validate` warns when one doesn't.

### Rhythm & continuity
- **Cut/hit to the audio — with `strata beats`, never by guessing.** `strata beats music.mp3`, then **snap cuts, entrances and accents to the returned onsets**; hand-timed motion against music always reads slightly off. `--fps <scene fps> --bands 12` adds the per-frame envelope for audio-reactive graphics. For narration, size each scene to the **returned duration**. Transitions ~0.3–0.5s. [generative-fx.md](generative-fx.md).
- **Choose the mix target before the bed is generated** — an integrated LUFS for where the piece plays, and a named gap between its loudest and quietest beat, both on the storyboard ([music.md](music.md), *Decide the mix*). A piece with no target lands in the middle whatever the brief (*measured:* three pieces, three briefs, −16.9 / −18.7 / −16.8 LUFS), and a piece with no gap has no impact to land. The dB levels below are balance between layers, not delivered loudness.
- **Sound design is part of the cut, not a garnish.** Three layers — voice, bed, **SFX**: a whoosh on the transition (peak on the cut), a hit when the logo lands, a riser into the climax, ticks on counters. `generate music "TrackType: SFX, …" --duration 1` makes them (measured: exact length, correct envelope) and an `audio` layer's `start` places them ([music.md](music.md), *sound design pass*). Levels: VO 0 dB · bed −10…−12 with `ducking` · SFX −3…−8. `volume` cannot be keyframed and audio has no trim-in — fades and cut-ins are ffmpeg, before import.
- **Vary energy across the declared range, and vary it hard.** A quiet shot makes the next loud one hit harder. Design the whole arc — intro (tone) → body (escalate) → climax (biggest move/stat) → resolve (logo/CTA) — and remember that averaging to the target is not hitting it: the reference showreel's 1.49 mean sits over shots swinging far above and below it. A flat profile at the right average is still flat, and the same is true of the cut.

### Polish & anti-patterns
- **Polish:** nothing moves linearly; entrances settle rather than pop (overshoot only where the material earns it — rigid, premium things stop clean); elements have weight; one focal point per frame.
- **Amateur tells:** everything fading in the same way at the same time; clutter with no hierarchy; looping a clip to fill time (a visible loop reads cheap — cut to another shot or `playback_mode: "hold"`); decorative motion with no meaning. The rest: [motion-design.md](motion-design.md), *web-animation tells*.

### Craft check (before I call it done)
After render I look at the poster (or extract a frame) — compile success ≠ good frame — and ask:
- Is the **message** unmistakable? Does **every shot** earn its place?
- **Did it land where it said it would?** Measure the four numbers on the render and put them beside the declared ones — including whether the longest shot is the one named to carry the idea. A piece that declared 1.0 s / energy 9 and came back at 2.3 s / energy 3 did not get "a bit slower" — it defaulted, and the fix is the edit, not a tweak.
- Does the piece have **range** in the picture too — a dark frame and a bright one, a small subject and a big one — or is every beat the same size?
- Does the **pacing** let the key shots land? Would the **first three seconds** make someone keep watching?
- Does every move have a job? Do images move; is there depth/camera where the frame felt flat — and nowhere it was flat on purpose?
- The poster answers none of the temporal questions — easing, rhythm, cuts, settles and sound are judged on the **rendered MP4** in step 7's review, never on one frame.

---

## Part 2 — Design & layout (so it reads clean and on-brand)
The grid, the named layouts and the rules that make any frame read as designed — snap to the grid, no dead third, one focal point at ~2–3× the next, consistent gutters, three type sizes, one accent with one job — are [layouts.md](layouts.md), *Rules that make any layout read as designed*. What that file does not carry:
- **Stamp test:** glance at the frame — what did I see first? If it isn't the most important thing, fix size/contrast/colour/placement. Product + CTA win the first glance.
- **Compose like a frame of film, not a web page** — **headroom and lead room** for anything that faces or moves; the subject on a **third** when the frame has direction, **centred** when it is a statement. "Less is more" reads premium.
- **Colour:** brand atoms exact; the grade is set per beat, not once for the piece (*Depth, light & atmosphere*).
- **Typography:** two typefaces is the usual ceiling — vary weight, size and colour rather than adding a face, unless the piece is about type. Define fallback fonts; the font must cover every glyph used.
- **Contrast — text must stay legible over whatever is behind it.** A backing **matched to the background brightness**: near-opaque over bright footage (a 45% scrim over a white screen only makes grey; white text then washes out), light/low-opacity over dark. White-on-light and dark-on-dark fail; aim for ~4.5:1. When in doubt, snapshot and check the caption is crisp.
- **Safe areas & no overlap:** text within ~90% **title-safe**; **no two text/visual elements overlap while both are on screen** — real gaps on the grid, or separate them in **time** (stagger their `start`). `validate`/`compile` warn on overlaps and boxes off the frame; fix those before rendering.
- **Over a personalised image slot** legibility must survive whatever image arrives, and a scrim is the **last** answer — the ordered alternatives are in [anti-slop.md](anti-slop.md), *Text backing*.
- **Personalization (Idomoo's core):** every layer is an API-replaceable placeholder keyed by its **name**. Size text boxes for the longest value (`shrink`, `min_size`), `fit:"fill"` for full-bleed media slots, per-character animators for any string length, and time the personalized reveal **early but not at t=0** (videos start muted). Graphs are swappable images whose animation reveals whatever data arrives. Details: [personalization.md](personalization.md).
