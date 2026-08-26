# Frame layouts & the grid — compose, don't guess coordinates

Blocks (`blocks.md`) are components; blueprints (`blueprints.md`) are whole-video
structures. **This file is the frame level: where things sit.** Pick a named layout that
matches the intent, then fill it — instead of inventing arbitrary boxes.

Always check the result with **`strata preview scene.json --at <sec> --grid`** (local
wireframe, free) before rendering.

**Canvas size and generated media:** `generate image` is 1376×768 and `generate video` is
1280×720, so a full-bleed generated plate in a 1920 comp is upscaled 1.5× — author at 1280×720
when the footage fills the frame, or keep it in a framed slot (assets.md, *Source resolution*).

---

## The grid (landscape 1920×1080)
| token | value | notes |
|---|---|---|
| margin | **120px** (6.25%) | never place content outside this; title-safe is 5% |
| content width | 1680 | `120 → 1800` |
| columns | **12** @ 118px, gutter 24 | col *n* starts at `120 + (n-1)·142` |
| bottom safe line | **y ≈ 950–990** (88–92%) | anchor the CTA/footer here |
| header band | y 120–360 | eyebrow + headline |
| body band | y 420–830 | cards/media/stats |
| gutter between blocks | 40–80 | breathing room; don't crowd |

Column-span width = **`142·k − 24`**. So **3 col** = 402 · **4 col** = 544 · **6 col** = 828 ·
**8 col** = 1112 · **12 col** = 1680. (Use the formula — don't eyeball a "round" width.)

## The other three canvases
Every template in the reference corpus that shipped socially shipped **four** sizes. Author
landscape first, then reflow (see *Reflow to vertical* below) — never letterbox.

| canvas | margin | content | columns | header band | body band | bottom safe line |
|---|---|---|---|---|---|---|
| **1920×1080** 16:9 | 120 | 1680 | 12 @118 / 24 | 120–360 | 420–830 | 950–990 |
| **1080×1920** 9:16 | 72 | 936 | 6 @136 / 24 | 160–460 | 560–1500 | 1690–1770 |
| **1080×1350** 4:5 | 72 | 936 | 6 @136 / 24 | 150–380 | 430–1120 | 1180–1250 |
| **1080×1080** 1:1 | 72 | 936 | 6 @136 / 24 | 120–300 | 340–800 | 900–960 |

For 9:16 and 4:5 keep text out of the **top 10% / bottom 15%** (platform UI chrome), and off the right-hand ~12% in the lower half (the Reels/TikTok button stack).

---

## Layouts

### 1. hero-center — one message, maximum impact
```
┌──────────────────────────┐   eyebrow   (center, small)
│         eyebrow          │   HEADLINE  (center, huge)
│        HEADLINE          │   sub       (center)
│          sub             │   ── CTA on the bottom safe line
│                          │
│          CTA             │
└──────────────────────────┘
```
`eyebrow [0,300,1920,50] center` · `headline [0,380,1920,180] center` · `sub [0,590,1920,70] center` ·
`cta [0,940,1920,70] center`. Use for: openers, end cards, single-claim statements.

### 2. split-media-left — product/UI beside the pitch
```
┌────────────┬─────────────┐   media fills the left half (bleed or framed)
│            │  HEADLINE   │   copy stack right, left-aligned, vertically centred
│   MEDIA    │  body       │
│            │  CTA        │
└────────────┴─────────────┘
```
`media [0,0,960,1080] fit:fill` · `headline [1050,340,750,150]` · `body [1050,510,750,180]` ·
`cta [1050,730,750,60]` (750 lands the copy exactly on the 1800 right margin). Mirror for split-media-right. Use for: product shots, demos, features.

### 3. three-up — three parallel points/stats
```
┌───┬───┬───┐   equal cards on the grid, staggered entrances
│ A │ B │ C │   header above, CTA on the safe line below
└───┴───┴───┘
```
Cards at `[120,470,544,360]`, `[688,470,544,360]`, `[1256,470,544,360]` — 544 is the **4-col span**, 24 gutter, and the third card ends exactly on the 1800 margin.
Value at card `+40,+90`; label at card `+40,+270`. Use for: benefits, stats, steps.
**Two-up** variant: `[120,440,828,420]` and `[972,440,828,420]` (828 = 6-col span).
**Lineup** variant (people): portrait tiles `[…,360,544,480]` with a name plate laid over the
bottom `[…,+400,544,80]` — name 30px bold, role 20px muted. See *avatar-cluster* for many faces.

### 4. stat-hero — one number that lands
```
┌──────────────────────────┐   context line, then a giant figure, then the takeaway
│        2.4M              │   figure ~180–260px type
│  videos shipped in 2025  │
└──────────────────────────┘
```
`context [0,340,1920,60] center` · `figure [0,410,1920,260] center` · `caption [0,700,1920,70] center`.
Pair with a count-up (see recipes). Use for: the one number in a data story.
**Stat grid** variant (2×2, seen on corporate openers): figure+label pairs at
`[120,420]`, `[972,420]`, `[120,700]`, `[972,700]`, each `828×240` (6-col span), figure 120px left,
label 28px baseline-aligned to the figure's bottom, hairline rules between the quadrants.

### 5. title-over-media — full-bleed footage with copy
```
┌──────────────────────────┐   media full frame
│        MEDIA             │   scrim over the text band ONLY
│                          │   copy bottom-left inside the margin
│▓▓ HEADLINE ▓▓▓▓▓▓▓▓▓▓▓▓▓│
└──────────────────────────┘
```
`media [0,0,1920,1080] fit:fill` · `scrim [0,760,1920,320]` · `headline [120,820,1400,110]` ·
`sub [120,940,1400,60]`. **Match the scrim to the footage brightness** (see blueprints.md).

### 6. quote-full — testimonial / pull quote
`mark [120,280,120,120]` · `quote [120,400,1500,300]` (56–72px, leading 1.25) ·
`attribution [120,760,1000,60]`. Keep the quote ≤ 20 words.

### 7. list-reveal — sequential points
Rows at y `420, 560, 700, 840`, each `[240,y,1440,90]`, revealed 0.4–0.6s apart, with a
number/tick at `[120,y,80,90]`. Use for: steps, agendas, feature lists.

### 8. lower-third — name/label over anything
`bar [120,860,760,150]` anchored bottom-left on the margin; rise in with `position [0,60]→[0,0]`.
See `blocks.md` for the block.

### 9. speaker-card — a person, their title, their slot
The single most-used frame in event/conference work. Two forms.

**(a) Panel form** — media on one side, name stack on the other:
```
┌───────────────┬──────────┐   media bleeds off its own edge
│  first        │          │   FIRST name small/light, SURNAME huge/bold
│  SURNAME      │  MEDIA   │   role under a hairline, theme below
│  ─────        │          │   date/time chip on the bottom safe line
│  role · theme │          │
└───────────────┴──────────┘
```
`media [1080,0,840,1080] fit:fill` · `first [120,500,900,70]` (48px) ·
`surname [120,575,900,140]` (110px bold, leading 1.0) · `rule [120,730,420,3]` ·
`role [120,745,820,44]` (24px, letterspaced, muted) · `theme [120,800,820,90]` (28px, ≤2 lines) ·
`chip [120,930,300,56]`. Mirror for media-left.

**(b) Over-footage form** — the name sits on the footage itself, bottom-left:
`first [120,760,1200,64]` · `surname [120,825,1200,130]` · `role chip [120,975,420,48]`.
Add a 40%-height bottom scrim only if the footage is busy under the type.

Rules: **surname 2–3× the first name**; never more than 4 lines of metadata; the accent colour
goes on exactly one element (usually the role chip).

### 10. diagonal-split — footage one side, solid the other
```
┌────────────┲━━━━━━━━━━━━┓   a single ~20° diagonal divides the frame
│  FOOTAGE   ┃   SALE     ┃   copy lives in the solid block, ≥160px clear of the diagonal
│            ┃   30% OFF  ┃   a second colour triangle may bite the opposite corner
└────────────┺━━━━━━━━━━━━┛
```
Divider from `(760,1080)` to `(1160,0)`. Copy box `[1240,380,560,320]`, left-aligned.
Keep the diagonal's angle **identical on every frame** of the piece — it's the brand device.
Use for: sale/offer promos, sport, anything that wants urgency.

### 11. media-mosaic — the multiscreen opener
```
┌──────┬────┬──────┐   irregular tiles, 8–16px gaps, full bleed
│  A   │ B  │      │   ONE tile is a solid brand cell carrying the headline
│      ├────┤  D   │   tiles ease in staggered 80–120ms apart
├──────┴────┼──────┤
│    E      │  F   │
└───────────┴──────┘
```
Base module: 3 cols × 2 rows of `640×540`, gap 8, then **merge cells** so it isn't a plain grid.
Example: `A [0,0,632,532]` · `B [640,0,632,262]` · `C [640,270,632,262]` · `D [1280,0,640,532]` ·
`E [0,540,952,540]` · `F [960,540,960,540]`. Headline 64–80px inside the solid cell, inset 56px.
Use for: openers, "what's in the event", sponsor/gallery walls, brand sizzles.

### 12. card-inset — a safe card over a busy background
Background art (gradient, pattern, blobs) runs full bleed; **all content lives inside a card.**
`card [80,60,1760,960]` radius 24 · content box `[160,140,1600,800]`.
Inside the card, re-apply the normal grid (margin 80 from the card edge, not the frame).
Use for: colourful/gradient brand systems where the background must not fight the copy.

### 13. product-card-split — the e-commerce frame
```
┌──────────┬───────────────┐   cutout product overlapping an outlined portrait rect
│  ▭ prod  │  New Arrival  │   old price + strike BAR, new price big
│          │  88.99  72.99 │   name, 2–3 lines of copy, then a pill button
│          │  Digital Watch│   alternate left/right on consecutive products
│          │  [ORDER NOW]  │
└──────────┴───────────────┘
```
`frame rect [200,180,520,720]` (stroke 6, accent) · product cutout centred on it and allowed to
overlap its edges · `eyebrow [980,300,700,40]` · `old [980,350,300,44]` (muted) + a `strike [980,372,150,3]` solid bar across it — ⚠ the text
`strikethrough` style does **not** render (format.md), so the bar is the only way ·
`price [980,395,400,60]` · `name [980,470,760,90]` (56–64px) · `body [980,575,760,120]` (24px) ·
`button [980,720,240,56]` (pill) · `url [980,960,700,32]`.
**Mirror every other product** so the reel has rhythm. Pair with *carousel-index* (#22).

### 14. schedule-row — one session in the programme
```
DECEMBER          12PM
   24             ┃ THEME ┃
                  two lines about the session
```
`month chip [120,420,220,50]` · `day [120,470,300,190]` (150–180px) ·
`time [470,430,260,70]` · `theme chip [470,510,180,44]` · `desc [470,570,760,120]`.
Stack 3–4 of these for a "programme of events" frame (rows 200px apart, y 260/480/700).

### 15. meta-rail — the evenly-spaced footer strip
Five (or four) small labels across the content width on the bottom safe line:
x = `120, 456, 792, 1128, 1464`, each `[x,950,300,40]`, 18–22px, uppercase, letterspaced +0.08em.
e.g. `OCT 15 · SAT 8PM · EVENT PLACE · THE TOPIC · THE INFO`.
Keeps a frame feeling designed when the middle is just footage.

### 16. frame-chrome — the persistent HUD
A tiny layer that never changes for the whole piece. It is what makes a set of frames read as
one system.
`brand [120,60,300,40]` (top-left) · `menu/mark [1760,60,40,40]` (top-right) ·
`caption [120,990,600,36]` (bottom-left, muted) · `index [1440,990,360,36]` right-aligned — the
running counter `03/07`. Opacity 0.5–0.8, 18–20px. Never animate it between shots; let it sit.

### 17. marker-headline — solid blocks behind each line
```
▉▉ Navigating ▉▉
   ▉▉ Digital ▉▉▉
▉▉ Disruption ▉▉
```
Each line gets its own solid block sized to the text + 24px horizontal padding, height
`1.35 × size`, 12px vertical gap. Left edges **step**: 120 / 160 / 120. Text knocks out white.
Use for: editorial/news-style titling over photography — it survives any background.

### 18. logo-wall — sponsors / partners / integrations
3×2 outlined cells: x = `200, 740, 1280`, y = `380, 620`, each `440×200`, stroke 2 at 40%.
Logo centred in the cell at ≤60% of the cell width; caption `[x,+210,440,30]`, 18px, centred.
Header above at `[0,240,1920,60] center`. Reveal in reading order, 100ms apart.

### 19. avatar-cluster — "15 speakers", many faces at once
Centre label `[660,500,600,80]` (center). 10–14 circular portraits on two rings around
`(960,540)`: inner ring r≈300, outer r≈470; radii 40–90px, **varied** (three big, the rest small).
Keep a clear `640×160` box behind the label. Scatter angles unevenly — a perfect ring reads as a
loading spinner. Portraits fade+scale in from 0.85 over 0.4s, 60ms apart.

### 20. device-trio — an app in three screens
`centre [810,180,300,650]` scale 1.08, in front · `left [480,240,290,610]` rotate −8° ·
`right [1150,240,290,610]` rotate +8°. Soft ground shadow under each. Copy, if any, goes above
at `[0,60,1920,90] center` — never between the devices.
Single-device variant: `[760,140,400,800]`, copy in the left third (`split-media-right`).

### 21. title-wall — the phrase repeated as a pattern
The same word/phrase tiled to fill the frame, over footage or flat colour.
Row height `1.05 × size`; offset every other row by `−0.35 × phrase width`; the whole wall drifts
40–80px across the shot. Footage shows through at 20–40% or is masked *by* the type.
Use for: hooks, festival/energy openers, chapter breaks. One shot only — it doesn't repeat well.

### 22. carousel-index — "which one of these am I on"
A numbered row on the bottom-left: `x = 120 + i·36`, `y 930`, 24px, inactive at 45% opacity;
the active number gets a 32px circle outline. Pairs with #13 and #16.

### 23. nav-bar frame — the web-page pastiche
`bar [0,0,1920,72]` (often transparent, just type): brand `[120,26,200,24]`, 3–5 links
right-aligned ending at 1800, 20px, 28px apart. Optional footer strip `[0,1008,1920,72]` with the
URL centred or right. Everything else uses the normal grid **inset by another 40px**.
Use for: "our site / our product" showcases, SaaS promos, digital-event openers.

### 24. ghost-display — oversized type as background
One word at 320–520px, cropped by the frame edges, at 8–15% opacity (or a 4–6% tint of the
background). It is never fully readable — it's texture that carries the brand voice.
Put it *behind* the media card, and keep real copy off its stems.

---

## Reflow to vertical (and square)
The reference templates all reflow the **same frame**, they don't crop it:

- **split-media → stacked.** Media takes the top ~55% full-width; the copy stack drops below it,
  still left-aligned, still on the margin.
- **Corner chrome migrates.** The brand mark goes top-centre or top-left; the index/counter moves
  from bottom-right to directly under the copy.
- **Price/CTA pills sit under the copy**, not beside it.
- **Type gets bigger, not smaller**: headline 1.15–1.3× the landscape size; body never below 26px
  at 1080 wide.
- **Cut the meta-rail** (#15) — five labels across 1080 is unreadable. Keep two, stacked.
- **Three-up becomes a vertical stack** of three rows at y 620/1010/1400, each `936×340`.
- 1:1 is the 4:5 layout with the body band compressed; drop the third card, not the headline.

---

## Rules that make any layout read as *designed*
- **Snap to the grid.** Left edges share a column; widths are column spans. Arbitrary
  numbers are the amateur tell.
- **Use the whole frame.** No dead third — either vertically centre the cluster or run a
  clear header / body / bottom-safe-line structure.
- **One focal point per frame.** Size is hierarchy: the hero element should be ~2–3× the
  next. If everything is medium, nothing reads.
- **Consistent gutters** (40–80). Uneven spacing looks accidental.
- **Align, don't centre everything.** Left-aligned copy blocks read faster; reserve centring
  for hero/end frames.
- **One accent colour, one job.** In every template that reads as professional, the accent lands
  on exactly one thing per frame — the chip, the price, the button — never on three.
- **Two type sizes are not a hierarchy.** Use three: display (90–140), body (24–30),
  meta (18–22, letterspaced, uppercase). Meta carries dates, roles, indices, URLs.
- **Corner metadata is cheap polish.** A brand mark, a running index and a small caption
  (layout #16) make an otherwise plain frame look like part of a system.
- **Never overlap two text blocks** while both are visible (`validate` warns) — separate in
  space or stagger in time.
- **Vertical text position is anchored at the box BOTTOM.** `align: "… top"` is not honoured —
  the glyphs render where `bottom` would (verified). So when you place a row at a y-coordinate,
  that coordinate is `box_y + box_h`, not the top of the type. Size text boxes to the copy
  (~`1.3 × size` per line) and place them by their bottom edge, or use `"… middle"` with the box
  centred on the target. Details + the measured numbers: [format.md](format.md).
- **Verify visually:** `strata preview --grid` → check balance/alignment → fix → only then
  `snapshot`/`render`. ⚠ **`preview` draws BOXES, not glyphs** — so it cannot reveal where text
  actually sits inside its box. Use `snapshot` for that.

---

## Letting the user lay it out — `strata studio`
Whenever a layout is shown (wireframe, options, or a `preview` grid), offer it:
*"want to lay it out yourself in the studio, or shall I go with this?"*

```bash
strata studio --width 1080 --height 1920      # vertical — NOT 16:9 by default
strata studio --width 1080 --height 1350      # 4:5 feed post
strata studio --width 1080 --height 1080      # square
strata studio scene.json                      # re-open a scene to nudge its boxes
```
**Always pass the piece's real canvas** (or a scene, which carries its own size). The studio
fits any aspect to the window, snaps to the same 12-column grid used here, and writes a
`*.guide.json` whose boxes are ready to author from directly.
