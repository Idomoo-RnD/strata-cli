# Generated assets, personalization and charts

Where scene elements come from when they are not files on disk, and how to build a scene whose elements can be swapped per viewer.

Part of the scene-format reference — the index, and every other part, is in [format.md](../format.md).

## Generating assets (Idomoo AI API)

The CLI generates the media a scene needs — `strata generate image|video|fastvideo|avatar|narration|music`,
plus `voices`. **The full reference is [assets.md](../assets.md)** (flags, reference images, the
upload rule, `.jet`); **prompting `generate video` is [video-generation.md](../video-generation.md)**.
Two facts worth having here, next to the scene syntax:

- The saved **local path** goes in the scene's `src`; the printed **`url:`** is what you pass to
  any `generate` flag that takes a URL (`--first-frame`, `--ref-*`, avatar inputs). Generated
  assets are already hosted — never re-upload one.
- **Native sizes:** `generate image` returns **1376×768** (16:9), `generate video` is capped at
  **1280×720**, avatar is 1280×720 @ 25 fps. In a 1920×1080 comp a full-bleed generated plate is
  already upscaled ~1.4–1.5× before any push-in — see *Source resolution* in assets.md.

⚠ `strata generate video <image> --prompt "…"` is the **pre-1.0.82** form. The CLI now refuses it
(the image path would otherwise become the prompt). New form: `generate video "<prompt>" --first-frame <url>`.

## Personalization — design for replaceable elements

Idomoo is a **personalized video platform**: every layer is a placeholder (VASCO `placeholder` defaults to true) whose content can be replaced per-viewer at generate time via the API, keyed by **layer name**. Replacement swaps only the content — text value or media asset — while the layer's box, timing, animations, effects, and masks play back exactly as authored. When the user says the video is personal/personalized, author for content that WILL change:

- **Name layers meaningfully** (`first_name`, `hero_photo`, `monthly_amount`) — names are the replacement keys.
- **Text**: assume values longer and shorter than your sample. Give text boxes generous width, keep `shrink: true` (the default) with a sensible `min_size`, choose alignment deliberately (a left-aligned box grows rightward; centered grows both ways), and never split one personal value across multiple hand-positioned layers.
- **Media**: assume any aspect ratio may arrive. Use `fit: "fill"` for full-bleed slots (crops to cover) or `fit: "fit"` over a designed backdrop (letterboxes). Anchor at the box center so zooms/Ken Burns work on any replacement.
- **Animations are content-agnostic**: per-character text animators adapt to any string automatically — prefer `percentage` range units over `index` when the text varies. A 6-character and a 14-character name both cascade correctly.
- Sample values in the scene should be realistic *long* examples, so the layout is proven against the hard case.

## Graphs & charts — dynamic images

Graphs are **images, not drawn primitives**. The data-dynamism comes from swapping the image asset at generate time while the authored animation stays identical:

- Author the graph image at its **canonical/full state** — a bar chart at 100%, a progress ring fully closed, a line chart with the complete curve.
- **Animate the reveal, not the data**: a mask wipe in the direction the graph grows (left→right rect morph for horizontal bars, bottom→top for columns, an expanding ellipse for rings), or a scale/opacity entrance. When the API replaces the asset with a 50%-filled variant of the same graph, the same wipe plays and the viewer simply sees a 50% graph.
- Never try to encode the data in the animation (e.g. stopping a wipe at 62%) — the image carries the data; the animation only presents it.
- Name the graph layer for replacement (e.g. `savings_graph`) and keep the layer box's aspect ratio equal to the graph image's, so every swapped variant lands pixel-identical.
