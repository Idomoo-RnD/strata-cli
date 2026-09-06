# Scene-file patterns, and compact JSON → builder

The recurring builds as code to paste into `scene_v1.mjs`, then the key-by-key table for rewriting
a JSON scene, a recipe from [recipes.md](recipes.md) or a block from [blocks.md](blocks.md) as
builder calls. The craft behind each pattern is in [craft.md](craft.md); the API is in
[scene-js.md](scene-js.md).

## Contents

- [Patterns](#patterns)
- [Compact scene JSON → builder](#compact-scene-json--builder)
- [Time, translated](#time-translated)

## Patterns

**A letter-by-letter reveal**
```js
s.text('title', 'NOTCH', { font, size: 150, color: '#EFEAE0' })
    .box(300, 270, 680, 190).align('center middle')
    .at(beats[8]).for(3)
    .reveal({ by: 'characters', delay: 0.04, over: 0.64, rise: 22 });
```
`reveal` pins the range end at 1 and sweeps `start`: hidden until each letter arrives. Word by word:
`by: 'words'`. A tagline after it: `.reveal({ by: 'words', delay: 1.4, over: 0.6 })`.

**A sheen through a wordmark (light on the letters, not over the frame)**
```js
const mark = s.text('mark', 'NINE BAR', { font, size: 118, color: '#EFEAE0' }).box(60, 560, 600, 130).align('center middle').at(14.5).until(20)
    .reveal({ by: 'characters', over: 0.6 });
mark.sheen({ direction: 'right', over: 2.2, delay: 1.0, opacity: 0.4 });    // a hidden twin is the matte; the glow cannot leave the glyphs
```
A pool of light over a plate is matted to the plate the same way: `s.solid('pool', '#D9A441').box(…).blend('screen').blur(70).opacity(0.3).matte(plate).move(…)`.

**A living hold**
```js
// footage moves on its own — it only has to outlast the slot (the run warns if not)
s.video('plate', './assets/plate.mp4').fullFrame().at(0).for(6).trimIn(1.5);
// a still or a graphic: ≈1 px/frame across the whole hold — px = fps × seconds
const hero = s.image('hero', './assets/hero.png').box(240, 120, 800, 480).fit('contain').at(6).for(4).drift(96, 0);
// or a slow camera push over the shot
s.camera().fov(60).push(-623, -560, { delay: 6, over: 4 });
```
A full-frame low-opacity plate drifting carries more measured motion than a push on the subject.

**A beat-cut montage**
```js
const beats = beatsFrom('./assets/beats.json');           // strata beats bed.wav -o assets/beats.json
const cuts = beats.every(2, 4).slice(0, 8);               // every 2nd onset from the 4th: eight shots
const clips = ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h'].map(n => `./assets/clip_${n}.mp4`);
cuts.forEach((t, i) => s.video(`shot_${i}`, clips[i]).fullFrame().at(t).until(cuts[i + 1] ?? t + 2));
s.audio('whoosh_3', './assets/whoosh.wav').at(cuts[3] - 0.15).volume(-6);   // the peak lands on the cut
```

**A lower third as a function**
```js
function lowerThird(s, id, name, role, at, { font, dur = 4 } = {}) {
    s.solid(`${id}_band`, '#14110E').box(80, 560, 520, 96).at(at).for(dur)
        .mask({ rect: [80, 560, 0, 96], animate: { shape: [kf(0, { rect: [80, 560, 0, 96] }, 'outQuint'), kf(0.5, { rect: [80, 560, 520, 96] })] } });
    s.text(`${id}_name`, name, { font, size: 40, color: '#EFEAE0' }).box(104, 572, 480, 44).align('left middle').at(at + 0.25).for(dur - 0.25).reveal({ by: 'words', over: 0.4 });
    s.text(`${id}_role`, role, { font, size: 24, color: '#9A9086' }).box(104, 614, 480, 30).align('left middle').at(at + 0.45).for(dur - 0.45).fadeIn(0.3);
}
lowerThird(s, 'lt1', 'Dana Levi', 'Head of Product', 3.0, { font });
lowerThird(s, 'lt2', 'Omer Katz', 'Design', 12.5, { font });
```
Names carry the `id` prefix so nothing collides.

**A sub-comp placed three times**
```js
const card = s.comp('card', { width: 600, height: 400 }, c => {          // duration = the scene's
    c.solid('card_bg', '#00ADB5').mask({ rect: [20, 20, 560, 360], feather: 2 });
    c.text('card_label', 'SUB-COMP', { font, size: 56, color: '#0A0A0A' }).box(0, 150, 600, 100).align('center middle').fadeIn(0.7, { delay: 0.3 });
});
[0, 1, 2].forEach(i => s.place(`card_${i}`, card).box(40 + i * 400, 160, 360, 240).at(1 + i * 0.3).for(4));
```
A comp's text is the same in every instance; per-instance text is separate layers or placeholders.

**A cut-out over the plate (matte)**
```js
const shape = s.text('cut_matte', 'MATTE', { font, size: 160 }).box(0, 480, 1280, 200).align('center middle').hidden();
s.video('cut_footage', './assets/plate.mp4').box(0, 480, 1280, 200).fit('fill').matte(shape);
```
A person or product cut out of footage is a `.jet` from `strata matte` / `strata jet`
([assets.md](assets.md)); the builder only places the file: `s.video('hero_cut', './assets/hero.jet')`.

**A camera push with correct depth**
```js
s.camera('cam').fov(60).position([640, 360, -623]).push(-623, -560, { over: 5 });        // 720p, fov 60: true size at z = −623
s.text('deep', 'BEHIND', { font, size: 120 }).box(0, 300, 1280, 140).align('center middle').is3d().anchor([640, 370]).position([640, 370, 200]);
```
Negative z is toward the camera; depth goes on `position` z with an anchor set
([format.md](format.md), the pinhole).

**Personalization placeholders**
```js
s.text('viewer_name', 'Sample Name', { font, size: 72, color: '#EFEAE0' }).box(0, 200, 1280, 90).align('center middle').set('placeholder', true);
s.image('hero_photo', './assets/sample_hero.png').box(240, 300, 800, 380).fit('fill').set('placeholder', true)
    .anchor([640, 490]).position([640, 490]).zoom(1, 1.06, { over: 4 });      // a swapped still still moves
```
The layer **name** is the personalization key (`render --data`), which is why a renamed duplicate is
reported rather than accepted.

**A series**: keep the shared palette, type and blocks in one module and import it —
`import { palette, lowerThird } from './series/kit.mjs'` — after `strata recipe use <name>` brought the
previous piece's brief, storyboard and brand document in.

**Translating an existing JSON scene**
```js
import { Scene } from './strata.mjs';
const s = Scene.fromJSON(readJSON('old_scene.json'));   // every layer is a Layer with its keys; refactor into helpers, then export default s
```

## Compact scene JSON → builder

Anything not listed is `.set(key, value)` — verbatim passthrough.

| JSON | builder |
|---|---|
| `{ "width", "height", "fps", "duration", "name" }` | `scene({ width, height, fps, duration, name })` |
| any other top-level key (`shutter_angle`…) | `scene({ …, shutter_angle: 180 })` |
| `"comps": { "card": { "width", "height", "duration", "layers" } }` | `s.comp('card', { width, height, duration }, c => { … })` |
| `"layers": [ … ]` | one call per layer, in order (order is z-order) |
| `"type": "solid", "color": "#222831"` | `s.solid(name, '#222831')` |
| `"type": "text", "text", "font", "size", "color"` | `s.text(name, text, { font, size, color })` |
| `"type": "image" / "video", "src"` | `s.image(name, src)` / `s.video(name, src)` |
| `"type": "audio", "src"` | `s.audio(name, src)` |
| `"type": "camera"` | `s.camera(name)` |
| `"type": "comp", "comp": "card"` | `s.place(name, card)` |
| `"start": 1.5` · `"duration": 3` | `.at(1.5)` · `.for(3)` or `.until(4.5)` |
| `"first_frame", "num_of_frames"` | `.frames(first, n)` |
| `"box": [x, y, w, h]` | `.box(x, y, w, h)`; full frame `.fullFrame()` |
| `"fit"` · `"opacity"` · `"blend"` · `"visible": false` · `"is_3d"` · `"motion_blur"` | `.fit()` · `.opacity()` · `.blend()` · `.hidden()` · `.is3d()` · `.motionBlur()` |
| `"position"`, `"scale"`, `"rotation"`, `"anchor"` | `.position([…]) .scale(…) .rotation(…) .anchor([…])` |
| `"animate": { "opacity": [{ "t", "v", "ease" }…] }` | `.animate('opacity', [kf(t, v, ease)…])` or `.fadeIn(over)` |
| `"animate": { "position" / "scale" / "rotation" }` | `.move() .drift()` / `.zoom()` / `.rotate()` or `.animate(channel, …)` |
| `"mask": { … }` · `"mask": { "shapes": [ … ] }` | `.mask({ … })` · `.mask([ … ])` |
| `"matte": { "type", "source" }` | `.matte(sourceLayer, type)` |
| `"effects": [{ "type": "blur" \| "shadow" \| "glow" \| "stroke" \| "overlay" \| "corner_pin" }]` | `.blur() .shadow() .glow() .stroke() .overlay() .cornerPin()` |
| `"effects": [{ "name": … }]` (raw VASCO) | `.rawEffect({ name, … })` |
| `"align"` · `"tracking"` · `"leading"` · `"min_size"` · `"shrink"` · `"styles"` | `.align() .tracking() .leading() .minSize() .shrink() .styles()` |
| `"animators": [{ "opacity": 0, "ranges": [{ …, "end": 1, "animate": { "start" } }] }]` | `.reveal({ by, delay, over, ease, rise })` |
| `"animators"` with both `start` and `end` animated | `.sweep({ … })` — effects over resting text only |
| any other `"animators"` entry | `.animator({ … })` |
| `"loop"` · `"offset_frame"` · `"volume"` · `"ducking"` · `"fov"` | `.loop()` · `.offsetFrame()` / `.trimIn(s)` · `.volume()` · `.ducking()` · `.fov()` |
| `"placeholder": true`, `"duration_referrer"`, any raw VASCO key | `.set('placeholder', true)` |

## Time, translated

| in JSON | in the builder |
|---|---|
| `"start": 4.45` computed from title 1.5 + 2.75 + 0.2 | `.at(after(title, 0.2))` |
| `"start": 8.417` read off the beat grid | `.at(beats[34])` or `.at(beats.nearest(8.4))` |
| a layer ending where the next begins | `.until(next.start)` |
| a clip layer sized to its slot | `.for(slot)` — the run confirms the file is long enough |
| 41 layers differing by an index | `for (let i = 0; i < 41; i++) s.solid(`n_${i}`, …).at(t0 + i * 0.06)` |
