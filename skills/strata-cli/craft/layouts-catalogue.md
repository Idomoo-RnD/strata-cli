# The 24 named layouts

Each layout: what it is for, its boxes on the grid, and what it expects to be given.

Part of the frame-layouts reference — the index, and every other part, is in [layouts.md](../layouts.md).

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
`cta [1050,730,750,60]` (750 lands the copy on the 1800 right margin). Mirror for split-media-right. Use for: product shots, demos, features.

### 3. three-up — three parallel points/stats
```
┌───┬───┬───┐   equal cards on the grid, staggered entrances
│ A │ B │ C │   header above, CTA on the safe line below
└───┴───┴───┘
```
Cards at `[120,470,544,360]`, `[688,470,544,360]`, `[1256,470,544,360]` — 544 is the **4-col span**, 24 gutter, and the third card ends on the 1800 margin.
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
`sub [120,940,1400,60]`. **Match the scrim to the footage brightness** (blueprints.md).

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

**(b) Over-footage form** — the name sits on the footage, bottom-left:
`first [120,760,1200,64]` · `surname [120,825,1200,130]` · `role chip [120,975,420,48]`.
Add a 40%-height bottom scrim only if the footage is busy under the type.

Rules: **surname 2–3× the first name**; never more than 4 lines of metadata; the accent goes
on one element (usually the role chip).

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
Inside the card, re-apply the grid (margin 80 from the card edge, not the frame).
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
`frame rect [200,180,520,720]` (stroke 6, accent) · product cutout centred on it, allowed to
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
A tiny layer that never changes for the whole piece — what makes a set of frames read as
one system.
`brand [120,60,300,40]` (top-left) · `menu/mark [1760,60,40,40]` (top-right) ·
`caption [120,990,600,36]` (bottom-left, muted) · `index [1440,990,360,36]` right-aligned — the
running counter `03/07`. Opacity 0.5–0.8, 18–20px. Never animate it between shots.

### 17. marker-headline — solid blocks behind each line
```
▉▉ Navigating ▉▉
   ▉▉ Digital ▉▉▉
▉▉ Disruption ▉▉
```
Each line gets a solid block sized to the text + 24px horizontal padding, height
`1.35 × size`, 12px vertical gap. Left edges **step**: 120 / 160 / 120. Text knocks out white.
Use for: editorial/news-style titling over photography — it survives any background.

### 18. logo-wall — sponsors / partners / integrations
3×2 outlined cells: x = `200, 740, 1280`, y = `380, 620`, each `440×200`, stroke 2 at 40%.
Logo centred at ≤60% of the cell width; caption `[x,+210,440,30]`, 18px, centred.
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
