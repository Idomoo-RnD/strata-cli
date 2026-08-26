# Personalization & data-driven batch — one template, many videos

> **Charts can be real animation instead of swapped images — but read the honesty rule.**
> `strata chart bars` emits bars as named solid layers that grow from the baseline, with
> named value labels. `render --data` swaps **text, media and audio by layer name — nothing
> else** (that is what the API accepts), so the value LABELS personalise per row but a bar's
> HEIGHT (and a donut's sweep) is baked into the layer when it is emitted. Per-viewer heights
> are therefore **one emitted scene per row**, not one template — see *Charts per viewer*
> below. A swapped-image chart remains the route when a scene per row is not acceptable.

Idomoo's superpower: a single IDM is a **template** whose layers are **placeholders** replaced per-viewer at generate time, keyed by **layer name**. The animation, layout, effects, and timing stay exactly as authored; only the *content* (a text value or a media asset) swaps. This is how you render thousands of personalized variants from one scene — something pure motion-graphics tools can't do.

## Contents

- [⚠️ The keys are LAYER NAMES — so duplicates silently break personalization](#the-keys-are-layer-names--so-duplicates-silently-break-personalization)
- [Author the template for replacement](#author-the-template-for-replacement)
- [Right-to-left values (Hebrew, Arabic) in an LTR-built template](#right-to-left-values-hebrew-arabic-in-an-ltr-built-template)
- [Get the real placeholder contract — `--emit-timeline`](#get-the-real-placeholder-contract----emit-timeline)
- [The batch flow — `strata render --data` (VERIFIED)](#the-batch-flow--strata-render---data-verified)
- [Charts per viewer — `strata chart`](#charts-per-viewer--strata-chart)
- [Notes](#notes)

## ⚠️ The keys are LAYER NAMES — so duplicates silently break personalization
Every `media`/`text`/`audio` entry the API replaces is keyed by its layer name. Names must be
unique **across the whole scene, every comp included**; when they aren't, the compiler
auto-renames (`label` → `label_2`) and warns. The video still renders correctly, so the damage
is invisible: the key your integration must send has changed. **Fix the names, never ship past
that warning** — and confirm the real keys with `strata render … --emit-timeline t.json`
(below), which prints exactly what the API sees.

## Author the template for replacement
- **Name every layer meaningfully and uniquely** — names are the replacement keys (`first_name`, `hero_photo`, `monthly_amount`, `cta_label`). Duplicate names are both a render bug (auto-uniquified) and a personalization hazard — keep them distinct.
- **Text** — assume values longer *and* shorter than your sample. **Text auto-fits its box** (the compiler defaults `shrink:true`), so a long value scales down to fit rather than overflowing — verified: a long name in a fixed box shrinks while a short one stays large. Still give generous box width and set a `min_size` if you don't want it shrinking below a floor; opt out with `"shrink": false`. Choose alignment deliberately (left-aligned grows right; centred grows both ways). Never split one personal value across hand-positioned layers. Put a realistic **long** sample in the scene so the layout is proven against the hard case.
- **Media** — assume any aspect ratio arrives. `fit:"fill"` for full-bleed slots (crops to cover) or `fit:"fit"` over a designed backdrop (letterboxes). Anchor at the box centre so Ken-Burns works on any replacement.
- **Animations are content-agnostic** — per-character text animators adapt to any string; prefer `percentage` range units over `index` so 6- and 14-character names both cascade.
- **Graphs, charts, progress wheels/rings, gauges — they must be real IMAGE files, or `strata chart` layers.** Idomoo replaces media **by layer name**, so a personalised data visual has to be an `image` layer whose file **actually exists** (generate it with `strata generate image`, or use the supplied asset) — a ring or bar drawn by hand from native solids/masks has nothing to swap, so every viewer would see the same numbers. The other route is `strata chart bars|donut|line` (*Charts per viewer* below): its value labels personalize through `render --data`, its heights and sweeps are baked — so it animates per viewer only as one emitted scene per row. Two routes, both valid; a hand-built native chart is neither. Author it at the canonical/full state, give it a unique meaningful name (`donut_savings`, `progress_ring`), and animate only the **reveal** (mask wipe, scale, opacity) so the replacement image still animates. Native-shape data-viz recipes are for **static** data only. Don't leave a `src` path that doesn't exist — it fails the compile.

## Right-to-left values (Hebrew, Arabic) in an LTR-built template

The engine lays RTL out correctly on its own ([format.md](format.md)) — the risk is the
**layout**, not the direction. A template proved against `"Jonathan"` and then fed `"יונתן"`:

- the line now grows from the **right**, so a left-anchored box leaves a hole on one side and
  overflows on the other — right-align the slot, and put its **right** edge on the grid line;
- the font must carry the script, or the render **crashes** (error 3000) rather than looking
  wrong — run `strata glyphs` against a real sample value, not the English one;
- prices, dates and Latin brand names stay LTR inside the RTL line, which is correct;
- mixed audiences mean the SAME template may receive both — so test both extremes, and prefer
  centred or symmetric slots when a template must serve both directions.

⚠ There is no `rtl` switch to set — the property exists in the schema but is a **no-op**
(measured). Do not treat it as the fix.

## Get the real placeholder contract — `--emit-timeline`
Before wiring any integration, have the CLI hand you the **exact request body it POSTs to
`/scenes/generate`** — no guessing at the shape:

```bash
strata render scene_v1.json --library <id> --emit-timeline timeline.json
```

Writes the payload the API **accepted** (post-retry, so nothing the schema rejects is in
it). Shape: `{ timeline: { scene_order, scenes: [ { scene_id, media, text, audio } ] }, output: {…} }`,
where each entry is `{ "key": "<layer name>", "val": <value>, … }` — `val` is a string for
media (a `pal://…` asset ref or a colour) and `[{ "text": "…" }]` for text. That is the
substitution contract: **replace `val` per viewer, keep `key`**. Also in `--json` output as
`timeline_path` + the payload inline as `timeline`.
Use it to: confirm which layers are actually replaceable, hand a backend team a real
example, or drive `/scenes/generate` directly. Note it needs one real render (the
`scene_id` comes from the cloud), and `snapshot` does not emit it.

## The batch flow — `strata render --data` (VERIFIED)
1. Build and approve **one** scene; confirm it renders (`strata render … --library <id>`).
2. Prepare a **data set** — a JSON array (`--data` reads JSON, not CSV; convert a CSV first),
   one object per viewer, keys = **layer names**, values = the text, or a **public URL** for media:
   ```json
   [ { "first_name": "Dana",  "monthly_amount": "$48",  "hero_photo": "https://s3.us-east-1.amazonaws.com/assets-temp.idomoo.ai/images/dana.png" },
     { "first_name": "Marco", "monthly_amount": "$112", "hero_photo": "https://t.idomoo.com/7c1e….jpg" } ]
   ```
   (A media URL comes from `generate image`'s printed `url:`, or `strata upload` for the
   viewer's own photo — a local path will not work, the cloud renderer cannot read it.)
3. Render them: **`strata render scene.json --library <id> --data rows.json -o out.mp4`**.
   The template is uploaded and exported **once**, then one `/scenes/generate` runs per row;
   the jobs poll concurrently and land as `out_1.mp4`, `out_2.mp4`, … (`--json` lists each
   file with its row and URLs). A single object instead of an array renders one variant —
   also valid for `snapshot`, which is the cheap way to proof a personalized frame.
   *Verified:* two rows → two videos, each showing its own greeting and amount.
4. **Keys are layer names and must match exactly** — an unknown key fails the render before
   anything is spent (`--data keys that match no placeholder layer: amout`). Text values
   replace the text; **media values must be public URLs** (a `generate` URL, or `strata
   upload` for the user's own file — never a local path). Per-viewer **audio** (a TTS that
   says the name) is not covered: generate that narration per row first and pass its URL as
   the audio layer's value. A per-viewer **spoken presenter** (the clip says the name) is the
   same idea one step up: one presenter still → per-row `generate narration` → per-row
   `generate avatar --image-key <key>` (the key from the first call, so the still is not
   re-uploaded 500 times) → each clip's printed URL as that row's `video` value; never a TTS
   layer on top of it ([avatar.md](avatar.md)). Clips return at slightly different lengths per
   name — `playback_mode:"hold"` on the slot, never a retime.
5. **Scale:** rows are independent jobs, so chunk a large set into files of ~50 rows and run
   the chunks in waves ([assets.md](assets.md)); `--json` maps each `out_N.mp4` to its row —
   keep that mapping, it is the delivery manifest. Spot-check a few finished files per chunk
   (`strata captions` on a spoken one) before the next wave.

## Charts per viewer — `strata chart`

```bash
strata chart bars  --data data.json --box x,y,w,h [--font ./font.ttf] [--accent #hex] [--stagger 0.12] [--grow 0.9] [--start 0.5] [--prefix chart] [scene.json]
strata chart donut --value 72 --box x,y,w,h [--sweep 1.5] [--suffix %] [scene.json]
strata chart line  --series series.json --box x,y,w,h [--draw-on 2.0] [--stroke 6] [scene.json]
```

`data.json` is `[{"label":"Mon","value":42}, …]`; `series.json` is `[3,7,5,…]`. With a trailing
`scene.json` the layers are merged in (validate before compiling); without one they print to
stdout for pasting. `bars` emits three named layers per datum — `<prefix>_bar_<label>` (a
solid anchored at the baseline, `scale [1,0]→[1,1]` `outCubic` over `--grow`, staggered by
`--stagger` from `--start`), `<prefix>_val_<label>` (the number, fading in after the bar
settles) and `<prefix>_lab_<label>` (the axis label). The highest bar takes `--accent`; the
others take the tool's base colour, and the ink is its default — **re-colour the emitted
layers to the brand or the subject's palette after merging** (only `--accent` is a flag; a
palette nobody chose is the [anti-slop.md](anti-slop.md) tell). Snap each bar's `start` to
`strata beats` onsets when there is music ([generative-fx.md](generative-fx.md)).

**What personalises, honestly.** The emitted heights are computed from `data.json` and written
into each bar's `box`; `render --data` can change `<prefix>_val_*` (text) but cannot change a
box or a scale keyframe — the API swaps text, media and audio only. So:

- **Same chart shape, per-viewer numbers only** (a KPI card where the picture is illustrative)
  → one template, `render --data rows.json` with the `_val_` keys. State in the storyboard
  that the bars are not the viewer's bars.
- **The viewer's own bars** (a weekly recap, a savings report — any piece where the chart is a
  factual claim, see [nonfiction-formats.md](editorial/nonfiction-formats.md)) → **one scene
  per row**: a short script loops the rows, writes each row's five values to `row_<id>.json`,
  runs `strata chart bars --data row_<id>.json … scene_<id>.json` on a copy of the approved
  template, then `render scene_<id>.json --library <id> --data row_<id>.json` for the text
  keys. Unique filenames per row (parallel renders of one filename collide), rendered in
  waves of ~25 ([assets.md](assets.md)), every file in the **same** library. Proof it first:
  `snapshot` two rows with very different values and compare the bar heights before the batch.
- **A swapped image per viewer** (the classic route) when a scene per row is not acceptable —
  the chart image is generated per viewer and passed as a public URL in the row; never through
  `strata upload` for personal data ([upload.md](upload.md)).

`chart donut`'s sweep and `chart line`'s draw-on are baked the same way; their value text personalises.

## Notes
- Keep all variants in **one library** (don't mint a new library per render).
- Time the personalized reveal **early but not at t=0** — videos often start muted, so a name shown at the very start gets missed.
- Give personalized content a visual tell (distinct colour/size/weight) so the viewer notices it's about them.
- Pair voiceover with the visual so the personal value lands ("Dana, you saved $48 this month…").
- For the cloud-managed brief/blueprint flow (not local IDM authoring), use the separate `idomoo` CLI.
