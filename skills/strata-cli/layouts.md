# Frame layouts & the grid — compose, don't guess coordinates

Blocks (`blocks.md`) are components; blueprints (`blueprints.md`) are whole-video
structures. **This file is the frame level: where things sit.** Pick a named layout that
matches the intent and fill it, instead of inventing arbitrary boxes.

Always check with **`strata preview scene.json --at <sec> --grid`** (local wireframe, free)
before rendering.

**Canvas size and generated media:** `generate image` is 1376×768 and `generate video` is
1280×720, so a full-bleed generated plate in a 1920 comp is upscaled 1.5× — author at 1280×720
when the footage fills the frame, or keep it in a framed slot (assets.md, *Source resolution*).

---

## Contents

- [The grid (landscape 1920×1080)](craft/grid.md#the-grid-landscape-19201080)
- [The other three canvases](craft/grid.md#the-other-three-canvases)
- [Layouts](craft/layouts-catalogue.md#layouts)
  - [1. hero-center — one message, maximum impact](craft/layouts-catalogue.md#1-hero-center--one-message-maximum-impact)
  - [2. split-media-left — product/UI beside the pitch](craft/layouts-catalogue.md#2-split-media-left--productui-beside-the-pitch)
  - [3. three-up — three parallel points/stats](craft/layouts-catalogue.md#3-three-up--three-parallel-pointsstats)
  - [4. stat-hero — one number that lands](craft/layouts-catalogue.md#4-stat-hero--one-number-that-lands)
  - [5. title-over-media — full-bleed footage with copy](craft/layouts-catalogue.md#5-title-over-media--full-bleed-footage-with-copy)
  - [6. quote-full — testimonial / pull quote](craft/layouts-catalogue.md#6-quote-full--testimonial--pull-quote)
  - [7. list-reveal — sequential points](craft/layouts-catalogue.md#7-list-reveal--sequential-points)
  - [8. lower-third — name/label over anything](craft/layouts-catalogue.md#8-lower-third--namelabel-over-anything)
  - [9. speaker-card — a person, their title, their slot](craft/layouts-catalogue.md#9-speaker-card--a-person-their-title-their-slot)
  - [10. diagonal-split — footage one side, solid the other](craft/layouts-catalogue.md#10-diagonal-split--footage-one-side-solid-the-other)
  - [11. media-mosaic — the multiscreen opener](craft/layouts-catalogue.md#11-media-mosaic--the-multiscreen-opener)
  - [12. card-inset — a safe card over a busy background](craft/layouts-catalogue.md#12-card-inset--a-safe-card-over-a-busy-background)
  - [13. product-card-split — the e-commerce frame](craft/layouts-catalogue.md#13-product-card-split--the-e-commerce-frame)
  - [14. schedule-row — one session in the programme](craft/layouts-catalogue.md#14-schedule-row--one-session-in-the-programme)
  - [15. meta-rail — the evenly-spaced footer strip](craft/layouts-catalogue.md#15-meta-rail--the-evenly-spaced-footer-strip)
  - [16. frame-chrome — the persistent HUD](craft/layouts-catalogue.md#16-frame-chrome--the-persistent-hud)
  - [17. marker-headline — solid blocks behind each line](craft/layouts-catalogue.md#17-marker-headline--solid-blocks-behind-each-line)
  - [18. logo-wall — sponsors / partners / integrations](craft/layouts-catalogue.md#18-logo-wall--sponsors--partners--integrations)
  - [19. avatar-cluster — "15 speakers", many faces at once](craft/layouts-catalogue.md#19-avatar-cluster--15-speakers-many-faces-at-once)
  - [20. device-trio — an app in three screens](craft/layouts-catalogue.md#20-device-trio--an-app-in-three-screens)
  - [21. title-wall — the phrase repeated as a pattern](craft/layouts-catalogue.md#21-title-wall--the-phrase-repeated-as-a-pattern)
  - [22. carousel-index — "which one of these am I on"](craft/layouts-catalogue.md#22-carousel-index--which-one-of-these-am-i-on)
  - [23. nav-bar frame — the web-page pastiche](craft/layouts-catalogue.md#23-nav-bar-frame--the-web-page-pastiche)
  - [24. ghost-display — oversized type as background](craft/layouts-catalogue.md#24-ghost-display--oversized-type-as-background)
- [Reflow to vertical (and square)](craft/reflow-and-rules.md#reflow-to-vertical-and-square)
- [Rules that make any layout read as *designed*](craft/reflow-and-rules.md#rules-that-make-any-layout-read-as-designed)
- [Letting the user lay it out — `strata studio`](craft/studio.md#letting-the-user-lay-it-out--strata-studio)
- [Hand the layout to the user — `strata studio`](craft/studio.md#hand-the-layout-to-the-user--strata-studio)

Pick the layout by name from the list above, then open its entry. Nothing here needs reading end to end.
