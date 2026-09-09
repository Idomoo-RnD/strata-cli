# Mask recipes

Windows, wipes, draw-on strokes and travelling shapes, built from mask geometry.

Part of the recipe library — the index, and every other part, is in [recipes.md](../recipes.md).

## 4. Masks

### Underline that writes itself
A solid masked by a stroked path, `trim` keyframed — no asset, and the colour stays live so a
brand swap or a `--data` pass still reaches it ([format.md](../format.md), *Strokes and draw-on*).
```json
{ "type": "text", "name": "word", "text": "live", "font": "./Inter.ttf", "size": 150,
  "color": "#E8ECF2", "box": [380,470,400,180], "align": "center middle" },
{ "type": "solid", "name": "underline", "color": "#F4B23F", "box": [0,0,1080,1080],
  "mask": { "path": "M420 640 L660 640", "stroke": 8, "cap": "round",
            "trim": { "end": [ {"t":2.6,"v":0,"ease":"out"}, {"t":3.1,"v":1} ] } } }
```

### Progress ring that fills to a value
The ring is one arc drawn to a fraction: the **track** is the full ring, the **value** is the same
path trimmed to it. ⚠ `trim` is baked at compile time, so a value that differs **per viewer** is not
reachable by `render --data` (which swaps text and media by layer name, nothing else) — that case is
still the image-swap rule ([personalization.md](../engine/personalization.md)) or one compiled scene per row.
Use this for a fixed value, a value known at author time, or a batch where each row gets its own
scene.
```json
{ "type": "solid", "name": "ring_track", "color": "#232C38", "box": [0,0,1080,1080],
  "mask": { "path": "M540 240 A300 300 0 1 1 539.9 240 Z", "stroke": 10 } },
{ "type": "solid", "name": "ring_value", "color": "#4FB6C4", "box": [0,0,1080,1080],
  "mask": { "path": "M540 240 A300 300 0 1 1 539.9 240 Z", "stroke": 10, "cap": "round",
            "trim": { "end": [ {"t":0.3,"v":0,"ease":"outCubic"}, {"t":1.9,"v":0.72} ] } } },
{ "type": "text", "name": "pct", "text": "72%", "font": "./Inter.ttf", "size": 120,
  "color": "#E8ECF2", "box": [340,470,400,160], "align": "center middle" }
```
A closed path drawn all the way round is welded as a ring (no seam); `0.72` stops it at 72 %.

### Radial tick scale, one object
```json
{ "type": "solid", "name": "tick", "color": "#39424F", "box": [538,168,4,18], "anchor": [540,540],
  "repeat": { "count": 24, "step": { "rotation": 15 }, "stagger": 0.03 },
  "animate": { "scale": [ {"t":0.15,"v":[0,0],"ease":"out"}, {"t":0.45,"v":[1,1]} ] } }
```
24 layers named `tick_01…tick_24`, each 15° further round and 30 ms later. Only build the scale if
something is measured against it ([anti-slop.md](../craft/anti-slop.md)).

### Logo draw-on from the logo's own outline
Paste the `d` of the logo's path (arcs included) and trim it; strokes draw in path order.
```json
{ "type": "solid", "name": "logo_draw", "color": "#ffffff", "box": [0,0,1920,1080],
  "mask": { "path": "M760 540 C760 420 860 340 960 340 C1060 340 1160 420 1160 540",
            "stroke": 12, "cap": "round",
            "trim": { "end": [ {"t":0,"v":0,"ease":"inOutSine"}, {"t":1.4,"v":1} ] } } }
```

### Feathered iris reveal
```json
{ "type": "image", "src": "./image.jpg", "box": [0,0,1280,720], "fit": "fill",
  "mask": { "shapes": [{ "ellipse": [640,360,40,40], "feather": [120,120],
    "animate": { "shape": [{"t":0.3,"v":{"ellipse":[640,360,40,40]},"ease":"outCubic"},{"t":2.2,"v":{"ellipse":[640,360,760,520]}}] } }] } }
```

### Shape morph (blob ↔ star)
Both keyframes must have the **same point count** to interpolate.
```json
{ "type": "image", "src": "./image.jpg", "box": [0,0,1280,720], "fit": "fill",
  "mask": { "shapes": [{ "path": [[640,60],[852,248],[640,360],[852,472],[640,660],[428,472],[640,360],[428,248]], "closed": true,
    "animate": { "shape": [{"t":0,"v":{"path":[[640,60],[852,248],[640,360],[852,472],[640,660],[428,472],[640,360],[428,248]]},"ease":"inOutSine"},
                           {"t":1.3,"v":{"path":[[640,20],[746,254],[746,360],[746,466],[640,510],[534,466],[534,360],[534,254]]}}] } }] } }
```

### Moving spotlight
```json
{ "type": "image", "src": "./image.jpg", "box": [0,0,1280,720], "fit": "fill",
  "mask": { "shapes": [{ "ellipse": [360,300,170,170], "feather": [90,90],
    "animate": { "shape": [{"t":0,"v":{"ellipse":[360,300,170,170]},"ease":"inOutSine"},{"t":2,"v":{"ellipse":[920,430,170,170]}},{"t":4,"v":{"ellipse":[360,300,170,170]}}] } }] } }
```

### Photo through text (alpha matte)
```json
{ "type": "text", "name": "mt", "text": "IDOMOO", "font": "./font.ttf", "size": 230, "color": "#ffffff",
  "box": [0,250,1280,240], "align": "center middle", "visible": false }
```
```json
{ "type": "image", "src": "./image.jpg", "box": [0,0,1280,720], "fit": "fill", "matte": { "type": "alpha", "source": "mt" } }
```

### Knockout text (hole in a panel)
```json
{ "type": "text", "name": "mt", "text": "OPEN", "font": "./font.ttf", "size": 260, "color": "#ffffff",
  "box": [0,240,1280,260], "align": "center middle", "visible": false }
```
```json
{ "type": "solid", "color": "#0b0e1a", "box": [0,0,1280,720], "opacity": 0.92, "matte": { "type": "alpha_inverted", "source": "mt" } }
```

### Barn-door reveal
```json
{ "type": "solid", "name": "L", "color": "#0b0e1a", "box": [0,0,640,720],
  "animate": { "position": [{"t":0.3,"v":[0,0],"ease":"inOutCubic"},{"t":1.6,"v":[-660,0]}] } }
```
```json
{ "type": "solid", "name": "R", "color": "#0b0e1a", "box": [640,0,640,720],
  "animate": { "position": [{"t":0.3,"v":[0,0],"ease":"inOutCubic"},{"t":1.6,"v":[660,0]}] } }
```

### Soft gradient wipe
A wide-feather rect mask sweeping across.
```json
{ "type": "image", "src": "./image.jpg", "box": [0,0,1280,720], "fit": "fill",
  "mask": { "shapes": [{ "rect": [-300,0,300,720], "feather": [220,0],
    "animate": { "shape": [{"t":0.3,"v":{"rect":[-300,0,300,720]},"ease":"inOutSine"},{"t":2.4,"v":{"rect":[1280,0,300,720]}}] } }] } }
```

---
