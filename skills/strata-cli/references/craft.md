# The craft — how a great video is made

The craft manifesto: story and shots, composition, occlusion and tracking, 3D and camera,
motion principles, kinetic type, transitions, atmosphere, rhythm and sound, polish, and the
design and layout rules. `SKILL.md` carries the one-screen spine and routes here; every rule
and number below is unchanged. Read this when planning any piece the user has not fully
dictated — it is what makes output *designed* rather than assembled.

## Contents

- [Part 1 — How I make great video (this is the job)](#part-1--how-i-make-great-video-this-is-the-job)
  - [Story & concept first](#story--concept-first)
  - [Think in shots (cinematography)](#think-in-shots-cinematography)
  - [Compose the frame — layouts, not just fullscreen](#compose-the-frame--layouts-not-just-fullscreen)
  - [Defaults are not choices — the anti-slop rule](#defaults-are-not-choices--the-anti-slop-rule)
  - [3D & camera — VASCO's superpower (use it)](#3d--camera--vascos-superpower-use-it)
  - [Keep every frame alive](#keep-every-frame-alive)
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
- **Find the arc.** Beginning → tension → resolution. Every piece, even 8 seconds, has one. I name the single message the viewer should leave with — if I can't say it in a sentence, it's not ready.
- **Find the tension.** Product vs. the old way, user vs. friction, before vs. after. Tension holds attention.
- **Align before building.** I run the concept past the user first — re-cutting an idea is cheap, re-rendering a finished video is not.
- **One idea per shot.** A shot is ~1.5–4s with a single job (hook → value → proof → CTA). If two things fight for the eye, I stagger them.

### Think in shots (cinematography)
A scene is a sequence of deliberate shots. **Which shot, why this cut, and why here** is the editorial layer — **[editing-director.md](editing-director.md)**: the scene's job, point of view, information release, a rhythm curve, and a cut-decision gate that asks *emotion* first and *continuity* sixth. I read it when planning the storyboard and whenever a brief names a kind of video (commercial, explainer, testimonial, trailer-style, social cutdown, sports recap). For each shot I decide the framing and camera:
- **Establishing → push in.** Open wide to set the world, then move in on the action. Show, don't tell — titles only when they beat pictures.
- **Hard cut / rapid intercut** between two things in tension.
- **Follow shot** that tracks a cursor, a graph line, a character, a product as it moves.
- **Reveal** that builds an image or layout piece by piece.
- **Name the shot size.** Wide (establish) · medium (the action) · close-up (the detail or the face) · insert (the hand, the product, the number). A sequence that never changes size reads as one shot; the *double contrast* rule from video-generation.md applies to authored scenes too — change size **and** camera character at every cut.
- **Vary the shots** — sameness kills attention. And **keep it in a real context** (a desk, a phone UI, a place); elements floating in the void read as unfinished.

### Compose the frame — layouts, not just fullscreen
My default is **not** "fullscreen image/video with text on top." Every layer has a `box`, so I compose deliberately and vary it across shots:
- **Split & grid layouts:** half/half (media on one side, a text/colour panel on the other), thirds, 2×2 grids, a sidebar + main.
- **Framed media & product slots:** a media placeholder sized and positioned *inside* a designed backdrop — colour panels, shapes, a device/phone frame, a card. Perfect for product shots and personalized photos.
- **Multiple media at once:** two videos side by side, picture-in-picture, or a video occupying a third of the frame with colour shapes/solids filling the rest.
- **Solids & shapes are design elements**, not just backgrounds — colour blocks, bars, cards, and panels structure the layout and frame the media.
- **OCCLUSION — the hero passes IN FRONT of the type (the signature ad move).** A transparent `.jet` of the product/subject sits on top of a giant wordmark, price or headline, physically covering it as it moves — the shoe sweeping across the brand name. **When to propose it — all four must hold**, or it is the wrong tool: (1) there is exactly ONE hero element, (2) there is large type it can physically cross, (3) the hero is *meant* to dominate the frame (a product/brand piece, not an explainer or a data story), and (4) the movement genuinely carries the hero **across** the type. If any of those is missing, use a plain layout — an occluding pass that doesn't overlap the type is cost for nothing. When they do all hold I propose it in the storyboard ("want the shoe to pass in front of the brand name?") rather than just building it. Pipeline: generate the hero on a **solid green** background → image-to-video with the motion stated → **chroma-key** (`strata jet --method chroma`; `matte` only if the hero is a person — it is a human model and finds nothing in a product) → `.jet` → top layer over the designed type. Creative elevations (hidden text-swap while covered, depth sandwich, grounding shadow, type that reacts to the pass) and the two builds: [video-layouts.md](video-layouts.md) §6.
- **Tracking — text ON a surface, or a label FOLLOWING a subject.** `strata track` analyses footage and writes keyframes: `--comp WxH` tracks a **flat surface** (sign, screen, poster, wall) and emits a `corner_pin` so text sits *on* it in perspective; `--point x,y` follows a **moving element** (plane, car, product) and emits `position` keyframes so a callout travels with it. **Tracking is ALWAYS done with the built-in `strata track` — I NEVER track by hand.** Not by extracting a few frames and eyeballing the subject's coordinates, not by interpolating my own keyframes between guessed positions, not by writing a one-off tracker script: hand-tracks drift, jitter and read pasted-on, every time. `strata track` measures every frame and smooths the path; if it reports a weak match I fix the *footage* (re-prompt the clip, constrain the camera move), never fall back to manual tracking. I offer these unprompted when footage has an obvious flat surface or one clear moving subject — *"want the headline painted onto that sign?"* **And when I generate the clip myself, I design the image and the motion prompt around the effect**: for a surface, a large blank rectangular face at a three-quarter angle filling much of the frame, moved with a **slow lateral dolly or push-in — never an orbit** (measured: dolly tracked 144/144 frames, an orbit fell to 78% and the text bled off the edge); for a follow, one high-contrast subject on a plain background with the **displacement stated explicitly** (image-to-video models tend to scale/rotate instead of translating). Full recipe, flags and the paste-in snippets: [recipes.md](recipes.md).
- **Multiple shots in one scene:** cut between framings/elements within a single scene using layer `start`/`duration` (and sub-comps) — a scene isn't one static composition.
- **Use the whole frame — don't leave a dead third.** The most common amateur tell in my own output is content clustered top/middle with an empty bottom third. I balance the canvas: vertically centre the cluster, OR use a clear top/bottom structure (header up top, content centred, a **footer/CTA anchored on the bottom safe line ~88–92% down**). I fill empty space with intent — a subtle background gradient/shape, a baseline rule, or by scaling content up — never leave a flat empty band. (9:16 distributes naturally; landscape needs deliberate vertical balance.)
The strongest videos change their composition shot to shot; I avoid repeating the same fullscreen-media-plus-caption frame.

### Defaults are not choices — the anti-slop rule
Agent-composed frames cluster around moves that appear regardless of subject: a scrim behind every text block, a rule under every title, a colour bar on the side of a card, glow on the hero word, light-leak + grain + vignette as "film", an eyebrow label over every headline, 01/02/03 over a list that is not a sequence, `outBack` on body text, cream+terracotta or black+one-neon when no brand exists. Each is legitimate for some briefs; **none is legitimate as a reflex**. The full named list, the test and the pre-render checklist are in **[anti-slop.md](anti-slop.md) — mandatory, I read it before every storyboard.** The short form:
- **The test, per decorative element:** would I have produced this for any similar brief? Then it is a default — cut it or replace it with a choice from *this* subject's world, and say what changed.
- **The brief's words always win.** A scrim, rule or glow the brand or user asked for is a choice; I make it exactly.
- **Spend boldness in one place** — one signature element, everything else quiet. **Remove one thing before rendering**; if the frame still reads, it was decoration.

### 3D & camera — VASCO's superpower (use it)
This is what separates a flat template from a film. VASCO layers can be 3D and there's a real camera:
- **`is_3d: true`** on layers + a **`camera`** layer (`fov`/`field_of_view`, animated `position`/`zoom`/`rotation`) → genuine dollies, push-ins, orbits. (There is no depth-of-field on the camera — a rack-focus is an animated `blur` effect on the layers, recipes.md "Depth-of-field focus pull".)
- **Parallax with depth:** give layers different **z** and move the camera — near and far layers drift at different rates. Instant cinematic depth. **The camera is a measured pinhole** (format.md, Camera): `scale = focal / (z_layer − z_camera)` with `focal = (h/2)/tan(fov/2)`, so **park the camera at `z = −focal`** (−935 for 1080p at fov 60; −623 at 720p) or z=0 layers render small. **Negative z comes TOWARD the camera and grows; a far background goes at POSITIVE z** and is oversized by `(z_bg − z_cam)/focal`. Position semantics: with **no anchor**, `position:[0,0,z]` is a pure depth offset; **with an anchor**, every position is absolute, so write `[anchorX, anchorY, z]` — `[0,0,z]` on an anchored layer flies it off-frame (verified).
- **Camera moves, not layer moves:** when several elements should travel together, move the **camera** (or a parent comp), not each layer. A slow camera push under a settling title reads premium.
- **3D card flips / space:** rotate 3D layers on X/Y for flips and turns; stage elements in depth so a push-in travels *through* them.
- See the **Camera** and **3D** keys in `format.md`. Reach for depth/camera when a shot feels flat **and the move has a job** — a frame that is *designed* flat (a graphic end card, a typographic statement, a locked product shot) is a decision, not a flaw, and a camera drift added "for life" is the templated look.

### Keep every frame alive
- **Something is always moving — and every move has a named job.** Except a deliberate held shot, the camera, an element, or a transition is in motion — drift, zoom, build. A truly static frame *with nothing alive and no reason to be still* reads as a bug. But quality is not activity: each move must answer "what is this doing for the shot?" (reveal, emphasis, transition, continuity, rhythm) — a move with no nameable job is decoration and comes out. **Stillness, a hard cut and flat graphic design are valid, often premium, decisions**; a held frame that is composed is stronger than a frame that drifts because nobody chose. One dominant motion idea per shot; at most two transition families per piece.
- **Images are never still — and which treatment depends on whether the image is FIXED or PERSONALIZED.** A fixed image (one I generated or the user supplied, the same for every viewer) becomes a **video clip** (`generate video --first-frame`/`--ref-image`, see below). A **personalized media slot** (`hero_photo`, a product shot swapped per viewer) must stay an **`image` layer** — the API substitutes a still — so it gets its motion at the **layer level**: a slow **Ken-Burns** (scale + position on an anchored layer, `fit:"fill"`, anchor at the box centre so any replacement zooms correctly), or graphics building over it. Ken-Burns is the *right* tool there, not a fallback.
- **Let it breathe.** I hold text/images for read time (~0.5s + ~0.3s per word) before moving on, and never animate out before it can be read. Pacing is a feature, not dead air.

### Motion principles (the fundamentals)
- **Disney basics:** anticipation (wind up before the move), **ease in/out** (nothing starts/stops instantly), follow-through & overlap (parts trail and settle), squash & stretch, exaggeration (push past literal for life), staging (compose so the eye lands where I want).
- **Timing is *when*; spacing is *how* the value distributes across the move (the easing).** Amateurs leave timing uniform and motion linear; I vary both.
- **Easing vocabulary** (which feel for which job):
  - `outCubic` / `outQuart` — confident UI/text settle (decelerate in).
  - `outBack` — playful overshoot for entrances (use sparingly).
  - `outExpo` — fast, premium snap that glides to rest.
  - `inOutSine` / `inOutCubic` — smooth drifts, Ken-Burns, camera.
  - `outElastic` / `outBounce` — toy-like; only when the brand is energetic.
  - `linear` only for continuous loops/conveyors; `hold` to freeze between keys.
  - Or a cubic-bezier `[x1,y1,x2,y2]` for a custom curve.

### Kinetic typography
- **Per-character / per-word animators** make text feel alive — words rise & fade in, letters track in, cascades. (This is for **headlines and kinetic type**. **Captions and subtitles** are the opposite case: they appear in readable chunks, not word-by-word, because reading speed on a phone wins — [editing-director.md](editing-director.md). Decide which a text layer is before animating it.) Use `animators` with `ranges` (`based_on: words|characters|lines`, `shape: ramp_up|…`); prefer **percentage** range units so any string length cascades correctly. (Syntax in `format.md` Text; copy from `recipes.md`.)
- **Type with intent:** big where it matters, generous tracking for labels, tight for impact. Animate the meaning (a number counts up; a key word punches in).
- **Textured type** — a hero word filled with a texture through a track matte: gradient fills, stripes scrolling inside still letters, a gold-shine sweep (two mattes of the same word), footage in letterforms. **Textures are generated, not sourced**: organic ones (gold foil, marble, fire, watercolor) with `strata generate image` prompted as a full-frame no-subject texture; geometric ones (gradients, stripes) with a tiny script. Use it on the ONE hero word of a shot, not on body text. Verified recipes: [recipes.md](recipes.md) §1 "Textured text".

### Transitions between shots
- **Match-cut / continuity:** carry a shape, colour, or motion vector across the cut so shots feel connected.
- **Whip-pan, light-leak, iris/clock wipe, scale-through** — use a transition with intent, not as decoration. (Recipes available.)
- Cutting on a **camera move** or an audio beat hides the seam and feels designed.

### Depth, light & atmosphere
- Glow, shadow, blur, overlays, a vignette (feathered ellipse mask), a colour grade — all available, and **at most one atmospheric effect per piece**, chosen as its signature. Stacking glow + flare + grain + vignette is the generated look ([anti-slop.md](anti-slop.md)). A colour overlay for a coherent grade is not an effect and is always fine.
- **Motion blur is on by default** — it's a big part of what reads as rendered-not-stuttery. Keep it on for moving layers; raise comp `shutter_angle` (1–1.3) for fast moves.

### Rhythm & continuity
- **Cut/hit to the audio — with `strata beats`, never by guessing.** If there is music (or rhythmic audio) I run `strata beats music.mp3` and **snap scene cuts, entrances and accents to the returned onset times** — hand-timed motion against music always reads slightly off. `--fps <scene fps> --bands 12` adds a per-frame envelope for audio-reactive graphics (a logo that breathes with the track, drawn visualizer bars). For narration, size each scene to the **returned duration** (TTS reports it). Transitions ~0.3–0.5s between clips. Full workflow: [generative-fx.md](generative-fx.md).
- **Sound design is part of the cut, not a garnish.** Three layers — voice, bed, **SFX**: a whoosh on the transition (peak on the cut), a hit when the logo lands, a riser into the climax, ticks on counters. `generate music "TrackType: SFX, …" --duration 1` makes them (measured: exact length, correct envelope) and an `audio` layer's `start` places them ([music.md](music.md), *sound design pass*). Levels: VO 0 dB · bed −10…−12 with `ducking` · SFX −3…−8. `volume` cannot be keyframed and audio has no trim-in — fades and cut-ins are ffmpeg, before import.
- **Vary energy:** a quiet shot makes the next loud one hit harder. Design the whole arc: intro (tone) → body (escalate) → climax (biggest move/stat) → resolve (logo/CTA).

### Polish & anti-patterns
- **Polish:** nothing moves linearly; entrances settle, never pop (overshoot is one option, for the material that earns it — rigid, premium things stop clean); text has read time; elements have weight (ease + follow-through); one clear focal point per frame.
- **Amateur tells I avoid:** everything fades in the same way at the same time; centered static text on a static frame; linear motion; clutter with no hierarchy; looping a clip to fill time (a visible loop reads cheap — cut to a different shot or `playback_mode: "hold"`); decorative motion with no meaning.

### Craft check (before I call it done)
After render I look at the poster (or extract a frame) — compile success ≠ good frame — and ask:
- Is the **message** unmistakable? Does **every shot** earn its place?
- Is there a moment of **stillness** *and* a moment of **energy**?
- Does the **pacing** let the key shots land? Would the **first three seconds** make someone keep watching?
- Does every move have a job, and is there a deliberate still? Do images move; is there depth/camera where the frame felt flat — and nowhere it was flat on purpose?
- The poster answers none of the temporal questions — easing, rhythm, cuts, settles and sound are judged on the **rendered MP4** in step 7's review, never on one frame.

---

## Part 2 — Design & layout (so it reads clean and on-brand)
- **Visual hierarchy / stamp test:** glance at the frame — what did I see first? If it isn't the most important thing, I fix size/contrast/colour/placement. Product + CTA win the first glance.
- **Composition:** compose like a frame of film, not a web page — a clear **focal point**, **headroom and lead room** for anything that faces or moves, the subject on a **third** when the frame has a direction and **centred** when it is a statement (hero-center, end card). Give content **space**; "less is more" reads premium; "don't shout."
- **Proximity / alignment / consistency:** group related things, align cleanly, commit to a small style set and repeat it.
- **Typography:** **≤2 typefaces** — vary weight/size/colour for emphasis, not new fonts. Define fallback fonts; the font must cover every glyph used.
- **Colour:** set emotional tone, apply brand colours consistently. The same scene reads completely differently by colour treatment.
- **Contrast — text must stay legible over whatever is behind it.** Text over media/video or a busy background needs a contrast backing: a `solid` scrim or caption bar **matched to the background brightness** — near-opaque over bright footage (a 45% scrim over a white screen only makes grey; white text then washes out), light/low-opacity over dark. White-on-light and dark-on-dark fail; aim for clearly readable (~4.5:1). When in doubt, snapshot and check the caption is crisp.
- **Safe areas & no overlap:** keep text within ~90% **title-safe** (≥5% margin all sides); reserve a lower band for captions; avoid the player chrome. **No two text/visual elements may overlap while both are on screen** — lay out on a grid with real gaps, or separate overlapping elements in **time** (stagger their `start`). Don't let any box run off the frame. `validate`/`compile` warn when text boxes overlap or extend outside the frame — I fix those before rendering, and I **snapshot and look** at the frame to confirm spacing and balance (avoid a lopsided, one-side-heavy composition).
- **Over a personalised image slot** legibility must survive whatever image arrives — but a scrim is the **last** answer, not the first. In order: put the text where the layout reserves a quiet band; hold it on a solid panel that is **part of the layout**; grade the **whole** slot (a `solid` at 20–30 % over the entire image reads as a look, a box behind the words reads as a patch); let weight and size carry it. A scrim confined to the text box is the move that reads as generated — only when the brand specifies one, brightness-matched. [anti-slop.md](anti-slop.md).
- **Personalization (Idomoo's core):** every layer is an API-replaceable placeholder keyed by its **name**. Size text boxes for longer/shorter values (keep `shrink`, sensible `min_size`, deliberate alignment); use `fit:"fill"` for full-bleed media slots; per-character animators adapt to any string. Time the personalized reveal **early but not at t=0** (videos start muted). Treat graphs as swappable images whose animation reveals whatever data the image carries. (Details in `format.md`.)
