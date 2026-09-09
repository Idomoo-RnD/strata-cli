# Transition and motion recipes

Getting from one shot to the next, and the plain moves — pushes, drifts, settles — underneath everything else.

Part of the recipe library — the index, and every other part, is in [recipes.md](../recipes.md).

These are **layer fragments**, not standalone scenes: provide a wrapper, durations, distinct
names and assets. [Runnable fixtures](../examples/runnable-scenes.md) have offline tests and
explicit proof levels. Select transitions for continuity/meaning, not because a dissolve is banned.

## 2. Transitions

### Scale-through (A blasts past, B scales in)
```json
{ "type": "solid", "name": "A", "color": "#0e7490", "box": [0,0,1280,720], "anchor": [640,360],
  "animate": { "scale": [{"t":0,"v":1},{"t":1.6,"v":1,"ease":"inExpo"},{"t":2.2,"v":6}], "opacity": [{"t":1.6,"v":1},{"t":2.2,"v":0}] } }
```
```json
{ "type": "solid", "name": "B", "color": "#a21caf", "box": [0,0,1280,720], "start": 1.7, "anchor": [640,360],
  "animate": { "scale": [{"t":0,"v":0.2,"ease":"outExpo"},{"t":0.6,"v":1}], "opacity": [{"t":0,"v":0},{"t":0.3,"v":1}] } }
```

### Whip-pan (motion-blur swish)
Set the comp `"shutter_angle": 2` for heavy blur.
```json
{ "type": "solid", "name": "A", "color": "#1d4ed8", "box": [0,0,1280,720], "motion_blur": true,
  "animate": { "position": [{"t":0,"v":[0,0],"ease":"hold"},{"t":1.7,"v":[0,0],"ease":"inCubic"},{"t":2.1,"v":[-1500,0]}] } }
```
```json
{ "type": "solid", "name": "B", "color": "#be123c", "box": [0,0,1280,720], "motion_blur": true,
  "animate": { "position": [{"t":0,"v":[1600,0],"ease":"hold"},{"t":1.7,"v":[1600,0],"ease":"outCubic"},{"t":2.1,"v":[0,0]}] } }
```

### Light-leak bloom over the cut
```json
{ "type": "image", "src": "./leak.png", "box": [0,0,1280,720], "fit": "fill", "blend": "add", "start": 1.4, "anchor": [640,360],
  "animate": { "opacity": [{"t":0,"v":0},{"t":0.5,"v":1},{"t":1.1,"v":0}], "scale": [{"t":0,"v":1.1,"ease":"inOutSine"},{"t":1.1,"v":1.5}] } }
```

### Clock wipe (radial wedge)
A wedge polygon mask whose sweep grows 0°→360° (compute the rim points; both keyframes need the same point count).
```json
{ "type": "image", "src": "./image.jpg", "box": [0,0,1280,720], "fit": "fill",
  "mask": { "shapes": [{ "path": [[640,360],[640,-400]], "closed": true,
    "animate": { "shape": [{"t":0.4,"v":{"path":[[640,360],[640,-400]]},"ease":"inOutSine"},
                           {"t":2.6,"v":{"path":[[640,360],[640,-400],[1400,360],[640,1120],[-120,360],[640,-400]]}}] } }] } }
```

### Iris (circle) reveal
```json
{ "type": "image", "src": "./image.jpg", "box": [0,0,1280,720], "fit": "fill",
  "mask": { "shapes": [{ "ellipse": [640,360,30,30], "feather": [40,40],
    "animate": { "shape": [{"t":0.4,"v":{"ellipse":[640,360,30,30]},"ease":"inOutCubic"},{"t":2.2,"v":{"ellipse":[640,360,820,820]}}] } }] } }
```

### 3D flip transition
```json
{ "type": "solid", "name": "A", "color": "#b45309", "box": [240,130,800,460], "anchor": [640,360], "motion_blur": true,
  "animate": { "rotation": [{"t":0,"v":[0,0,0],"ease":"hold"},{"t":1.6,"v":[0,0,0],"ease":"inSine"},{"t":2.2,"v":[0,-90,0]}], "opacity": [{"t":2.1,"v":1},{"t":2.2,"v":0}] } }
```
```json
{ "type": "solid", "name": "B", "color": "#6d28d9", "box": [240,130,800,460], "anchor": [640,360], "start": 2.1, "motion_blur": true,
  "animate": { "rotation": [{"t":0,"v":[0,90,0],"ease":"outSine"},{"t":0.6,"v":[0,0,0]}] } }
```

### Speed-line streak
```json
{ "type": "image", "src": "./rays.png", "box": [0,0,1280,720], "fit": "fill", "blend": "add", "start": 1.5, "anchor": [640,360], "motion_blur": true,
  "animate": { "opacity": [{"t":0,"v":0},{"t":0.3,"v":0.9},{"t":1.0,"v":0}], "scale": [{"t":0,"v":0.6,"ease":"outExpo"},{"t":1.0,"v":3}] } }
```

### Luma dissolve (textured)
`noise.png` is grayscale; the outgoing layer is matted by it and fades — bright areas linger.
```json
{ "type": "image", "name": "mt", "src": "./noise.png", "box": [0,0,1280,720], "fit": "fill", "visible": false }
```
```json
{ "type": "solid", "name": "A", "color": "#111827", "box": [0,0,1280,720], "matte": { "type": "luma", "source": "mt" },
  "animate": { "opacity": [{"t":0.4,"v":1,"ease":"inOutSine"},{"t":2.6,"v":0}] } }
```

---

## 3. Motion

### Anticipation + overshoot
```json
{ "type": "solid", "color": "#a855f7", "box": [490,210,300,300], "anchor": [640,360],
  "effects": [{ "type": "shadow", "color": "#00000099", "distance": 16, "size": 8 }],
  "animate": { "scale": [{"t":0,"v":0,"ease":"outBack"},{"t":0.5,"v":1},{"t":1.0,"v":1,"ease":"inOutSine"},{"t":1.2,"v":0.88},{"t":1.55,"v":1.12,"ease":"outBack"},{"t":1.9,"v":1}],
               "rotation": [{"t":1.0,"v":0},{"t":1.2,"v":-6},{"t":1.9,"v":0,"ease":"outBack"}] } }
```

### Float / bob idle loop
```json
{ "type": "text", "text": "idomoo", "font": "./font.ttf", "size": 130, "box": [0,270,1280,180],
  "align": "center middle", "anchor": [640,360],
  "animate": { "position": [{"t":0,"v":[640,348],"ease":"inOutSine"},{"t":1,"v":[640,372]},{"t":2,"v":[640,348]},{"t":3,"v":[640,372]},{"t":4,"v":[640,348]}],
               "rotation": [{"t":0,"v":[0,0,-2],"ease":"inOutSine"},{"t":2,"v":[0,0,2]},{"t":4,"v":[0,0,-2]}] } }
```

### 3D camera push-in
The camera starts at **`−focal`** (623 for a 720-high comp at fov 60 — format.md, Camera) so
the layer is true-size on frame 1, then dollies in. An anchored 3D layer's `position` is
absolute, written `[anchorX, anchorY, z]`; the unanchored alternative is no anchor and
`position:[0,0,0]`.
```json
{ "type": "camera", "fov": 60, "position": [640,360,-623],
  "animate": { "position": [{"t":0,"v":[640,360,-623],"ease":"inOutSine"},{"t":4,"v":[640,360,-300]}] } }
```
```json
{ "type": "text", "text": "idomoo", "font": "./font.ttf", "size": 120, "box": [0,250,1280,220],
  "align": "center middle", "is_3d": true, "anchor": [640,360], "position": [640,360,0] }
```

### Fly-through warp (scale-from-depth)
```json
{ "type": "text", "text": "FUTURE", "font": "./font.ttf", "size": 150, "box": [0,250,1280,220],
  "align": "center middle", "anchor": [640,360], "motion_blur": true,
  "effects": [{ "type": "glow", "color": "#a855f7", "size": 20, "opacity": 0.9 },
    { "type": "blur", "amount": 0, "animate": { "amount": [{"t":0,"v":28,"ease":"outQuad"},{"t":0.5,"v":0},{"t":2.6,"v":0,"ease":"inQuad"},{"t":3.2,"v":36}] } }],
  "animate": { "scale": [{"t":0,"v":0.05,"ease":"outExpo"},{"t":0.6,"v":1},{"t":2.6,"v":1,"ease":"inExpo"},{"t":3.2,"v":6}],
               "opacity": [{"t":0,"v":0},{"t":0.25,"v":1},{"t":2.7,"v":1},{"t":3.2,"v":0}],
               "rotation": [{"t":0,"v":[0,-40,0],"ease":"outCubic"},{"t":0.6,"v":[0,0,0]}] } }
```

### Bounce drop + squash
Anchor at the ball's **bottom** so the squash flattens onto the floor at contact.
```json
{ "type": "solid", "color": "#22d3ee", "box": [580,0,120,120], "anchor": [640,120],
  "mask": { "ellipse": [640,60,60,60] },
  "animate": { "position": [{"t":0,"v":[640,120],"ease":"inQuad"},{"t":0.6,"v":[640,556]},{"t":1.0,"v":[640,320],"ease":"outQuad"},{"t":1.45,"v":[640,556],"ease":"inQuad"},{"t":1.7,"v":[640,470],"ease":"outQuad"},{"t":1.95,"v":[640,556],"ease":"inQuad"}],
               "scale": [{"t":0.55,"v":[1,1]},{"t":0.62,"v":[1.3,0.65]},{"t":0.72,"v":[0.85,1.18],"ease":"outBack"},{"t":0.9,"v":[1,1]}] } }
```

---
