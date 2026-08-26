---
name: strata-cli
description: Make motion-design and AI video with the strata CLI — scene JSON compiled to .idm, rendered via Idomoo; generate images, AI video (text, image, keyframe, reference-driven), talking presenters, narration, music. Use for any strata / IDM / VASCO video, motion graphics, kinetic type, explainers, promos, personalized or data-driven video, image and reference-image generation, brand kits, Figma-to-video, and ffmpeg editing (trim, join, reframe, speed ramps, grade). Not for the Idomoo cloud briefs API.
---

# Strata CLI — cinematic motion design, authored as IDM/VASCO

Make **bold, cinematic, story-driven motion graphics** — not slideshows. VASCO is a real 3D motion-design engine (3D layers, a moving camera, masks, effects, per-character text animators, a keyframe tween engine); use that power. The default is *great*: deliberate shots, alive frames, motion with meaning. This file is the **router and the workflow**; the craft lives in [craft.md](references/craft.md), the syntax in [format.md](references/format.md), the patterns in [recipes.md](references/recipes.md).

## Contents

- [Before authoring — five checks, in order](#before-authoring--five-checks-in-order)
- [Workflow](#workflow)
- [Commands](#commands)
- [The traps that cost renders](#the-traps-that-cost-renders)
- [The craft, in one screen](#the-craft-in-one-screen)
- [References — condition → file](#references--condition--file)

---

## Before authoring — five checks, in order

- **A0. The request comes with material** (a storyboard image, a script, a PDF, a URL, reference sheets, footage, a voice) → read [intake.md](references/intake.md) **first** and treat the material as the brief. Open every attachment and say what it changes; transcribe a storyboard into the shot table; route each scene to its generation mode; present *that* as the storyboard with an execution plan. A supplied storyboard is executed, not redesigned — and for an executed live-action board the four references of check A apply only to the frames the board leaves to be *authored* (an end card, a title, a graphic): anti-slop and motion-design for those frames, layouts / video-layouts only if such a frame is composed, step 2½'s style only for authored graphics; the clips themselves are governed by intake, the production bible and the video-generation files. When two or more clips share a person, product or set — or consistency is requested — build the **[production bible](references/production-bible.md)** before the first clip: one sheet and one canonical identity block per principal, reused byte-identical in every prompt.
- **A. The user has not dictated layout and motion** → read **four references before authoring, every time**: [anti-slop.md](references/anti-slop.md) (the defaults not to reach for, and the test that catches them), [layouts.md](references/layouts.md) (which frame layout + the grid), [video-layouts.md](references/video-layouts.md) (video as a design element — footage in shapes, type-as-window, split-screen, tracked graphics, subject cut-outs), [motion-design.md](references/motion-design.md) (make it read as After Effects, not web animation). These are what make output *designed* instead of vanilla; without them the default is a flat fade-and-slide slideshow, which is the failure this skill exists to prevent. Pick a named layout, apply the motion techniques, run the "web-animation tells" checklist before shipping.
- **B. It is an EDIT, not a design job** (footage handed over to cut, trim, join, crop/reframe, speed up, loop, mute or swap audio) → do it with ffmpeg per [video-editing.md](references/video-editing.md) and hand back an MP4. Do not build a scene, and never burn titles in with `drawtext` (no easing, no animators — it reads as a watermark). Graphics on top → edit the clip first, then use it as a `video` layer.
- **C. Brand first** — check for `.brand/brand.md` at the start of every job. If it exists, read it and author everything from it (canvas, palette, type ramp with real font paths, components, motion language) — that is what makes a user's videos a set instead of one-offs. If it does not exist and the user supplied brand material (logo, brand book, style guide, existing videos, a website, a Figma file) — or asks for "on brand", "like the last one", a series — **offer to build it first**: *"want me to capture this as a brand document so every video matches?"* Write `.brand/brand.md` from their assets and get sign-off before storyboarding. Build + use workflow and the template: [brand.md](references/brand.md).
- **D. The layout comes from Figma** → read [figma.md](references/figma.md) first. The traps: Figma boxes are canvas-absolute (subtract the frame origin) and web-sized (one uniform scale factor, never stretch x/y differently); `"font": "Inter"` does not compile (fonts are real `.ttf`/`.otf` paths); icons, vectors and gradients are exported to PNG; `visible:false` nodes are skipped; Figma's repeated names (`Rectangle 1`, `Text`) crash the render — rename every layer unique. Compare `strata preview --grid` against the Figma screenshot before rendering, then author motion on top: an imported frame is a layout, not a video.

## Workflow

Copy this list and tick it off; the validation gates (4, 5, 7) are not optional.

- [ ] 1. Assets · [ ] 2. Storyboard + sign-off · [ ] 2½. Style · [ ] 3. Scene JSON · [ ] 4. Preview · [ ] 5. Validate + compile · [ ] 6. Render · [ ] 7. Verify (Definition of Done)

**1. Sort out assets first.** If any person, product or set recurs across clips, the **production bible** comes first ([production-bible.md](references/production-bible.md)) and every generated frame or clip cites it. Clip length is decided here, not fixed later: a clip that comes back **shorter than its scene is never retimed to fit** — cover the scene with **more shots of the same subject** (a companion clip from a reference frame) or **extend** off the last frame ([video-generation.md](references/video-generation.md)); a longer one is trimmed, never sped up. **Generate in waves, in the background** — every asset with no unmet dependency at once (sheets, plates, TTS, music), then every clip whose inputs exist, then only continuations — and author the scene while they render; a 10-scene piece is ~10 min in waves, ~60 serially ([assets.md](references/assets.md)). Parallel renders need unique scene filenames. For each visual element decide: (a) is there a file, or `generate` it; (b) **is it a background plate, or an element that sits OVER other layers?** — anything composited over another layer (plane, mascot, product cut-out, logo sting, person) needs alpha, so it must be a `.jet` — a format fact, not a style choice, and only when the layer really does sit over another. A full-frame background or unoverlapped footage stays an MP4: `matte` is the slowest step and `.jet` is lossy, so do not cut out a subject that has nothing to composite against (`strata matte clip.mp4` for footage of a person; green screen + `strata jet --method chroma` otherwise); an `.mp4` overlay arrives as an opaque rectangle; (c) **every image becomes a video** unless it is an icon/logo ([assets.md](references/assets.md), *Every image becomes a video*). Ask about narration and music. Text layers need a real `.ttf`/`.otf` — the brand's font when there is one; otherwise a system face that covers the copy (`C:\Windows\Fonts`, `/System/Library/Fonts`, `/usr/share/fonts`) or a downloaded open-licence family (Google Fonts as `.ttf`), checked with `strata glyphs`; say which was used and that a client piece may need a licence check. Pick the scene fps from the footage it carries — `generate video` clips arrive at 24 fps, avatars at 25 — and keep every clip, `.jet` and envelope at that one rate.

**Ask these once, together, before the storyboard** (they are scattered through the steps below so none is skipped): narration and music (or a licensed track); platform and canvas (16:9 / 9:16 / both); brand assets, logo file and fonts; which Idomoo library (reuse or create — step 6); preview grids as the work goes; the studio when a layout is shown (step 2).

**2. Present a STORYBOARD and get sign-off — before any scene JSON.** Apply the craft ([craft.md](references/craft.md)) to plan the piece, then show a storyboard the user can read and approve. Do not start authoring until they confirm; revise the storyboard with them first (cheap to re-cut, expensive to re-render). Format:
- **Title** + one-line **Style** (palette, motion feel, type).
- The **editorial contract** — format / dominant mode / modifiers, the one-sentence **viewer promise**, the **information-release** plan (what the viewer knows before, during, after each beat) — from [editing-director.md](references/editing-director.md). One dominant mode, at most two modifiers; never a generic fast montage. (The editorial blueprint that file describes is not a second document: its rhythm arc, grammar and integrity notes fold into this storyboard's rows and motion notes.)
- A **shot list** — `Time | Visual / Motion | Voiceover | Sound` — one row per shot (~2–4 s), covering the full duration.
- **A layout sketch for the key frames** — a small ASCII wireframe showing *where things sit*, named from [layouts.md](references/layouts.md) (`hero-center`, `split-media-left`, `three-up`, `stat-hero`, `title-over-media`…). A shot list says what happens; only the sketch says what it will look like — so the user can move the logo or resize the stat for free, before any JSON.
- **End frame** (logo/CTA text) and **motion notes** (transition timing ~300–500 ms; transform-based — scale/position/opacity/masks; the intended feel).
- If a presenter is in the piece, **name the route and its trade-off** — filmed (`generate video` + references, the default) or avatar (personalized pieces, fixed plates) — [avatar.md](references/avatar.md). A character who acts and speaks *inside a scene* is not a presenter: that is a filmed dialogue clip (`--ref-image` + `--ref-audio`, [video-generation-advanced.md](references/video-generation-advanced.md)), and avatar.md is not the file for it.

When the framing could go more than one way, **offer 2–3 layout options as side-by-side wireframes** and let the user pick — people recognise the layout they want far better than they can describe it. **Ask once, up front: "want me to show layout preview grids as I go?"** — if yes, run `strata preview … --grid` at each key frame and show it before moving on.

**Every time a layout is shown — the wireframe, layout options, or a preview grid — ask in the same breath: "want to lay it out yourself in the studio, or shall I go with this?"** `strata studio` opens a local browser designer (offline, 127.0.0.1) where they drag and annotate named areas per scene on the same 12-column grid, set roles/colours/notes, and press Save to write a `*.guide.json`; then author from those boxes as given rather than re-inventing the layout. **Launch it at the user's actual canvas** — `strata studio --width 1080 --height 1920` for vertical, `--width 1080 --height 1080` for square — or pass an existing `scene.json` so it picks the size up; it is not 16:9 unless the piece is. Re-open a written scene (`strata studio scene.json`) so they nudge the boxes instead of describing the fix. It runs a local server and waits for Save — launch it only once they say yes, never in an automated run.

**2½. Pick the STYLE** — what kind of video is this? [video-styles.md](references/video-styles.md) has 50, each with its construction, timing values and the tells that sell it. Take the **technique** (shot order, stagger, easing, layer construction) and never the sample's look: if `.brand/brand.md` exists, the brand's palette, type, radii and motion language override the style entirely — style is composition, brand is atoms.

**3. Write the scene JSON** to the approved storyboard (compact format — [format.md](references/format.md) is the spec). Pick a frame layout from [layouts.md](references/layouts.md) and **snap boxes to its grid** rather than inventing coordinates; reuse blocks (`strata add <block>` — [blocks.md](references/blocks.md)) and follow a [blueprint](references/blueprints.md) for the video type instead of building from scratch; **unique name on every layer**; iterate the timeline. **Hand-write by default** — the JSON stays the readable source of truth; reach for a generator script when the scene contains computed or heavily repeated values (polygon paths, keyframes snapped to `strata beats` onsets, per-glyph offsets, big grids of near-identical layers, audio-envelope arrays) — code gets that math right where hand-typing silently does not; a script for a handful of hand-placed layers is overengineering; mixed scenes mix (hand-write the scene, generate just the computed part, paste it in). **Write scene JSON with the file-writing tool, never through a shell heredoc** — real copy contains apostrophes, `$`, backticks and backslashes; *measured:* a heredoc broke on the apostrophe in `BIG JOE'S` and silently produced malformed JSON. The shell is not a text editor; the same applies to any file whose content is user-facing copy.

**4. PREVIEW the layout locally — before spending any render.** Preview draws **boxes, not glyphs**, so it cannot show where text sits inside its box — and vertical text anchors to the box **bottom** (`align "… top"` is not honoured; verified). Compute vertical positions from `box_y + box_h`, and confirm real type placement with a cheap `snapshot`. `strata preview scene.json --at <sec> --grid` draws a free, instant wireframe (every layer's box, the 12-column grid, title-safe + bottom safe line, thirds/centre). **Look at it and fix the composition** — balance (no dead third), alignment to the grid, one clear focal point, no overlaps, CTA on the safe line — and re-preview each key shot (`--at`) until it reads right. This is where design gets fixed; renders are for confirming, not discovering. If the user asked for preview grids, show each one.

**5. Validate, then compile:** `strata validate scene.json` (free, offline — names any bad key/layer, warns about the known exporter traps, **flags composition tells** — scrims under text, rules under titles, side bars, list markers, effect stacks — each naming its [anti-slop.md](references/anti-slop.md) pattern, and with `--data rows.json` runs the **localisation guard**: per-row auto-fit shrink + per-row glyph coverage; fix them or say why the brief earns them) → `strata compile scene.json -o out.idm`.
- **Version the filename on every new cut — never overwrite the scene just rendered.** The scene's filename is what the user sees in their Idomoo library (the upload is named `basename(<scene>)`) and the MP4 takes its name from it, so each round of edits gets its own file: `promo_v1.json` → `promo_v2.json` → `promo_final.json` (`_v3`, `_rc`, `_15s`, `_9x16` — whatever names the change); keep the descriptive stem and add the version so the library sorts them together (`spring_sale_v1`, `spring_sale_v2`). Why: overwriting fills the library with identical names the user cannot tell apart, clobbers the previous MP4, and leaves no way back to a cut they preferred. State the new filename when reporting a render.
- **Version history is automatic — use it.** Every `compile`/`render` snapshots the scene into `.strata/versions/` beside it (unchanged content makes no new version), and every `.idm` carries an embedded version stamp (`strata.meta` — version, parent, scene hash; survives the Idomoo exporter; `strata inspect` prints it). So: (a) on any redo / iterate / "go back" request, run `strata versions <scene>` and **name the version being continued from** ("continuing from v4"); (b) restore with `strata revert <scene> --to N` — the current state is snapshotted first, so revert never loses anything (`-o other.json` branches instead); (c) handed a bare `.idm`, `strata inspect` says which version it is and what it descended from. **A previous cut is never gone** — never tell a user an earlier version is unrecoverable without checking `strata versions` first. Details: [format.md](references/format.md).

**6. Render:** `strata render scene.json --library "<id>" -o out.mp4`.
- **Library — ASK before the first render, never pick one.** The library is the user's Idomoo workspace: renders land in it and stay there, so choosing one silently puts their work somewhere they did not agree to. If the user has not already named a library (this conversation, or a persisted `.idm-library` file), run `strata library list` before rendering and ask, offering both: **reuse an existing one** (show the `id  name` rows so they can point at one — a series belongs in ONE library) or **create a new one** (propose a name from the project; run `strata library create "<name>"` only after they say yes — `create` is idempotent: it reuses an id/name match instead of duplicating and prints `♻️ reusing existing`). A name not seen before is not assumed to exist — `library create "<that name>"` resolves it either way. Then **save the printed id** (persist it, e.g. a `.idm-library` file) and pass that same `--library <id>` on every later render; it logs `Reusing library <id>` (a `Created NEW library` log means the wrong value was passed). Switch only when the user says so. `library list` is **paginated and omits a just-created library**, so an id missing from the listing is not proof it does not exist — pass the id and let `render` verify it. `render` itself refuses to pick one — a non-interactive run fails with the library list rather than uploading somewhere arbitrary.
- Renders take minutes — run them in the background and report the `video_url` / `poster_url`.
- **Only if the user asks for the scene to be TAGGED** (a reusable template / catalog entry, not a one-off): add `--tags manifest.json` here and on `compile`, after reading [tagging.md](references/tagging.md). The manifest rides inside the `.idm`, so the library copy is self-describing. Never invent tags — both vocabularies are closed.

**7. Verify** — run `strata snapshot scene.json --library <id>` for a fast poster-only frame (cheaper than a full MP4) and look at it, then the **Definition of Done** before calling it shipped:
- Message clear in the first 3 seconds? Every shot earns its place? Stillness *and* energy?
- Text legible **muted** and inside the safe area — legible by placement, panel or grade, not by a scrim patched under it?
- **Ran the composition-tells checklist in [anti-slop.md](references/anti-slop.md) and removed one thing?** If two tells are true the frame reads as generated.
- **Every layer name unique across every comp** — and was any `⚠ renamed … duplicate layer name(s)` line read? The compiler fixes duplicates by renaming (`label`→`label_2`), which **changes the personalization key**, so fix the scene rather than accept the rename.
- Holds on the CTA; nothing loops cheaply; motion blur on moving layers?
- **Would you ship this with your name on it?** If not, fix it before delivering.

Debug with `--vasco` or `strata inspect out.idm`.

## Commands

The `strata` CLI is a standalone binary (embeds its own runtime — never install Node/npm for it). **Check first:** `strata version`; also try `~/.local/bin/strata` (Unix) and `%LOCALAPPDATA%\Programs\strata\strata.exe` (Windows). **Only if missing:** Linux/macOS `curl -fsSL https://raw.githubusercontent.com/Idomoo-RnD/strata-cli/main/install.sh | bash` (set `STRATA_SKILL=skip STRATA_AUTH=skip` in agents — the installer otherwise prompts for skills and credentials); Windows `irm https://raw.githubusercontent.com/Idomoo-RnD/strata-cli/main/install.ps1 | iex` (same env vars); or a binary from the [releases page](https://github.com/Idomoo-RnD/strata-cli/releases), run by path.

Full notes per command — flags, modes, the rules that go with each — are in [assets.md](references/assets.md), *Command reference*. The map:

| Command | Makes / does | Details |
|---|---|---|
| `generate image "<prompt>" [--aspect] [--colors] [--reference <img\|url>…]` | a still PNG; `--reference` keeps the same person or the same art style, cited as `image 0`, `image 1` | [assets.md](references/assets.md) |
| `generate video "<prompt>" [--first-frame] [--last-frame] [--ref-image/--ref-video/--ref-audio] [--duration] [--ratio] [--audio] [--last-frame-out]` | a video clip (3–9 min), five modes chosen by the inputs; frames and references are mutually exclusive; `--ref-audio` needs a `--ref-image`/`--ref-video` beside it | [video-generation.md](references/video-generation.md), [video-prompts.md](references/video-prompts.md) |
| `generate fastvideo <image\|url> [--prompt] [--duration] [--ratio]` | the OLD quick image-to-video path — only when fast mode is explicitly asked for | [assets.md](references/assets.md) |
| `generate avatar <image URL> --audio <url> [--aspect] [--motion]` | a talking presenter from one still + audio — for personalized pieces and fixed plates; a presenter is otherwise a filmed shot | [avatar.md](references/avatar.md) |
| `generate narration "<text>" --voice <id>` · `generate voices` · `generate music "<prompt>" [--duration]` | TTS MP3 · voice ids · an instrumental WAV (Stable Audio 3) | [music.md](references/music.md) |
| `sketch <plan.json> -o plan.mp4` | a grey-box 3D animatic to drive a camera plan via `--ref-video` | [video-generation-advanced.md](references/video-generation-advanced.md) |
| `path <file.svg> -o out.jet [--duration] [--stroke] [--color] [--head]` | a stroke reveal (draw-on) as an alpha `.jet`; fps must match the scene | [generative-fx.md](references/generative-fx.md) |
| `retime <clip> --ramp "0:1.0, 2.0:0.25, 3.2:1.0"` | a keyframed speed ramp for an impact beat — never to make a clip fit | [video-editing.md](references/video-editing.md) |
| `grade <clip> --match <ref> \| --lut <f.cube> \| --look <name>` | colour-match a companion clip to its reference, apply a brand LUT, or a restrained look | [video-editing.md](references/video-editing.md) |
| `chart bars --data d.json \| donut --value N \| line --series s.json --box x,y,w,h … [scene.json]` | animated chart layers as scene JSON — bars grow from the baseline, donuts sweep, lines draw on; value labels personalise by name, heights are baked (per-viewer heights → one scene per row) | [personalization.md](references/personalization.md) |
| `upload <file>` | a public URL for a generation INPUT that has no URL — public and permanent, temp handoff only | [assets.md](references/assets.md) |
| `compile` · `validate [--data rows.json]` · `preview [--at] [--grid]` · `snapshot --library <id> [--data]` · `render --library <id> [--data]` | scene → `.idm`; offline checks + the localisation guard; free wireframe; poster-only render; the render (a data object personalizes one render, an array renders one per row) | [format.md](references/format.md), [personalization.md](references/personalization.md) |
| `versions <scene>` · `revert <scene> --to N [-o]` · `inspect <file.idm> [--assets <dir>]` · `repack <doc.json>` | the automatic history; restore a version; unpack an `.idm` (prints its version stamp); re-encode an unpacked one (escape hatch — text/asset swaps yes, motion no) | [format.md](references/format.md) |
| `jet <frames\|video> [--key] [--method chroma]` · `matte <video> [--width]` · `track <video> [--comp WxH \| --point x,y]` · `beats <audio> [--fps --bands]` · `captions <file\|url>` · `glyphs <font> "<text>"` | alpha overlay; AI background removal (people/characters only); surface or point tracking; onsets + envelope (never hand-time to music); speech timing (segment-level); glyph coverage | [assets.md](references/assets.md), [generative-fx.md](references/generative-fx.md), [captions.md](references/captions.md) |
| `studio [scene.json] [--width --height]` · `add <block>` · `library list\|create` · `init` · `auth login\|status` · `schema` · `update` · `uninstall` | the local layout designer (only with the user's go-ahead); reusable blocks; libraries (create is get-or-create — ask the user first); setup | [blocks.md](references/blocks.md) |

Add `--json` for machine-readable output (errors on stderr; nothing reads a TTY non-interactively). Exit codes: 0 ok · 1 compile/schema · 2 missing file · 3 auth · 4 render timeout.

## The traps that cost renders

The full text of each, with the measurements, is in [format.md](references/format.md), *Technical must-knows*. The list, so none is skipped:

- **If anyone speaks in a clip, that clip's audio IS the voice** — never lay a TTS of the same line on top (*measured:* lines land 1.3 / 3.8 / 6.3 s into their clips, so no offset repairs it). A TTS file is an input to the generation *or* a layer in the scene, never both.
- **Editing an AI clip with audio: the first `-map` silently drops the track** — pair every `-map "[v]"` with `-map 0:a`, and `ffprobe` the output.
- **Hebrew/Arabic already render correctly** — `rtl` is a no-op; what breaks RTL is glyph coverage and box alignment. Never pre-reverse a string.
- **`position` is a DELTA from the box** (no anchor) — never repeat the box's x/y in it; with an anchor it is the absolute pivot point. `validate` warns on the tell-tale.
- **Overlay video needs alpha → `.jet`, never `.mp4`**; the plate and its matted overlay are the SAME clip; the `.jet` fps must match; any video input needs ffmpeg.
- **Personalised data visuals must be real image files, or a `strata chart` scene per row** — `render --data` swaps only text, media and audio by layer name; a bar's height is baked into its box, so per-viewer heights mean one scene per row (`strata chart bars --data row.json … scene_<id>.json`) — a hand-built native chart has nothing to swap.
- **`validate` before render** — the strict schema rejects invented keys and names the offender.
- **Layer names unique across the whole scene** — the exporter keys by name globally; the compiler auto-renames duplicates, which changes the personalization key.
- **Fonts must cover every glyph** (`strata glyphs`); comp max dimension 1920/axis; keyframe times are relative to the layer's `start`; sub-comps referenced by others come first.
- **Camera: a measured pinhole** — park it at `z = −focal` (−935 for 1080p at fov 60); negative z comes toward the camera; anchor is 2D, depth goes on `position` z.

## The craft, in one screen

The full manifesto — story, shots, composition, occlusion, tracking, 3D and camera, motion principles, kinetic type, transitions, atmosphere, rhythm and sound, polish, the design rules — is [craft.md](references/craft.md). Read it when planning any piece the user has not fully dictated. The spine:

- **Find the arc and the tension**; one idea per shot; align on the concept before building.
- **Think in shots** — establishing → push in, hard cuts, follow shots, reveals; name the shot size; change size *and* camera character at every cut.
- **Compose, do not fullscreen** — splits, grids, framed media, solids as design elements; use the whole frame; occlusion and tracking when the four conditions hold.
- **Defaults are not choices** — the anti-slop test per element; spend boldness once; remove one thing before rendering.
- **Use the 3D camera** — parallax, push-ins, orbits; move the camera, not each layer.
- **Something is always moving**; every image becomes a video; hold text for read time.
- **Easing is the craft** — `outExpo` / `outCubic` settles, `outBack` sparingly, `inOutSine` for drifts, `linear` only for loops.
- **Cut to the audio with `strata beats`**, never by ear; sound design is three layers — voice, bed, SFX.
- **Design**: hierarchy (the stamp test), ≤2 typefaces, brand colour, contrast matched to the plate, safe areas, no overlaps, personalization keyed by layer name.

## References — condition → file

Every file below ships with the skill; the condition is a rule, not a suggestion — when it is true, read the file before authoring, even if the answer seems known. The four marked **always** are the four of check A — read on every non-trivial piece the user did not fully dictate.

**Mandatory and routing**

| When… | Read |
|---|---|
| the request comes with material (storyboard, script, PDF, URL, sheets, footage, voice) — **first** | [intake.md](references/intake.md) — open everything, the effect map, the shot table, mode per scene, assembly to timecodes |
| **always** — before every storyboard and before every render | [anti-slop.md](references/anti-slop.md) — the named defaults, the per-element test, the composition-tells checklist |
| planning any piece the user did not fully dictate | [craft.md](references/craft.md) — the craft manifesto and the design rules |
| **always**, before authoring, unless boxes were dictated | [layouts.md](references/layouts.md) — the named frame layouts and the 12-column grid for 16:9 / 9:16 |
| **always**, when the piece uses any video | [video-layouts.md](references/video-layouts.md) — footage in shapes, type-as-window, split-screen, device frames, PiP, cut-outs, tracked graphics, match-cut |
| **always**, before deciding how anything moves | [motion-design.md](references/motion-design.md) — custom easing, overshoot+settle, stagger, anticipation, designed transitions, camera; the value tables; the web-animation tells checklist |
| planning the storyboard; a brief names a kind of video; cutting or critiquing footage; the note would be "make it faster" | [editing-director.md](references/editing-director.md) — the editorial contract, the decision order, the cut-decision gate, the blueprint fields that fold into the storyboard; it routes into `editorial/` (the six control dimensions and the pacing-diagnosis table are in editing-grammar.md) |
| ≥2 clips share a person, product or set; consistency is asked for | [production-bible.md](references/production-bible.md) — sheets, one identity block per principal, hero-element protection, one voice per speaker |
| `.brand/brand.md` exists (read it); brand material is supplied; "on brand", a series | [brand.md](references/brand.md) — capture and author from a brand document |
| the layout comes from Figma | [figma.md](references/figma.md) — scale/origin math, node→layer map, fonts→paths, export vectors, unique names |

**Authoring the scene**

| When… | Read |
|---|---|
| any syntax question; confirming a key exists; the engine traps in full | [format.md](references/format.md) — every layer key, 3D/camera, effects, masks, track mattes, sub-comps, rich text, animators, glyph check, versioning, the must-knows |
| before hand-writing any animation | [recipes.md](references/recipes.md) — engine-correct patterns: kinetic text, transitions, masks, FX, 3D/camera, data-viz, track & corner-pin, loops |
| before building a common component | [blocks.md](references/blocks.md) — lower-third, stat-card, end-card, logo-sting, device-frame, extruded-title… via `strata add` |
| before writing the storyboard for a known video type | [blueprints.md](references/blueprints.md) — whole-video structures: product launch, explainer, social promo, data story, logo reveal, showcase, overlay-footage |
| the piece has a recognisable kind ("like a TikTok", "cinematic", "sale ad"); the motion-value table | [video-styles.md](references/video-styles.md) — 50 built styles, technique only; a brand overrides every style |
| a brief names a film, director or campaign | [reference-styles.md](references/reference-styles.md) — ~25 references → measurable signature → nearest built style |
| a name for a technique is needed; more options; critiquing motion | [motion-atlas.md](references/motion-atlas.md) — the ~700-concept vocabulary index (numbers defer to motion-design.md) |
| any personal / data-driven video | [personalization.md](references/personalization.md) — placeholder naming, `--emit-timeline` for the contract, `render --data rows.json` for the batch, charts as animation |
| the user asks to tag a scene, make a template, add to a catalog — never otherwise | [tagging.md](references/tagging.md) — the manifest and its two closed vocabularies |

**Generating and editing media**

| When… | Read |
|---|---|
| before generating any asset; using a reference image; every-image-becomes-video; source resolution vs canvas; waves; the command reference | [assets.md](references/assets.md) |
| before the first `strata upload` of a job — a generation input has no URL | [upload.md](references/upload.md) — the two-condition test, temp-only, public-and-permanent, extension must match bytes |
| a layer sits OVER another layer (needs alpha); footage of a person to cut out; green screen; text behind a subject | [alpha.md](references/alpha.md) — `.jet`, `strata matte`, `strata jet --method chroma`, same-source occlusion, the quality setting |
| every time `generate video` runs (avatars are governed by avatar.md instead) | [video-generation.md](references/video-generation.md) — five modes, the frames-vs-references rule, `--realistic-human`, the 720p cap, prompt anatomy, camera lock, chaining, the never-stretch rule |
| before writing any generate-video prompt | [video-prompts.md](references/video-prompts.md) — a measured worked prompt per kind of video + the shot budget by duration |
| a consistent character, product or set across clips (`--ref-image`, the `[Image N]` JOB lines, the sheet form), an authored camera move, spoken dialogue, a music-cut, a series, a change to existing footage | [video-generation-advanced.md](references/video-generation-advanced.md) — `--ref-image` / `--ref-video` / `--ref-audio`, `strata sketch`, editing/extension (last resort) |
| a presenter, spokesperson or talking head | [avatar.md](references/avatar.md) — the filmed-vs-avatar decision, then the mechanics of whichever route |
| before generating music | [music.md](references/music.md) — the tag+genre+mood+BPM formula, the arc, ducking, SFX |
| the piece has music or rhythmic audio (beat-sync is mandatory — dialogue-only audio does not trigger this); overlays, visualizers, wipes, shaped windows, draw-on, displacement | [generative-fx.md](references/generative-fx.md) — `strata beats`, generator scripts → `.jet`, `strata path`, displacement |
| the scene needs to know when words are spoken | [captions.md](references/captions.md) — `strata captions` transcribes audio or video into `{t0,t1,text}` cues (segment-level, not per-word) |
| footage handed over to cut, join, crop, retime; before using any supplied clip as a layer; drift, ramps, grading | [video-editing.md](references/video-editing.md) — the `-map` rule, AAC drift, `retime`, `grade`, reframe |

**The motion atlas** (vocabulary — a lookup, never read by default; numbers defer to motion-design.md)

| When… | Read |
|---|---|
| any timing, easing or "why does this feel wrong" question | [01-foundations.md](references/motion/01-foundations.md) |
| multi-element scenes, parallax, camera moves | [02-choreography-space-camera.md](references/motion/02-choreography-space-camera.md) |
| any text, counter, caption, Hebrew/Arabic work | [03-typography.md](references/motion/03-typography.md) |
| scene changes, editing rhythm | [04-transitions.md](references/motion/04-transitions.md) |
| logo builds, icon animation, effects, backgrounds, look | [05-shape-effects-texture.md](references/motion/05-shape-effects-texture.md) |
| product UI, dashboards, infographics, brand stings; a personalized number as the hero (honesty rules for data motion) | [06-ui-data-brand.md](references/motion/06-ui-data-brand.md) |
| briefs, scripts, music-driven pieces, tone | [07-narrative-sound-emotion.md](references/motion/07-narrative-sound-emotion.md) |
| "make it feel like X", genre references | [08-styles-index.md](references/motion/08-styles-index.md) |
| before delivery, technical specs, reviews | [09-production-qa.md](references/motion/09-production-qa.md) |

**The editorial layer** — `references/editorial/` (routed by editing-director.md; direct links so no file is two hops away)

| When… | Read |
|---|---|
| cut logic, pacing, continuity, montage, "why does this feel flat"; filling the rhythm arc's *picture density* / *viewer state* columns | [editing-grammar.md](references/editorial/editing-grammar.md) — the six control dimensions, the pacing-diagnosis table |
| promos, ads, social, trailers, anything with a CTA | [short-form-performance.md](references/editorial/short-form-performance.md) |
| explainers, testimonials, internal comms, recaps, real people or real data | [nonfiction-formats.md](references/editorial/nonfiction-formats.md) |
| a genre modifier — thriller information release, trailer escalation, comedy timing | [narrative-genres.md](references/editorial/narrative-genres.md) |
| cutting or critiquing existing footage; captions, flashing, rights | [workflow-and-qa.md](references/editorial/workflow-and-qa.md) |
| an editorial rule seems to conflict with a measured fact (the measured fact wins) | [research-sources.md](references/editorial/research-sources.md) |
