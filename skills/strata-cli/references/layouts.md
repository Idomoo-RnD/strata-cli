# Frame layouts & the grid — compose, don't guess coordinates

Blocks (`blocks.md`) are components; blueprints (`blueprints.md`) are whole-video
structures. **This file is the frame level: where things sit.** Pick a named layout that
matches the intent, then fill it — instead of inventing arbitrary boxes.

Always check the result with **`strata preview scene.json --at <sec> --grid`** (local
wireframe, free) before rendering.

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

Common column spans → widths: **3 col** = 378 · **4 col** = 520 · **6 col** = 804 · **12 col** = 1680.

## The grid (vertical 1080×1920)
margin **72px** · content 936 · 6 columns @ 140, gutter 20 · bottom safe line y ≈ 1690–1770 ·
header y 160–460 · body y 560–1500. Keep text out of the top 8% / bottom 12% (platform UI chrome).

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
`media [0,0,960,1080] fit:fill` · `headline [1050,340,760,150]` · `body [1050,510,760,180]` ·
`cta [1050,730,760,60]`. Mirror for split-media-right. Use for: product shots, demos, features.

### 3. three-up — three parallel points/stats
```
┌───┬───┬───┐   equal cards on the grid, staggered entrances
│ A │ B │ C │   header above, CTA on the safe line below
└───┴───┴───┘
```
Cards at `[120,470,520,360]`, `[700,470,520,360]`, `[1280,470,520,360]` (520 wide, 60 gutter).
Value at card `+40,+90`; label at card `+40,+270`. Use for: benefits, stats, steps.
**Two-up** variant: `[120,440,820,420]` and `[980,440,820,420]`.

### 4. stat-hero — one number that lands
```
┌──────────────────────────┐   context line, then a giant figure, then the takeaway
│        2.4M              │   figure ~180–260px type
│  videos shipped in 2025  │
└──────────────────────────┘
```
`context [0,340,1920,60] center` · `figure [0,410,1920,260] center` · `caption [0,700,1920,70] center`.
Pair with a count-up (see recipes). Use for: the one number in a data story.

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
`bar [80,860,760,150]` anchored bottom-left; rise in with `position [0,60]→[0,0]`.
See `blocks.md` for the block.

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
strata studio --width 1080 --height 1080      # square
strata studio scene.json                      # re-open a scene to nudge its boxes
```
**Always pass the piece's real canvas** (or a scene, which carries its own size). The studio
fits any aspect to the window, snaps to the same 12-column grid used here, and writes a
`*.guide.json` whose boxes are ready to author from directly.
