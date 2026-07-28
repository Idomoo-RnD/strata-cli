# Generative FX — beat-sync, procedural overlays, audio-reactive graphics, vector windows

Four proven techniques that make a piece read as *produced*, not templated. The heavy
lifting is split: **`strata beats` (in the CLI) analyses the audio; I write small
generator scripts for the visuals** — frames → PNG sequence → `strata jet` → an alpha
overlay in the scene. The whole pattern is verified to work with just the installed
binary + Node.

---

## 1. Beat-sync — cut and animate ON the music (never guess)

Whenever the scene has music (or any rhythmic audio), I do **not** hand-time the motion:

```bash
strata beats music.mp3 -o beats.json                 # onsets + bpm
strata beats music.mp3 --fps 24 --bands 12 -o a.json # + per-frame envelope (see §3)
```

`beats.json` → `{ "duration": 12, "bpm": 123, "beats": [0.255, 0.499, 0.766, …] }`

Then I snap the design to it:
- **Scene cuts / shot changes** land on beats (usually every 4th or 8th onset — the bar, not
  every hit).
- **Entrances** (cards, words, logos) start at `beats[i]` with a fast pop:
  `scale [{t:B-0.02,v:0.6,ease:"outExpo"},{t:B+0.16,v:1.06},{t:B+0.3,v:1}]`.
- **Accents** (a pulse bar, a glow hit) on every onset.
- Onsets include off-beats (hats); for the main pulse use every other onset or filter by
  spacing ≈ `60/bpm`.

This is the single strongest "professionally edited" tell, and it costs one command.

---

## 2. Procedural overlays — I WRITE the generator (confetti, light leaks, wipes, snow…)

There is no fixed preset list: **I write a small Node script that draws RGBA frames, save
them as a PNG sequence, and encode with `strata jet`** (PNG frames keep their own alpha —
no keying). Anything drawable is an overlay: confetti, light leaks, sparks, snow, bokeh,
scanlines, animated gradients, luma wipes for transitions.

### The canonical template (verified end-to-end)
```js
// gen.mjs — run with: node gen.mjs   then: strata jet frames --fps 24 -o fx.jet
import { writeFileSync, mkdirSync } from 'node:fs';
import { deflateSync } from 'node:zlib';
const W = 1280, H = 720, FPS = 24, DUR = 5;          // MATCH THE SCENE'S fps
mkdirSync('frames', { recursive: true });
// --- minimal RGBA PNG writer (keep verbatim) ---
const crcT = [...Array(256)].map((_, n) => { let c = n; for (let k = 0; k < 8; k++) c = c & 1 ? 0xedb88320 ^ (c >>> 1) : c >>> 1; return c >>> 0; });
const crc = b => { let c = 0xffffffff; for (const x of b) c = crcT[(c ^ x) & 0xff] ^ (c >>> 8); return (c ^ 0xffffffff) >>> 0; };
const chunk = (t, d) => { const l = Buffer.alloc(4); l.writeUInt32BE(d.length); const td = Buffer.concat([Buffer.from(t), d]); const cc = Buffer.alloc(4); cc.writeUInt32BE(crc(td)); return Buffer.concat([l, td, cc]); };
function savePNG(path, rgba) {
  const ih = Buffer.alloc(13); ih.writeUInt32BE(W, 0); ih.writeUInt32BE(H, 4); ih[8] = 8; ih[9] = 6;
  const raw = Buffer.alloc((W * 4 + 1) * H);
  for (let y = 0; y < H; y++) { raw[y * (W * 4 + 1)] = 0; Buffer.from(rgba.subarray(y * W * 4, (y + 1) * W * 4)).copy(raw, y * (W * 4 + 1) + 1); }
  writeFileSync(path, Buffer.concat([Buffer.from([137,80,78,71,13,10,26,10]), chunk('IHDR', ih), chunk('IDAT', deflateSync(raw)), chunk('IEND', Buffer.alloc(0))]));
}
// --- frame loop: draw into img (RGBA, straight alpha; 0 alpha = transparent) ---
for (let f = 0; f < FPS * DUR; f++) {
  const img = new Uint8Array(W * H * 4), t = f / FPS;
  /* draw here — set img[(y*W+x)*4 + 0..3] = r,g,b,a */
  savePNG(`frames/f${String(f).padStart(4, '0')}.png`, img);
}
```

### Generator recipes (drop into the frame loop)
- **Confetti** — N particles `{x, y, fallSpeed, sway, phase, size, colour}`; per frame
  `y += speed·t`, `x += sin(phase + t·2)·sway`, wrap at H; draw small rects whose width
  oscillates with `|cos(phase + t·spin)|` (reads as tumbling). Use the brand palette.
- **Light leak** — 2–3 large soft blobs (`alpha = exp(-3·d²)·~0.4`, warm colours) drifting
  on slow sine paths. In the scene: `"blend":"add"`, opacity ~0.85.
- **Snow / bokeh** — confetti with round dots, slower fall, slight blur (draw at 2 sizes,
  lower alpha halo).
- **Luma wipe (transition matte)** — animate a threshold over a noise field: pixel is white
  where `noise(x,y) < t/DUR`, black elsewhere → use as a `luma` **track matte** on the
  incoming scene (see format.md Track mattes). Gives a designed dissolve, not a crossfade.
- Keep generation cheap: 5s @ 24fps/720p ≈ seconds of runtime; particles ≤ ~200.

Then: `strata jet frames --fps 24 -o fx.jet` and in the scene
`{ "type":"video", "name":"fx", "src":"./fx.jet", "box":[0,0,W,H] }` on top
(`"blend":"add"` for light effects). **The jet's fps MUST equal the scene's fps.**

---

## 3. Audio-reactive graphics — the envelope drives the pixels (or the keyframes)

`strata beats music.mp3 --fps <scene fps> --bands 12` adds:

```json
"envelope": { "fps": 24, "frames": 288, "rms": [0.02, 0.31, …],
              "bands": [[…], …12 arrays…], "band_freqs": [55, 86, …] }
```

Two ways to use it:
- **Drawn visualizer** — in a generator (§2), bar `b`'s height at frame `f` is
  `envelope.bands[b][f] · maxH`. The CLI already applies fast-attack/slow-decay smoothing,
  so bars look alive, not jittery. Encode → `.jet`, place as a strip in the layout.
- **Baked keyframes** — map `rms[f]` onto any channel as per-frame keyframes: a logo that
  breathes with the track (`scale: 1 + rms[f]·0.15`), a glow whose opacity follows the
  music. One keyframe per frame is fine — VASCO bakes per-frame arrays anyway.

---

## 4. Vector-path video windows — footage inside a shape (star, badge, blob, logo)

`mask.path` takes any polygon — so I **generate the polygon with code** and put footage
(or a colour panel) inside it. No SVG file needed:

```js
// star: alternate outer/inner radius around a centre
const pts = []; const cx = 640, cy = 340, R = 250, r = 105;
for (let i = 0; i < 10; i++) { const a = -Math.PI/2 + i*Math.PI/5, rad = i%2 ? r : R;
  pts.push([Math.round(cx + rad*Math.cos(a)), Math.round(cy + rad*Math.sin(a))]); }
// badge/gear: many points, radius alternating R/R·0.92 · blob: R·(1+0.2·sin(3a+seed))
```

```json
{ "type":"video", "name":"win", "src":"./clip.mp4", "box":[0,0,1280,720], "fit":"fill",
  "anchor":[640,340], "mask":{ "path": <pts> },
  "animate":{ "scale":[{"t":0.3,"v":0.001,"ease":"outExpo"},{"t":1.0,"v":1}] } }
```

- Pop the window in by animating **scale from ~0** with the anchor at the shape's centre
  (verified — mask + layer scale together).
- A slightly larger copy of the same path on a `solid` behind it makes a ring/border.
- For a real SVG logo, flatten its outline to points and use them as the path (curves →
  sampled points). Keep it to one closed outline per mask shape.

---

## Rules (all learned by building these)
- **fps discipline:** generated `.jet` fps = scene fps = audio envelope fps. One mismatch
  and the sync visibly drifts.
- **PNG frames carry their own alpha** — never pass `--key` for generated frames.
- Generated overlays follow the same layering rules as any `.jet` (video-layouts.md), and
  light effects sit in `"blend":"add"`.
- Big soft gradients make large jets (dedup can't compress them) — keep leak overlays
  short/looping, or reduce `--width`.
- Verify with a `snapshot` before the full render, like everything else.
