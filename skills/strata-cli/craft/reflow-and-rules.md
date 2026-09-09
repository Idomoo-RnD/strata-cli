# Reflowing a layout, and what makes one read as designed

Taking a landscape layout to vertical or square, and the rules that separate a composed frame from a placed one.

Part of the frame-layouts reference — the index, and every other part, is in [layouts.md](../layouts.md).

## Reflow to vertical (and square)
The reference templates all reflow the **same frame**, they don't crop it:

- **split-media → stacked.** Media takes the top ~55% full-width; the copy stack drops below,
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
- **Snap to the grid.** Left edges share a column, widths are column spans; arbitrary
  numbers are the amateur tell.
- **Use the whole frame.** No dead third — centre the cluster vertically, or run a clear
  header / body / bottom-safe-line structure.
- **One focal point per frame.** Size is hierarchy: the hero should be ~2–3× the next. If
  everything is medium, nothing reads.
- **Consistent gutters** (40–80). Uneven spacing looks accidental.
- **Align, don't centre everything.** Left-aligned copy blocks read faster; reserve centring
  for hero/end frames.
- **One accent colour, one job.** In every professional-looking template the accent lands on
  exactly one thing per frame — the chip, the price, the button — never three.
- **Two type sizes are not a hierarchy.** Use three: display (90–140), body (24–30),
  meta (18–22, letterspaced, uppercase). Meta carries dates, roles, indices, URLs.
- **Corner metadata is cheap polish.** A brand mark, a running index and a small caption
  (layout #16) make a plain frame look like part of a system.
- **Never overlap two text blocks** while both are visible (`validate` warns) — separate in
  space or stagger in time.
- **Vertical text position is anchored at the box BOTTOM.** `align: "… top"` is not honoured —
  the glyphs render where `bottom` would (verified). So a row's y-coordinate is
  `box_y + box_h`, not the top of the type. Size text boxes to the copy (~`1.3 × size` per
  line) and place them by their bottom edge, or use `"… middle"` with the box centred on the
  target. Measured numbers: [format.md](../format.md).
- **Verify visually:** `strata preview --grid` → check balance/alignment → fix → only then
  `snapshot`/`render`. ⚠ **`preview` draws BOXES, not glyphs** — it cannot reveal where text
  sits inside its box. Use `snapshot` for that.

---
