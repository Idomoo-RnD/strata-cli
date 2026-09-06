# The scene as a program — `scene_v1.mjs` and the builder

The scene is authored in JavaScript and the CLI runs it: `strata validate scene_v1.mjs`,
`preview`, `compile`, `render`, `snapshot` all accept the `.mjs`, execute it inside the binary
(nothing to install — a strata binary is a complete Node runtime), write `scene_v1.json` beside it
and continue exactly as before. The JSON is a build product; the `.mjs` is the source and the file
you version (`scene_v1.mjs` → `scene_v2.mjs`). Never edit the JSON and keep working in the `.mjs`.

```
scene_v1.mjs  →  strata <command> scene_v1.mjs  →  scene_v1.json  →  the same pipeline
```

## Contents

- [Why a program instead of JSON](#why-a-program-instead-of-json)
- [The shape of a scene file](#the-shape-of-a-scene-file)
- [What the builder compiles away](#what-the-builder-compiles-away)
- [The rules that still apply](#the-rules-that-still-apply)
- [API — every method and the JSON it emits](#api--every-method-and-the-json-it-emits)

## Why a program instead of JSON

- **Relative time.** `layer.start`, `layer.end`, `after(title, 0.2)`, `beats[8]` — no seconds
  computed by hand and copied into forty places, no cut that drifts off its beat.
- **Repetition.** 41 notches are one loop; a lower third is a function called twice; a series
  shares one module of palette, type and blocks.
- **Traps that stop being writable** (next section).
- **Nothing lost.** Every key the compiler accepts has a helper or passes through `.set()` /
  `.raw()` verbatim — every property of every layer type in the VASCO schema is reachable.

A ten-layer logo sting is fine as JSON and gains nothing from a program. Reach for the `.mjs`
when the piece has repetition, arithmetic, beat timing, or the JSON is past a screen.

## The shape of a scene file

`strata js init` writes `strata.mjs` (the builder, from the binary) and a starter `scene_v1.mjs`
in the project folder. A complete scene:

```js
// scene_v1.mjs — run by: strata validate scene_v1.mjs
import { scene, kf, after, beatsFrom } from './strata.mjs';

const font = './assets/FranklinGothic-Medium.ttf';                     // paths are relative to the scene file's folder
const beats = beatsFrom('./assets/beats.json');                        // strata beats bed.wav -o assets/beats.json
const s = scene({ width: 1280, height: 720, fps: 24, duration: 25 });  // 24 fps when the piece is footage-led

s.video('plate', './assets/plate.mp4').fullFrame().for(10).drift(-60, 0);      // a living hold: 60 px over 10 s
s.audio('bed', './assets/bed.wav').volume(-10).ducking();

const title = s.text('title', 'NOTCH', { font, size: 150, color: '#EFEAE0' })
    .box(300, 270, 680, 190).align('center middle')
    .at(beats[8]).for(3)
    .reveal({ by: 'characters', delay: 0.04, over: 0.64, rise: 22 })          // hidden, then N,O,T,C,H in order
    .shadow({ color: '#000000', opacity: 0.5, angle: 135, distance: 10, size: 16 });

const band = s.solid('band', '#F2A900').box(120, 500, 1040, 68).at(after(title, 0.2)).for(3);
for (let i = 0; i < 41; i++)
    s.solid(`notch_${i}`, '#F2A900').box(130 + i * 25, 510, 14, 48).at(band.start + i * 0.06).until(band.end);

s.camera().fov(60).push(-623, -560, { over: 5 });                       // z toward the subject; motion_blur added on emit

export default s;                                                       // the CLI takes the default export
```

`export default` may be a scene, a plain scene object, or a function returning either. Inside the
file, `await s.check()` compiles and validates in-process and returns `{ errors, warnings }` —
useful in a loop over variants; the CLI runs the same checks on every `.mjs` it takes.

## What the builder compiles away

| the trap (traps.md, craft.md) | in the `.mjs` |
|---|---|
| duplicate layer names crash the exporter | names are made unique at creation; each rename is a `⚠` on the command (a renamed personalization key is wrong — fix the source) |
| an animated camera renders crisp (schema default `motion_blur: false`) | `motion_blur: true` written on emit; `.motionBlur(false)` opts out |
| a sub-comp without `duration` blanks part-way through a longer scene (the engine's own short default, [format.md](format.md)) | a sub-comp defaults to the scene's duration |
| a letter-by-letter reveal built as a travelling window shows the word first | `.reveal()` pins the range end at 1 and sweeps `start`; `.sweep()` is the explicit window for effects over resting text |
| a clip shorter than its slot freezes and `validate` cannot see it | with ffprobe on PATH the run warns, `offset_frame` included |
| cut times computed by hand from a beat grid | `beatsFrom()`, `after(layer, dt)`, `layer.start` / `layer.end`, `.until(next.start)` |

## The rules that still apply

The builder changes how the JSON is written, not what the engine does.

- **Keyframe times are relative to the layer's start**; `after()`, `.start`, `.end`, `.at()` are
  scene time. `.at(4).fadeIn(0.4)` fades from 4.0 to 4.4 s.
- **`position` is a delta from the box unless an `anchor` is set** — then it is the absolute
  pivot ([traps.md](traps.md)). The builder does not hide this.
- **Fonts are file paths; boxes are `[x, y, w, h]` in comp pixels; colours are hex; `size` is the
  font size** — the compact format's words ([format.md](format.md)), not VASCO's.
- **`offset_frame` counts comp frames** at the comp fps; `.trimIn(seconds)` converts; the clip
  must satisfy offset + slot ≤ clip length.
- **`.set()` / `.raw()` are verbatim**; the strict schema names a typo when the file is run.
- **The file is JavaScript the CLI executes** — the author's own code, the same trust as any script
  the run writes. It reads files; it should not need the network.

## API — every method and the JSON it emits

All layer methods return the layer, so calls chain. Times are seconds unless a method says frames.
`→` shows the compact JSON produced; the compiler turns that into VASCO as for any scene.

**Module**: `import { scene, Scene, Comp, Layer, kf, kff, after, withStart, beatsFrom, beatsOf, readJSON } from './strata.mjs'`

| export | what |
|---|---|
| `scene(opts)` | new scene — `{ width, height, fps, duration, name, shutter_angle, … }`; extra keys are written at the top level |
| `kf(t, v, ease?)` / `kff(f, v, ease?)` | keyframe at t seconds / f frames, **relative to the layer's start** |
| `after(layer, dt = 0)` / `withStart(layer, dt = 0)` | scene time at the layer's end / start, plus dt |
| `beatsFrom(path)` / `beatsOf(json)` | onsets array with `.bpm .duration .nearest(t) .every(n, from)` |
| `readJSON(path)` | a data file |
| `Scene.fromJSON(json)` | an existing compact scene loaded into builder objects, keys kept |

Eases: `linear`, `in|out|inOut` × `Quad Cubic Quart Quint Sine Expo Circ Back`, `outElastic`,
`outBounce`, `hold` — the ease on a keyframe shapes the segment **leaving** it.

**Scene / Comp**

| call | → JSON |
|---|---|
| `s.comp(name, { width, height, duration?, fps? }, c => { … })` | `comps[name]` — duration defaults to the scene's |
| `s.layer(type, name, props)` | `{ type, name, …props }` — `text solid image video audio camera comp` |
| `s.solid(name, color)` · `s.text(name, text, { font, size, color })` · `s.image(name, src)` · `s.video(name, src)` · `s.audio(name, src)` · `s.camera(name?)` · `s.place(name, comp)` | the layer, typed; no box on a solid = full frame |
| `s.find(name)` · `s.renamed` · `s.toJSON()` · `s.write(path)` · `await s.check({ sceneDir })` | lookup · renames made · the JSON · write it · compile + schema + clip guard |

**Layer — generic**: `.set(key, value)` · `.raw({…})` · `.get(key)` · `.name .type .fps` ·
`.start .end .duration` (scene seconds, from at/for/frames and the comp length)

**Layer — timing**: `.at(t)` → `start` · `.for(d)` → `duration` · `.until(t)` · `.frames(first, n?)`
→ `first_frame num_of_frames` (frame-exact) · no duration = to the comp end

**Layer — placement**: `.box(x, y, w, h)` · `.fullFrame()` · `.fit('fill'|'contain'|'stretch')` →
`alignment` · `.opacity(v)` · `.blend(mode)` · `.visible(b)` / `.hidden()` · `.is3d()` ·
`.motionBlur(b)` · `.position([x,y,z?])` `.scale(s)` `.rotation(r)` `.anchor([x,y])`

**Layer — animation**: `.animate(channel, [kf…])` (appended, sorted; `position scale rotation anchor
opacity color` or any raw channel) · `.tween(channel, from, to, { delay, over, ease })` ·
`.fadeIn(over, { delay, ease, to })` · `.fadeOut(over, { ease, from })` (ends at the layer's end) ·
`.move(from, to, { delay, over, ease })` — **`over` defaults to the layer's whole length**, like
`.drift()`; pass `over` for a short move · `.drift(dx, dy, { over, dz, ease, delay })` (from the
static position; 1 px/frame = fps × seconds px) · `.zoom(from, to, opts)` · `.rotate(from, to, opts)`
— `tween`, `zoom` and `rotate` default `over` to 0.5 s

**Layer — light on a surface**: `.sheen({ color, opacity = 0.35, blur = 40, width, direction =
'right'|'left'|'down'|'up', delay, over, ease, matte, name })` on a **text** layer → a screen-blended,
blurred solid travelling across the box, **track-matted to a hidden twin of the text** (or to the
`matte` layer you pass) — light on the letters, never a glow over the frame. Returns the sheen layer.
For a plate, matte a solid to the plate layer yourself (`.matte(plate)`); a glow left unmatted is a
grade change ([craft.md](craft.md), *Light stays on the surface it lights*).

**Layer — mask, matte, effects**: `.mask({ rect|ellipse|path…, feather, inverted, opacity,
expansion, animate })` or `.mask([shapes])` · `.matte(sourceLayer|name, type = 'alpha')` →
`matte: { type, source }` · `.blur(amount, { dimensions, repeat_edge, animate })` · `.shadow({…})`
`.glow({…})` `.stroke({…})` `.overlay({…})` (layer styles) · `.cornerPin(from, to, opts)` ·
`.rawEffect({ name: 'blur'|'corner_pin'|'styles', … })` · `.effect(obj)`

**Layer — text**: `.font(path)` `.text(str)` `.size(px)` `.minSize(px)` `.shrink(b)` `.color(hex)`
`.align('left|center|right [top|middle|bottom]')` `.tracking(n)` `.leading(n)` `.styles([{ start,
length, color, font, size, … }])` · `.animator({…})` (raw, `animate` blocks allowed) ·
`.reveal({ by = 'characters'|'words'|'lines', delay, over = 0.6, ease = 'outExpo', rise, shape =
'square', offsets })` → `{ opacity: 0, position: [0, rise, 0], ranges: [{ based_on, shape, end: 1,
animate: { start: [0 → 1] } }] }` — `delay` is when the sweep starts (layer time), `over` how long
the whole word takes; there is no per-character stagger value, the sweep is the stagger · works on a
hidden matte-source text too (revealing a fill letter by letter) · `.sweep({ by, delay, over, width,
ease, shape, offsets })` → both edges move, no opacity 0. Effects (`.blur()` `.shadow()` …) apply to
any visual layer, text included.

**Layer — media, audio, camera**: `.src(path)` · `.loop(b)` → `playback_mode` · `.offsetFrame(n)` /
`.trimIn(seconds)` · `.mediaAfter(prev, extraFrames = 0)` — continue this clip where `prev`'s media
ended (a stinger cut from the outgoing clip's next frames) · `.volume(db)` (not animatable)
`.ducking(b)` → sidechain · `.fov(deg)` ·
`.push(fromZ, toZ, { delay, over, ease, at })` — camera z toward the subject is more negative; true
size at z = −focal (−623 at 720p / fov 60), [format.md](format.md)

**`check()`** returns `{ errors, warnings, json, doc }`: compile errors and schema violations (a typo
in a raw key), renamed names, a clip shorter than its slot. In the CLI it runs in-process; a file run
by plain `node` spawns `strata validate`.

Patterns — reveal, living hold, beat-cut montage, lower third as a function, sub-comp reuse, matte,
camera push, personalization, a series — and the compact-JSON → builder translation table:
[scene-js-patterns.md](scene-js-patterns.md).
