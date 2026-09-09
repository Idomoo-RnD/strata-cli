# Steps 1–2½ — assets, storyboard, sign-off, style

Getting the media made in parallel, then the storyboard and the sign-off that has to happen before any scene JSON exists.

Part of the production workflow — the index, and every other part, is in [workflow.md](../workflow.md).

## 1. Assets

If any person, product or set recurs across clips, the production bible comes first — sheets and
identity blocks, [production-bible.md](production-bible.md) — and every generated frame or clip
cites them.

**Clip length is decided here, not fixed later.** Every clip must be at least as long as the scene
slot it fills — size it from the storyboard's slot plus ~1 s of margin, generate longer and trim,
never shorter: a `video` layer that runs out holds its last frame and the shot freezes. One that
still comes back short is covered with more shots or extended off its last frame, never retimed;
a longer one is trimmed, never sped up. The measured contract, the `offset_frame` arithmetic and
the 24 fps clip against the 25 fps scene default:
[video-generation.md](../video-generation.md#what-generate-video-actually-does--measured) and
[traps.md](../traps.md).

**Measure every clip the moment it lands.** `strata deconstruct <clip> --json` on each generated
clip, before the storyboard commits to it: a clip that came back static despite the camera move in
its prompt (*measured:* five of thirteen in one piece, one at stillness 1.00) is re-prompted now,
not discovered on the first full render — and the clip's `motion.thirds` say where its liveliest
window is, which is where the slot's `offset_frame` starts ([craft.md](../craft.md), *A clip has a
liveliest window*).

**Generate in waves, in the background** — every asset with no unmet dependency at once (sheets,
plates, TTS, music), then every clip whose inputs exist, then only continuations — and author the
scene while they render; a 10-scene piece is ~10 min in waves, ~60 serially
([assets.md](../assets.md)). Parallel renders — and parallel `snapshot`s — need unique scene filenames: two jobs on one file collide in the exporter (error 3000 — a generic code, [traps.md](../traps.md#error-3000-is-a-generic-exporter-code)).

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
still apply in full. The library comes from a choice the user already made, in this order:
`--library`, a `.idm-library` file in the project (the persisted id from `library create`, which
`render` and `snapshot` now read on their own and log `library <id> from <file>`), then the
`library` preference (`strata prefs`); with none of the three a non-interactive `render` fails
with the library list rather than picking one ([commands.md](../commands.md), *Libraries*).
**Never end a turn on a promise.** Nobody is watching, so a turn that ends on
"waiting for the snapshots" or "I'll render next" blocks the job until someone notices — and a
backgrounded waiter does not wake you, so it stalls indefinitely. Run
long steps in the foreground with a long timeout, or poll them yourself in the same turn, and end
the turn only when the deliverable exists or a real blocker needs the user.

Apply the craft ([craft.md](../craft.md)) to plan the piece. A storyboard that lets the user actually
decide has:

- **Title**, a one-line **style** (palette, motion feel, type, where the light comes from), and
  **the four numbers** — the shot-length range (the shortest cut and the longest hold, each named
  with the job that earns it, and the hold also with what keeps moving through it), motion energy, stillness ratio, and loudness as an integrated LUFS
  target for where the piece will play plus the gap between its loudest and quietest beat
  ([music.md](../shoot/music.md), *Decide the mix*) — with the reference row they sit near and the one line
  of *this* brief that puts them there (SKILL.md, *Place the piece on the range*). Written before
  the shot list, because the shot list is derived from them, and decided here rather than inherited
  from whatever the bed generated at.
- **For premium work** (AAA, cinematic, broadcast, launch film, hero animation — or when the brief
  says "premium"): directions that differ in kind, styleframes for the hook, the hero moment and the
  end card, and a motion bible when the piece is big enough to need one. What stays still is decided
  as carefully as what moves. The protocol: [director.md](../direct/director.md).
- The **editorial contract** — format / dominant mode / modifiers, the one-sentence viewer promise,
  and the information-release plan (what the viewer knows before, during, after each beat) — from
  [editing-director.md](../direct/editing-director.md). One dominant mode, a modifier or two; a generic fast
  montage is what a piece becomes when nobody chose.
- A **shot list** — `Time | Visual / Motion | Voiceover | Sound` — covering the full duration,
  **one row per beat with the length that beat's job needs** — the mean falls out of the rows, it
  does not generate them. Mark the declared hold — naming the motion that carries it, not just its length — and the declared
  flashes, and read the durations
  column before any JSON: rows all within a whisker of each other are one speed, whatever the mean.
- **A layout sketch for the key frames** — a small ASCII wireframe showing where things sit, named
  from [layouts.md](../layouts.md) (`hero-center`, `split-media-left`, `three-up`, `stat-hero`,
  `title-over-media`…). A shot list says what happens; only the sketch says what it will look like,
  so the user can move the logo or resize the stat for free, before any JSON.
- **End frame** (logo/CTA text) and **motion notes** — transition timing ~300–500 ms, transform-based
  (scale/position/opacity/masks), the intended feel, how many motion ideas run per shot and how
  many transition families are in play (both scale with the declared energy —
  [craft.md](../craft.md)), and the job of every move.

When the framing could go more than one way, offer 2–3 layout options as side-by-side wireframes —
people recognise the layout they want far better than they can describe it. Ask once, up front,
whether to show `strata preview … --grid` at each key frame as you go. Whenever a layout is shown,
offer `strata studio` in the same breath (a local browser designer writing a `*.guide.json` to
author from); launch it only on a yes: [layouts.md](../layouts.md), *Hand the layout to the user*.

## 2½. Style

What kind of video is this? [video-styles.md](../craft/video-styles.md) has 50, each with its construction,
timing values and the tells that sell it. Take the technique (shot order, stagger, easing, layer
construction), not the sample's look: if `.brand/brand.md` exists, the brand's palette, type,
radii and motion language override the style entirely — style is composition, brand is atoms.
