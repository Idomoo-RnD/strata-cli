# Steps 3–5 — scene JSON, preview, validate and compile

Writing the scene, fixing composition for free before spending a render, and the offline checks that must be clean first.

Part of the production workflow — the index, and every other part, is in [workflow.md](../workflow.md).

## 3. Scene JSON

Write the scene JSON to the approved storyboard (compact format — [format.md](../format.md) is the
spec). A build order that keeps decoration from creeping in: blocking (boxes, cuts, holds, on
`preview --grid`) → primary motion (the one idea per shot) → secondary motion (stagger,
follow-through, counters) → finishing (effects, grade, sound). Passes 1–3 are judged on previews
and snapshots, finishing on the rendered MP4.

Pick a frame layout from [layouts.md](../layouts.md) and snap boxes to its grid rather than
inventing coordinates; reuse blocks (`strata add <block>`, [blocks.md](../engine/blocks.md)) and follow a
[blueprint](blueprints.md) for the video type instead of building from scratch; a unique name on
every layer; iterate the timeline.

**Hand-write vs generate.** Scene JSON is written by hand by default — it stays the readable
source of truth. Reach for a generator script when the scene contains computed or heavily repeated
values (polygon paths, keyframes snapped to `strata beats` onsets, per-glyph offsets, big grids of
near-identical layers, audio-envelope arrays) — code gets that math right where hand-typing
silently doesn't. A script for a simple scene (a handful of layers with hand-picked boxes) is
overengineering. Mixed scenes can mix: hand-write the scene, generate just the computed part (a
path, a keyframe list) and paste it in.

**Write scene JSON with the file-writing tool, never through a shell heredoc.** Real copy contains
apostrophes, `$`, backticks and backslashes — *measured:* a heredoc broke on the apostrophe in
`BIG JOE'S` and silently produced malformed JSON. The shell is not a text editor; the same applies
to any file longer than a few lines — a storyboard, `decisions.md`, a prompt file — a quoted heredoc
has failed on a markdown storyboard in the same way (*measured*).

## 4. Preview

Preview the layout locally before spending any render. `strata preview scene.json --at <sec>
--grid` draws a free, instant wireframe (every box, the 12-column grid, title-safe and bottom safe
line, thirds/centre). Fix the composition against [layouts.md](../layouts.md)'s rules and re-preview
each key shot (`--at`) until it reads right. This is where design gets fixed; renders are for
confirming, not discovering.

`strata preview` draws layer boxes, not masks, and only the comp given with `--comp` (the main comp
by default) — a scene built from masks previews as blank rectangles; use `snapshot` to see the frame.
Preview draws boxes, not glyphs, so it cannot show where text sits *inside* its box — and vertical
text anchors to the box bottom (`align "… top"` is not honoured; verified). Compute vertical
positions from `box_y + box_h`, and confirm real type placement with a cheap snapshot —
`strata snapshot scene.json --library <id> --at <sec>`. The default is t = 1 s, so a piece that
builds to a reveal has nothing there; pick the moment the type is on screen rather than building a
throwaway probe scene (which also litters the user's library).

## 5. Validate and compile

`strata validate scene.json` is free and offline: it names any bad key or layer, warns about the
known exporter traps, flags composition tells (scrims under text, rules under titles, side bars,
list markers, effect stacks — each naming its [anti-slop.md](../craft/anti-slop.md) pattern; fix them or say
why the brief earns them, and check the scrim warning's two layers are ever on screen together),
and with `--data rows.json` runs the localisation guard: per-row auto-fit shrink and per-row glyph
coverage. Then `strata compile scene.json -o out.idm`.

**Version the filename on every new cut — never overwrite the scene just rendered.** The scene's
filename is what the user sees in their Idomoo library (the upload is named `basename(<scene>)`),
and the MP4 takes its name from it too. Keep the descriptive stem and add the version so the
library sorts them together: `spring_sale_v1` → `spring_sale_v2` → `spring_sale_final` (`_rc`,
`_15s`, `_9x16` — whatever names the change). Overwriting clobbers the previous MP4 and removes
the way back to a cut the user preferred. State the new filename when reporting a render.

**Version history is automatic — use it.** Every `compile`/`render` snapshots the scene into
`.strata/versions/` beside it (unchanged content makes no new version), and every `.idm` carries an
embedded version stamp (`strata.meta` — version, parent, scene hash; survives the Idomoo exporter,
`strata inspect` prints it). So: on "redo / go back", run `strata versions <scene>` and name the
version being continued from; to restore one, `strata revert <scene> --to N` snapshots the current
state first, so revert never loses anything (`-o other.json` branches instead); and a bare `.idm`
tells `strata inspect` which version it is and what it descended from. A previous cut is never gone
— check `strata versions` before telling a user otherwise. Details: [format.md](../format.md).
