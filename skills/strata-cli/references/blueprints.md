# Blueprints — whole-video recipes by type

Structure-level recipes for the common video kinds. Each gives a shot list, pacing, which [blocks](blocks.md) to use, and the narration shape — feed it into the storyboard stage (SKILL.md step 2), get sign-off, then author. All assume an audio-anchored timeline: I size each scene to the **narration's returned duration** and put **0.3–0.5s transitions** between clips. Default 1920×1080 (or 1080×1920 for social).

Pick one, adapt the shots, keep every layer name unique.

**Pacing that holds up across the reference corpus:** shots run **2.5–4s** (down to 1.2–2s for
sport/energy, up to 6s for a speaker frame with copy to read). Total runtimes cluster at
**17–30s** (openers, stings), **30–60s** (promos, product reels — the bulk), **60–120s**
(explainers, showcases). Always **hold the end card 2–3s** past the last move; never cut on it.

---

## Product launch / promo
**Arc:** hook → problem → product → proof → CTA. 6–8 shots, 20–40s.
1. **Hook** (0–3s) — bold title + a moving background (animated still or video). One sentence.
2. **Problem** (3–7s) — the old way / friction; kinetic text.
3. **Product reveal** (7–13s) — `device-frame` block with the product shot pushing in; a `lower-third` naming the feature.
4. **Benefits** (13–20s) — 2–3 `stat-card`s or feature cards, staggered.
5. **Proof** (20–25s) — `quote-card` (testimonial) or a logo wall.
6. **CTA** (25–30s) — `end-card` block; **hold** on it.
Narration ≤~20 words/shot; brand colours throughout; ≤2 typefaces.

## Event / conference promo
The most common commercial shape there is. 10–16 shots, 35–60s. Layouts #9, #11, #14, #15, #18.
1. **Sizzle** (0–4s) — `media-mosaic` (layouts #11) of the venue/audience/topic, tiles landing on the beat. No copy yet, or one word.
2. **Title card** (4–8s) — event name huge, then the dates as a separate accent line, then `#hashtag`. This exact frame comes back at the end.
3. **Promise** (8–16s) — 2–4 one-line claims over B-roll ("15 best speakers", "5 days to network", "50 sessions & workshops"). Numeral + noun, one idea per shot.
4. **Speakers** (16–34s) — 3–5 `speaker-card` frames (layouts #9), **alternating** panel-left / panel-right. Each: first name + SURNAME + role + talk title + their date/time chip.
5. **Programme** (34–42s) — 1–2 frames of `schedule-row` (layouts #14) or a 3-row day breakdown. Optional live **countdown** block (days / hours / minutes in three outlined cells).
6. **Sponsors** (42–48s) — `logo-wall` (layouts #18), revealed in reading order.
7. **Venue / city** (48–52s) — aerial or building shot with the city name and address.
8. **CTA** — repeat the title card + `REGISTER TODAY` + URL + hashtag. Hold 3s.
Keep a `frame-chrome` (layouts #16) on every shot: brand mark, running index, small caption.

## Explainer (faceless)
**Archetype:** concept · how-to · listicle · story. 4–8 shots, 30–90s.
- Open with an **establishing shot** that frames the topic; show, don't tell.
- One idea per shot; build diagrams/graphics over a calm background.
- Use `stat-card`/charts (see [recipes.md](recipes.md) data-viz) for any numbers.
- Close on a one-line takeaway + soft CTA.
Generate narration first, then pace shots to its word timings.

## SaaS / app / AI product promo
Dark, quiet, confident. 8–12 shots, 25–45s. Almost no footage — the UI *is* the visual.
1. **Pain hook** (0–4s) — one sentence of plain text on a dark gradient ("Slow team processes", "The way we work is changing"). Two-tone: the key phrase in the accent, the rest at 60% white.
2. **Promise** (4–8s) — the counter-statement, same treatment.
3. **The moment** (8–14s) — a single UI element doing one thing: a prompt bar being typed into, a search field, a toggle. Nothing else on screen. This is the shot people remember.
4. **Surfaces** (14–26s) — 2–4 shots of dashboards / cards / charts floating at a slight 3D tilt, drifting; a statement line beside or above each. Cards get a 1px accent stroke and a soft outer glow, never a hard drop shadow.
5. **Integrations / capabilities** (26–32s) — three rounded icon cards in a row (layouts #3), or a `logo-wall`.
6. **Proof** (32–38s) — animated KPI counters in pill cards (`10K monthly`, `4.8 rating`, `98.2% uptime`), counting up on entry.
7. **CTA** (38–45s) — "Book a demo" / the bare URL, centred, held.
Palette: near-black ground + one saturated hue (indigo / violet / blue) as a **soft radial blob**
behind everything; keep total saturated area under ~30% of the frame. Type: one geometric sans,
regular weight, generous tracking. Motion: slow drifts and fades, 0.6–0.9s — no snappy cuts.

## UI walkthrough / product demo (simulated interface)
When the point is *how it works*. 6–10 shots, 20–40s. Related to the above but literal.
- Build the UI as layers (chips, fields, cards) rather than pasting a screenshot — then you can
  animate the individual parts.
- Shot rhythm: **typing** into a field (character-by-character, 40–70ms) → **click** (cursor +
  a 120ms button press) → **result appears** (a grid of result cards staggering in 60ms apart).
- One interaction per shot. Never move the cursor and change the screen at once.
- Zoom the frame to whatever you want read: a 1.15–1.3× push into the relevant panel beats
  showing the whole app.
- Close on the full screen pulled back, then the CTA.

## E-commerce / product carousel
N products, one frame each. 8–12 shots, 30–55s. Layout #13 + the index (#22).
1. **Title card** — brand + "Product Promo"/collection name, with the first product ghosting in behind.
2. **Product frames** ×5–8 — each: cutout on an outlined/solid shape, name, 2–3 lines of copy, old price with a **strike bar** over it (a thin `solid` — the `strikethrough` text style does not render), new price, discount badge, `ORDER NOW` pill. **Mirror left/right on alternate frames.** Index counter bottom-left ticks `01/07 → 02/07 …`.
3. **Everything frame** — all products in a row or a `three-up`, with prices, under a "Best deals" header.
4. **CTA** — the URL, big, centred.
Product cutouts want a consistent size relative to the frame (~45–55% of the height) and a
consistent light direction. Give each frame **exactly one** accent element — the badge or the
button, not both in the accent.

## Sale / seasonal offer
Loud version of the above. 6–8 shots, 15–30s. Layout #10 (diagonal-split) is the device.
Percentages are the hero: `SALE 30% OFF` at 140–200px, the product photo bleeding off the diagonal.
Add a `COUPON CODE: XXXX` block on the penultimate frame. Fast 250–350ms transitions, hard cuts on
the beat. Keep the same diagonal angle on every frame.

## Social promo (9:16, 5–15s)
- Frame **1080×1920**; reserve the bottom ~17% for captions/safe area; keep text in the title-safe ~90%.
- 2–4 punchy shots, fast 300–400ms transitions, big type, one message.
- Assume **muted autoplay**: lead with a strong visual + on-screen text; personalized value appears early but not at t=0.

## Multi-aspect delivery (cross-cutting)
Reference templates ship **1920×1080 + 1080×1920 + 1080×1350 + 1080×1080**, plus a **15s cutdown**
of a 40–60s master. Plan for it up front:
- Author landscape, then **reflow** (layouts.md § *Reflow to vertical*) — never centre-crop a 16:9
  frame into 9:16; the copy ends up in the platform's UI chrome.
- The cutdown is not "the first 15s". It's **hook → one proof → CTA**, re-timed.
- Say which sizes you're producing before you start; each one is a real authoring pass.

## Data story / KPI
- A grid or stack of metrics; reveal each with a **count-up** + **bar/ring** (recipes.md data-viz).
- Animate the **reveal**, not the data (a mask wipe in the growth direction); the number/shape carries the value.
- Group related stats; one hero stat gets the biggest move/scale.
- A `stat grid` (layouts #4 variant) of four figures behind hairline rules reads as "annual report"
  and costs nothing; a single `stat-hero` reads as "headline claim".

## Logo reveal / sting (3–6s)
- `logo-sting` block: logo scales/fades in with light overshoot, a quick accent (glow or a wipe), settle, hold.
- Optional tagline fades in under it; end on a clean hold.
- Wordmark variant: animate the letterforms instead of the mark — draw-on, ink-bleed, stagger-in,
  bounce-settle. One idea, 1.2–2s, then still.

## Kinetic typography / title pack
No footage at all, or footage only as a mask. 8–12 shots, 20–40s.
- **One line per shot**, 2–5 words, centred or hard-left. The animation *is* the design.
- Named moves worth having in the vocabulary (straight from the reference packs): *KineticFlow*
  (words swap in place), *DropPulse*, *BounceOrbit*, *SpinFlick*, *ArrowRush*, *ElasticSwap*,
  *DepthFlow* (per-character z), *FinalImpact* (everything lands at once on the last beat).
- Alternate two backgrounds (e.g. black / brand) shot to shot — the flip *is* the transition.
- Optional: mask footage **inside** the letterforms on 1–2 shots for contrast; don't do it on all.
- Finish with the wordmark or URL, still.

## Sport / fitness / energy promo
15–30s, 1.2–2.5s shots. One word per shot (`STRENGTH`, `ENDURANCE`, `EXTREME`), 100–140px,
centred, over hard-graded footage. Glitch/RGB-split or shatter transitions on the beat, cut to
black for 2–3 frames between sections. Palette: desaturated footage + one hot accent
(red/orange). Close on a 3-line statement (`MAKE / MUSCLES / NOT EXCUSES`) then the logo.

## Fashion / lookbook
30–50s. Editorial and calm even when colourful. Rounded-rect or capsule-masked media, generous
white space, one serif display face + one small sans for meta. Shots alternate: full-bleed look →
two/three-up of looks → a copy frame with an oversized ghost word (layouts #24) → back to a look.
Colour blocking (a solid half-frame in the brand colour) does the transitions. End on the logo
over white with a single `Drop Email` / `Shop now` pill.

## Corporate / interior / real-estate slideshow
40–60s, slow. A muted ground (bone, warm grey), black display type in outlined boxes, one image
card per frame with a `_01 … _04` index chip on its corner, and an oversized letterform sliding
behind. Ken-Burns everything by 3–5% only. This shape is what "premium and quiet" looks like —
resist adding motion.

## Website / app showcase
- Capture a screenshot of the site/app (the agent can use the browser tools), bring it in as an `image` layer.
- **Ken-Burns** the screenshot (slow scale+position) or place it in a `device-frame`; add kinetic **callouts** pointing at features (a small `solid` chip + a `lower-third`-style label, or an animated cursor image layer following the UI).
- Perspective-tilt the page panel (8–15° on Y) and let it drift — a flat screenshot reads as a slide, a tilted one reads as a product.
- A `device-trio` (layouts #20) sells "it's an app"; a browser chrome frame sells "it's a site".
- Don't loop a captured screen-recording clip — cut to the next shot when it ends, or `playback_mode:"hold"`.

## Overlay an existing video (lower-thirds / captions / callouts)
- Put the source MP4 as a full-frame `video` layer at the back (`loop:false`; size the scene to the clip).
- Add `lower-third` blocks, callout chips, or a logo bug **on top**, timed to moments in the footage.
- Don't recolour the footage — the graphics are the only addition. Drop a `solid` scrim where text sits over busy areas.
- **Match the scrim to the footage brightness** so captions stay legible. A 45%-black scrim over **bright** footage (e.g. a white UI screen) only makes mid-grey — white text on it is low-contrast. Over bright/unknown footage use a near-opaque scrim (opacity ~0.7–0.85) or **dark** text; over dark footage a light/low-opacity scrim with light text is enough. Aim for clearly-readable contrast (WCAG ~4.5:1). When the footage brightness varies under the text, prefer a solid caption bar over a faint scrim.

---

## Devices worth stealing (they work in almost any blueprint)
- **The frame that returns.** Show the title card early, then again at the end, identical. It makes
  a loose reel feel composed.
- **Persistent chrome.** A brand mark, a running `03/07` index and a small caption on every shot
  (layouts #16). Cheapest possible polish.
- **Numeral + noun claims.** "50 SESSIONS", "15 SPEAKERS", "5 DAYS" — the numeral 2–3× the noun.
- **The repeated-title wall** (layouts #21) as a one-shot hook or chapter break.
- **Colour-block transition.** A solid brand panel wipes across, and the next shot is already
  behind it. No effect plugin required, and it always reads as intentional.
- **One accent, one job, per frame** — see layouts.md rules.

**Always:** run it through the storyboard stage for sign-off; give every layer a unique name; verify the poster before calling it done (`strata snapshot`).
