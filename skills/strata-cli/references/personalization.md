# Personalization & data-driven batch — one template, many videos

Idomoo's superpower: a single IDM is a **template** whose layers are **placeholders** replaced per-viewer at generate time, keyed by **layer name**. The animation, layout, effects, and timing stay exactly as authored; only the *content* (a text value or a media asset) swaps. This is how you render thousands of personalized variants from one scene — something pure motion-graphics tools can't do.

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
- **Graphs, charts, progress wheels/rings, gauges — they must be real IMAGE files.** Idomoo replaces media **by layer name**, so a personalised data visual has to be an `image` layer whose file **actually exists** (generate it with `strata generate image`, or use the supplied asset) — a ring or bar drawn from native solids/masks has nothing to swap, so every viewer would see the same numbers. Author it at the canonical/full state, give it a unique meaningful name (`donut_savings`, `progress_ring`), and animate only the **reveal** (mask wipe, scale, opacity) so the replacement image still animates. Native-shape data-viz recipes are for **static** data only. Don't leave a `src` path that doesn't exist — it fails the compile.

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
   the audio layer's value.

## Notes
- Keep all variants in **one library** (don't mint a new library per render).
- Time the personalized reveal **early but not at t=0** — videos often start muted, so a name shown at the very start gets missed.
- Give personalized content a visual tell (distinct colour/size/weight) so the viewer notices it's about them.
- Pair voiceover with the visual so the personal value lands ("Dana, you saved $48 this month…").
- For the cloud-managed brief/blueprint flow (not local IDM authoring), use the separate `idomoo` CLI.
