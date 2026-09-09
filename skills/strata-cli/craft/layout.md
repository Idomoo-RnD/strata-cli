# Design and layout

Reading order, grid, weight and colour — so the frame reads clean and on-brand.

Part of the craft manifesto — the index, and every other part, is in [craft.md](../craft.md).

## Part 2 — Design & layout (so it reads clean and on-brand)
The grid, the named layouts and the rules that make any frame read as designed — snap to the grid, no dead third, one focal point at ~2–3× the next, consistent gutters, three type sizes, one accent with one job — are [layouts.md](../layouts.md), *Rules that make any layout read as designed*. What that file does not carry:
- **Stamp test:** glance at the frame — what did I see first? If it isn't the most important thing, fix size/contrast/colour/placement. Product + CTA win the first glance.
- **Compose like a frame of film, not a web page** — **headroom and lead room** for anything that faces or moves; the subject on a **third** when the frame has direction, **centred** when it is a statement. "Less is more" reads premium.
- **Colour:** brand atoms exact; the grade is set per beat, not once for the piece (*Depth, light & atmosphere*).
- **Typography:** two typefaces is the usual ceiling — vary weight, size and colour rather than adding a face, unless the piece is about type. Define fallback fonts; the font must cover every glyph used.
- **Contrast — text must stay legible over whatever is behind it.** A backing **matched to the background brightness**: near-opaque over bright footage (a 45% scrim over a white screen only makes grey; white text then washes out), light/low-opacity over dark. White-on-light and dark-on-dark fail; aim for ~4.5:1. When in doubt, snapshot and check the caption is crisp.
- **Safe areas & no overlap:** text within ~90% **title-safe**; **no two text/visual elements overlap while both are on screen** — real gaps on the grid, or separate them in **time** (stagger their `start`). `validate`/`compile` warn on overlaps and boxes off the frame; fix those before rendering.
- **Over a personalised image slot** legibility must survive whatever image arrives, and a scrim is the **last** answer — the ordered alternatives are in [anti-slop.md](anti-slop.md), *Text backing*.
- **Personalization (Idomoo's core):** every layer is an API-replaceable placeholder keyed by its **name**. Size text boxes for the longest value (`shrink`, `min_size`), `fit:"fill"` for full-bleed media slots, per-character animators for any string length, and time the personalized reveal **early but not at t=0** (videos start muted). Graphs are swappable images whose animation reveals whatever data arrives. Details: [personalization.md](../engine/personalization.md).
