# Effects and extras

Light, grain, glitch, glow and the smaller patterns that do not belong to any one family.

Part of the recipe library — the index, and every other part, is in [recipes.md](../recipes.md).

## 5. Special FX

### Neon glow pulse + flicker
```json
{ "type": "text", "text": "NEON", "font": "./font.ttf", "size": 160, "color": "#f0abfc", "box": [0,250,1280,220], "align": "center middle",
  "effects": [{ "type": "glow", "color": "#d946ef", "size": 20, "opacity": 0.9,
    "animate": { "size": [{"t":0,"v":10,"ease":"inOutSine"},{"t":1,"v":34},{"t":2,"v":10},{"t":3,"v":34},{"t":4,"v":10}],
                 "opacity": [{"t":0,"v":0.6,"ease":"inOutSine"},{"t":1,"v":1},{"t":2,"v":0.6}] } }],
  "animate": { "opacity": [{"t":0,"v":1,"ease":"hold"},{"t":1.5,"v":1},{"t":1.55,"v":0.4},{"t":1.62,"v":1}] } }
```

### Depth-of-field focus pull
Background pre-blurred; the subject pulls into focus.
```json
{ "type": "image", "src": "./image.jpg", "box": [0,0,1280,720], "fit": "fill", "effects": [{ "type": "blur", "amount": 22 }] }
```
```json
{ "type": "text", "text": "in focus", "font": "./font.ttf", "size": 110, "box": [0,300,1280,150], "align": "center middle",
  "effects": [{ "type": "blur", "amount": 0, "animate": { "amount": [{"t":0,"v":18,"ease":"outQuad"},{"t":0.9,"v":0}] } }],
  "animate": { "opacity": [{"t":0,"v":0},{"t":0.5,"v":1}] } }
```

### Chromatic aberration (RGB split breathing)
Three additive copies (`#ff003c` / `#00ff8c` / `#3b7bff`), the outer two drifting ±x.
```json
{ "type": "text", "text": "CHROMA", "font": "./font.ttf", "size": 140, "color": "#ff003c", "box": [0,250,1280,200],
  "align": "center middle", "blend": "add", "anchor": [640,350],
  "animate": { "position": [{"t":0,"v":[640,350],"ease":"inOutSine"},{"t":1,"v":[628,350]},{"t":2,"v":[640,350]}] } }
```

### Ambient light leaks
```json
{ "type": "image", "src": "./leak.png", "box": [0,0,1280,720], "fit": "fill", "blend": "add", "anchor": [640,360],
  "animate": { "opacity": [{"t":0,"v":0.4,"ease":"inOutSine"},{"t":2,"v":0.9},{"t":4,"v":0.4}],
               "position": [{"t":0,"v":[600,360],"ease":"inOutSine"},{"t":4,"v":[720,360]}],
               "scale": [{"t":0,"v":1,"ease":"inOutSine"},{"t":4,"v":1.2}] } }
```

### Lens flare sweep
```json
{ "type": "image", "src": "./flare.png", "box": [0,0,1280,720], "fit": "fill", "blend": "add", "anchor": [640,360],
  "animate": { "position": [{"t":0,"v":[180,360],"ease":"inOutSine"},{"t":4,"v":[1100,360]}],
               "opacity": [{"t":0,"v":0},{"t":1,"v":1},{"t":3,"v":1},{"t":4,"v":0}] } }
```

### Corner-pin perspective turn
```json
{ "type": "image", "src": "./image.jpg", "box": [0,0,1280,720], "fit": "fill",
  "effects": [{ "type": "corner_pin",
    "from": [[0,0],[1280,0],[0,720],[1280,720]],
    "to":   [[120,90],[1160,60],[180,660],[1100,690]],
    "animate": { "to.upper_left": [{"t":0,"v":[120,90],"ease":"inOutSine"},{"t":4,"v":[320,150]}],
                 "to.lower_left": [{"t":0,"v":[180,660],"ease":"inOutSine"},{"t":4,"v":[360,600]}] } }] }
```

### Mirror reflection
A flipped (`scale:[1,-1]`), faded, blurred copy with a feathered mask fading it out.
```json
{ "type": "text", "text": "REFLECT", "font": "./font.ttf", "size": 120, "color": "#22d3ee", "box": [0,230,1280,150],
  "align": "center middle", "anchor": [640,380], "scale": [1,-1], "opacity": 0.28,
  "effects": [{ "type": "blur", "amount": 3 }],
  "mask": { "rect": [0,384,1280,150], "feather": [0,110] } }
```

### Frosted-glass panel
A blurred copy of the bg, masked to the panel, under a translucent white solid.
```json
{ "type": "image", "src": "./image.jpg", "box": [0,0,1280,720], "fit": "fill",
  "effects": [{ "type": "blur", "amount": 18 }], "mask": { "rect": [290,230,700,260], "feather": [2,2] } }
```
```json
{ "type": "solid", "color": "#ffffff", "box": [290,230,700,260], "opacity": 0.18 }
```

### Colour grade (teal–orange duotone)
```json
{ "type": "solid", "color": "#0a2a3a", "box": [0,0,1280,720], "blend": "screen",
  "animate": { "opacity": [{"t":0.5,"v":0,"ease":"inOutSine"},{"t":2,"v":0.5}] } }
```
```json
{ "type": "solid", "color": "#ff8a3d", "box": [0,0,1280,720], "blend": "overlay",
  "animate": { "opacity": [{"t":0.5,"v":0,"ease":"inOutSine"},{"t":2,"v":0.35}] } }
```

### Long flat shadow
Stack offset duplicates of the word in a dark colour behind the master (generated in a loop — 20–30 copies stepping `+5,+5`).
```json
{ "type": "text", "text": "SHADOW", "font": "./font.ttf", "size": 150, "color": "#0a0f20", "box": [5,255,1280,200], "align": "center middle", "opacity": 0.9 }
```
*(Repeat with box offset `[i*5, 250+i*5, …]` for i = N…1, then the bright master at `[0,250,…]`.)*

---

## 6. Extras

### Rich text — many styles in one block (VERIFIED)
Weight and slant come from **font files** per span; `bold`/`italic`/`underline`/`highlight`
flags do **not** render (verified: the flagged version came out in one weight, no underline,
no highlight). Spans must be **contiguous over every character, spaces and punctuation
included** — the renderer drops any character no span covers, so skipping the spaces renders
"BolditaliccolourSIZE" with the tail of the sentence gone.
```json
{ "type": "text", "text": "Bold, italic, colour & SIZE - one block, many styles.",
  "font": "./font.ttf", "size": 58, "color": "#cfd6e6", "box": [120,250,1040,280], "align": "center middle", "leading": 1.3, "shrink": true,
  "styles": [
    { "start": 0,  "length": 6,  "font": "./font-bold.ttf",   "color": "#ffffff" },
    { "start": 6,  "length": 8,  "font": "./font-italic.ttf", "color": "#c4b5fd" },
    { "start": 14, "length": 9,  "color": "#22d3ee" },
    { "start": 23, "length": 7,  "size": 92, "font": "./font-bold.ttf", "color": "#fde047" },
    { "start": 30, "length": 23, "color": "#cfd6e6" }
  ],
  "animate": { "opacity": [{"t":0,"v":0},{"t":0.6,"v":1,"ease":"outCubic"}] } }
```
*(0+6+8+9+7+23 = 53 = the string length — no gaps.)*

### Easing comparison — one ease per row
One row per ease, so you can feel the difference side-by-side.
```json
{ "type": "solid", "color": "#a855f7", "box": [360,126,30,30], "mask": { "ellipse": [375,141,15,15] },
  "animate": { "position": [{"t":0.6,"v":[0,0],"ease":"outBounce"},{"t":2.6,"v":[820,0]}] } }
```
*(Duplicate per ease — `linear`, `inOutSine`, `outCubic`, `outQuint`, `outExpo`, `outBack`, `outElastic`, `outBounce` — stacking the rows down the frame with a label each.)*
