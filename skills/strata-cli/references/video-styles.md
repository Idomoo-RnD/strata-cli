# Video styles — 50 technique references

A catalogue of **how a kind of video is constructed**: its shot grammar, its motion
vocabulary, and the specific tells that make it read as that kind of thing. Each entry was
built and rendered as a real scene, so the techniques below are measured, not imagined.

---

## 🔴 Read these two rules before using anything here

### 1. A brand document OVERRIDES every style in this file

If `.brand/brand.md` exists it wins — palette, typeface, type ramp, corner radii, shadow
tokens, motion language, pacing. Always, silently, without asking.

This file governs **structure and technique**. The brand governs **atoms**. It is the same
split `brand.md` already states — *atoms are sacred, composition is free* — and a style
entry is composition:

| Comes from the STYLE (this file) | Comes from the BRAND (always wins) |
|---|---|
| Shot order, cut rhythm, what appears when | Every colour |
| Stagger intervals, easing choices, entrance/exit moves | Typeface and the type ramp |
| Layer construction (what is a solid vs a comp vs a matte) | Corner radius, shadow, stroke tokens |
| Framing rules, safe areas, aspect | Motion language where the brand defines one |
| Where the restraint is | Logo treatment and lockups |

So: take the **bento-grid** entry's 130ms stagger and outBack pop — but the cards are the
brand's colours, the brand's radius and the brand's font. Never carry a sample's palette
into branded work.

### 2. These are TECHNIQUES, not looks to copy

The samples exist to answer *"how is this built and timed"*, never *"what should it look
like"*. Copying a sample's colours, copy or composition wholesale produces a video that
looks like the sample instead of like the client. Take the construction; leave the skin.

---

## Pick a style

| The user says… | Read |
|---|---|
| "like a real TikTok/IG story", "user-generated", "selfie" | **ugc-selfie** |
| "unboxing", "first person", "hands only" | **ugc-unboxing** |
| "customer story", "interview", "quote from a client" | **testimonial** |
| "show the product/app working", "screen recording" | **screen-capture** |
| "cut this long video down for social" | **footage-repurpose** |
| "recap of the event", "highlights" | **event-recap** |
| "cinematic", "just the footage", "let it breathe" | **footage-only** |
| "brand film", "documentary", "our story" | **brand-film** |
| "hero shot of the product", "make it look premium" | **product-hero** or **luxury** |
| "presenter", "spokesperson", "someone talking to camera" | **avatar-presenter** |
| "cartoon", "animated character", "mascot" | **character-animation** |
| "high energy", "sport", "hype" | **sports-hype** |
| "drone", "flyover", "establish the city" | **aerial-flyby** |
| "80s", "VHS", "retro", "nostalgic" | **retro-analog** |
| "meme", "trending format", "native to the feed" | **meme-remix** |
| "news", "headline", "breaking" | **news-editorial** |
| "performance ad", "we're running this as paid" | **paid-social** |
| "TV commercial", "broadcast", "30 seconds exactly" | **tv-broadcast** |
| "product carousel", "our range", "shop the collection" | **retail-carousel** |
| "sale", "% off", "urgent", "limited time" | **sale-urgency** |
| "App Store preview", "app screenshots in motion" | **app-store** |
| "screen in a shop window", "billboard", "it must loop" | **ooh-loop** |
| "same video for every customer", "personalised" | **personalized-template** |
| "we need it in all sizes" | **multi-aspect-kit** |
| "for an email", "a GIF", "tiny file" | **email-loop** |
| "before and after", "the transformation" | **before-after** |
| "top 3", "countdown", "list of tips" | **listicle** |
| "explain how it works", "diagram it" | **motion-explainer** |
| "our numbers", "annual report", "the results" | **data-report** |
| "the words ARE the video", "kinetic text" | **kinetic-typography** |
| "clean", "editorial", "Swiss", "grid" | **swiss-grid** |
| "dashboard", "modular cards", "bento" | **bento-grid** |
| "technical", "engineering", "schematic" | **blueprint** |
| "terminal", "hacker", "code" | **crt-terminal** |
| "layered", "papercraft", "depth" | **papercut-parallax** |
| "draw it on", "line art", "illustrated" | **line-draw** |
| "glitchy", "broken signal", "corrupted" | **glitch** |
| "organic", "fluid", "morphing", "blobs" | **liquid-morph** |
| "ambient", "calm", "just light and colour" | **aurora-gradient** |
| "react to the music", "sync to the beat" | **audio-reactive** |
| "isometric", "3D-ish diagram", "build it up" | **isometric-build** |
| "counter", "the number goes up" | **odometer-count** |
| "playful", "bold shapes", "fun" | **memphis** |
| "fly through the words", "type in 3D" | **type-in-space** |
| "orbit it", "move around it" | **orbit-dolly** |
| "fly through", "tunnel", "into the screen" | **corridor-3d** |
| "explode the layers", "show what's inside" | **exploded-3d** |
| "logo sting", "ident", "2 seconds of brand" | **logo-ident** |
| "text behind the person/product" | **matte-occlusion** |

---

## A · Footage the user supplies

Runs through `matte` / reframe / trim / ffmpeg — never `generate`.

- **ugc-selfie** — the imperfection rules ARE the type. Nothing grid-aligned: every overlay
  sits at 2–8° rotation, off-centre, sometimes clipping frame. Captions in the middle-lower
  third where a thumb would drop them, never on a safe line. Stickers **pop** (scale 0.8→1
  outBack ~0.3s), they never fade. One or two overlays at a time, appearing and leaving as
  the person talks — not a static HUD. ⚠ Each sticker is a **sub-comp** (solid + text
  rotated as one unit) or the two drift apart. Emoji are image layers, never text.
- **ugc-unboxing** — first-person overhead, hands enter frame from the bottom, macro on the
  reveal. Same sticker grammar, fewer of them; the product does the talking.
- **testimonial** — lower third slides in (position −60px + fade, outCubic ~0.55s) and
  leaves before the b-roll; subtitle bar held for the whole clip. b-roll cutaway on the
  sentence break, not mid-word.
- **screen-capture** — **build the UI as layers**, don't film it: chrome bar, traffic-light
  dots, cards. Cursor moves inOutCubic with a hold before the click; results stagger ~220ms.
  One interaction per shot; never move the cursor and change the screen at once.
- **footage-repurpose** — centre-crop 16:9 → 9:16 with ffmpeg, then panel the top and bottom
  to carry title and CTA. What survives a crop decides the cut.
- **event-recap** — energy over polish. Title over handheld, held legible by placing it on
  the quietest part of the plate or on a solid panel, not a scrim ([anti-slop.md](anti-slop.md));
  a meta line (place · scale · duration) does the work a paragraph would.

## B · Generated footage

- **footage-only** — **restraint is the entire type.** Letterbox bars, one letterspaced
  title late (7s+), nothing else for ten seconds. If you are adding a second graphic, it is
  no longer this type.
- **brand-film** — one quiet line, once. Type almost absent.
- **product-hero** — the wordmark sits **under** the footage; light and product occlude the
  type. See **matte-occlusion** for the alpha version.
- **luxury** — defined by what is removed: media boxed to one side, ~60% negative space, a
  hairline rule drawing on, slow settle. Restraint reads as expensive.
- **avatar-presenter** — lower third only around the speech; a stat card pops at the proof
  moment, not before. See [avatar.md](avatar.md).
- **character-animation** — model-sheet consistency; subtitle bar for dialogue.
- **sports-hype** — one word per shot, on the cut, ~1.5s each; a hot accent bar; nothing
  fades, everything cuts.
- **aerial-flyby** — place + coordinates chrome and nothing else; the camera is the content.
  Block the camera path with a sketch rather than describing it.

## C · Graphics-led

- **news-editorial** — the band grows from x-scale 0 (outExpo), a LIVE flag anchors it, the
  ticker travels at constant px/s (linear, never eased).
- **listicle** — cards stagger ~2s apart, chip pops then text slides in; the per-item beat
  must be identical or it reads sloppy.
- **before-after** — an **animated mask-rect** wipe with a divider travelling with it; the
  AFTER tag pops only once the reveal lands. Framing must match exactly between the two.
- **motion-explainer** — nodes pop outBack, the connecting line grows x-scale, a packet dot
  travels the line with **holds** at each node. One idea per beat.
- **data-report** — hero number scale-settles (outExpo 0.7→1); bars grow from x-scale 0.01;
  labels arrive **after** their bar. Animate the reveal, never the data.
- **kinetic-typography** — words swap in place; full-frame colour flips **are** the
  transitions. No footage, no decoration.

## D · Ads & commerce

- **paid-social** — hook on frame 1 (not second 1), captions always on for muted autoplay,
  CTA end card with a pill. Hook variants are a deliverable, not a nicety.
- **tv-broadcast** — exact duration, title-safe, legal line small and legible, end sting
  scale-settles 1.06→1.
- **retail-carousel** — one frame per product, layout **mirrors** on alternate frames for
  rhythm, strike-bar over the old price (⚠ the `strikethrough` style does NOT render — draw
  a solid bar), running index.
- **sale-urgency** — a tilted wedge (−4°), the percentage in the heaviest face available,
  coupon card lands late.
- **app-store** — device built from rounded solids; one CTA; respect store spec.
- **ooh-loop** — silent, huge type, legible at distance, and it **must** loop: build the
  motion so the exit state equals the entry state.

## E · Personalized / systemic

- **personalized-template** — the **layer names are the API contract** (`greeting`,
  `statValue`, `ctaButton`). Design for the longest plausible string, not the demo one. See
  [personalization.md](personalization.md).
- **multi-aspect-kit** — one master plus reflows; never centre-crop a 16:9 into 9:16.
- **email-loop** — seamless, silent, tiny; the first frame doubles as the poster.

## F · Style-forward

- **retro-analog** — artefacts are the design. REC blink uses **hard opacity steps, never
  fades**; date/timecode chrome in the corners.
- **meme-remix** — deliberately unpolished: top-bar caption, bottom punchline. Polish kills
  it exactly as it kills UGC.

## G · Pure motion design (no footage at all)

The cheapest to produce, the easiest to personalise, and where the craft shows most.

- **swiss-grid** — columns unroll on y-scale, a red rule crosses inOutExpo, type slides in
  −40px. Strict grid, nothing decorative.
- **bento-grid** — cards stagger **130ms** apart with an outBack pop; label at ~75% opacity,
  value slides up behind it.
- **blueprint** — the outline draws **edge by edge**, then the dimension line, then the
  measurement. Order is the whole effect.
- **crt-terminal** — lines **appear** (no fade — a fade is instantly wrong), cursor blinks
  on hard steps, scanlines are static.
- **papercut-parallax** — 4+ planes at different z with real shadows; the camera drifts and
  parallax does the rest. Shadows are what sell the paper.
- **line-draw** — strokes grow from scale 0.01 along their own axis; nodes pop at the joints
  as each stroke lands.
- **glitch** — three additive RGB copies jittering **independently**; damage bars flash for
  ~2 frames. Controlled damage, on a rhythm.
- **liquid-morph** — path-mask **shape keyframes** morph blob to blob; additive blend; never
  cuts. Both shapes need the same point count to morph.
- **aurora-gradient** — blurred additive ellipse masks drifting and breathing. No objects at
  all. Pure atmosphere.
- **audio-reactive** — **`strata beats` gives the real onsets; never hand-time this.**
  outExpo attack, outCubic decay. See [generative-fx.md](generative-fx.md).
- **isometric-build** — a cube is three path-mask faces shaded ×1 / ×0.8 / ×0.62; pieces drop
  in ordered by grid distance.
- **odometer-count** — a rolling digit strip inside a **nested-comp window** (a mask cannot
  make a window — see gotchas), stepping outExpo. For a plain numeric count the native
  `character_offset` animator is cheaper and personalization-safe (recipes.md, Count-up).
- **memphis** — squash-and-stretch: `[0.2,1.7] → [1.12,0.9] → [1,1]`. Overshoot is the point.
- **type-in-space** — words every ~600 units of z, camera flying through them.
- **orbit-dolly** — cards at different z, camera arcs ±170px; parallax does the work, no
  actual rotation needed.
- **corridor-3d** — stroked frames every ~360z, camera flies through and rolls slightly.
- **exploded-3d** — tilted plates fanning apart along the tilted normal, then reassembling.
- **logo-ident** — 2–4s: assemble (rotation 225→45 outExpo), a 2-frame white flash, the name
  rises, hold. Nothing more.
- **matte-occlusion** — text passes **behind** the subject. The plate and the `.jet` are
  **the same clip**, same box, and the `.jet` has **no position animation**. See
  [video-layouts.md](video-layouts.md) §6 for when this is worth doing at all.

---

## Cross-cutting motion vocabulary

Reusable regardless of style:

| Move | Values that worked |
|---|---|
| **Pop / spring in** | scale `0.8→1` outBack over 0.3–0.5s, opacity 0→1 in the first 0.12s |
| **Settle** | scale `1.06→1` or `1.25→1` **outExpo** — reads premium, never bouncy |
| **Slide + fade** | position `[0,±26]→[0,0]` outCubic 0.5s with opacity 0→1 |
| **Bar / rule grows** | anchor at one end, scale `[0.01,1]→[1,1]` inOutExpo 0.7–1.2s |
| **Card stagger** | 100–200ms between siblings; more than ~250ms reads as a list, not a group |
| **Hard state change** | opacity keyframes 0.05s apart — for REC blinks, terminals, glitch |
| **Ticker** | linear only; constant px/s. Easing a ticker is instantly wrong |

---

## ⚠ Engine gotchas these styles hit

Every one was found by rendering, and each one silently produces a broken video:

1. **Sub-comps default to ~100 frames (≈4.2s at 24fps).** A 10s scene using comps **must**
   set `duration` on every comp, or its content blanks part-way through.
2. **Camera `position` is an ABSOLUTE comp coordinate** — `[960,540,z]` for 1920×1080, with
   **negative z**. `[0,0,z]` parks the camera in the top-left corner and the scene renders
   black or badly off-centre. And it is a measured pinhole: park it at **`z = −focal`**
   (`focal = (h/2)/tan(fov/2)`, 935 for 1080p @ fov 60) or every z=0 layer renders small;
   **negative z on a layer comes TOWARD the camera** (bigger), far backgrounds go at positive z.
3. **`anchor` + animated `position` on a 3D layer:** with an anchor set, every position
   keyframe is the **absolute point where the anchor lands**. Keep x,y at the anchor and vary
   only z, or the layer is dragged to that coordinate.
4. **A mask travels WITH its layer, so it can never be a window.** Windows are **nested
   comps** (a comp canvas clips its content) or a static track-matte layer.
5. **The `validate` overlap warning is a false positive for 3D and RGB-split scenes** —
   layers at different z, and deliberate chromatic-split copies, legitimately overlap in 2D.
6. **`strikethrough` / `underline` / `highlight` do not render** — draw a `solid` bar when the brief asks for one (a rule under a lone title is a tell — [anti-slop.md](anti-slop.md)).
7. **Emoji are image layers, never text** — `strata glyphs` catches this before a render.
