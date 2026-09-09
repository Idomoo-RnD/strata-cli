# The grid, and the four canvases

The column grid every layout is placed on, and how it changes across landscape, vertical, square and the wide canvas.

Part of the frame-layouts reference — the index, and every other part, is in [layouts.md](../layouts.md).

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
landscape first, then reflow (*Reflow to vertical* below) — never letterbox.

| canvas | margin | content | columns | header band | body band | bottom safe line |
|---|---|---|---|---|---|---|
| **1920×1080** 16:9 | 120 | 1680 | 12 @118 / 24 | 120–360 | 420–830 | 950–990 |
| **1080×1920** 9:16 | 72 | 936 | 6 @136 / 24 | 160–460 | 560–1500 | 1690–1770 |
| **720×1280** 9:16 (the 1080×1920 grid × 2/3) | 48 | 624 | 6 @91 / 16 (626 — trim the last column by 2 px) | 107–307 | 373–1000 | 1127–1180 |
| **1080×1350** 4:5 | 72 | 936 | 6 @136 / 24 | 150–380 | 430–1120 | 1180–1250 |
| **1080×1080** 1:1 | 72 | 936 | 6 @136 / 24 | 120–300 | 340–800 | 900–960 |

**Which 9:16 canvas:** author at **720×1280** when generated footage fills the frame — it is the clip's native size, and a 1080×1920 comp would upscale it 1.5× before any push-in; author at 1080×1920 when the piece is graphics-led and footage sits in a slot ([assets.md](../assets.md), *Source resolution*).

For 9:16 and 4:5 keep text out of the **top 10% / bottom 15%** (platform UI chrome) and off the right-hand ~12% in the lower half (the Reels/TikTok button stack).

---
