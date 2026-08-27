# Brand document — capture a visual identity once, reuse it on every video

A brand document is how a user gets **videos that come out the same** every time. Without
one, every session re-invents the palette, the type sizes, the corner treatment and the
motion, and the fifth video looks unrelated to the first.

The artefact is **`.brand/brand.md`** in the user's project. I write it from the assets
they give me, they approve it, and from then on **every scene is authored from it**.

Two jobs, both in this file: **§A build it** · **§B use it**.

---

# §A — Building `.brand/brand.md`

## Contents

- [When to build one — I offer it, I don't wait to be asked](#when-to-build-one--i-offer-it-i-dont-wait-to-be-asked)
- [Step 1 — Inventory every input, then actually look at it](#step-1--inventory-every-input-then-actually-look-at-it)
- [Step 1½ — After Effects files: the richest brand source](#step-1½--after-effects-files-the-richest-brand-source)
- [Step 2 — Fill the template (§C below), obeying the strata traps](#step-2--fill-the-template-c-below-obeying-the-strata-traps)
- [Step 3 — Say what I could not know](#step-3--say-what-i-could-not-know)
- [Step 4 — Show it and get sign-off](#step-4--show-it-and-get-sign-off)
- [Rules](#rules)

## When to build one — I offer it, I don't wait to be asked
- The user hands me **brand assets** (logo, brand book/PDF, style guide, screenshots,
  existing videos, a website, a Figma file) → *"want me to capture this as a brand
  document so every video matches?"*
- They say **"like the last one"**, "on brand", "same style", "our template", or ask for
  a **series** / multiple videos / a campaign.
- They're on their **second** video in a project and there is no `.brand/`.
- ❗ If `.brand/brand.md` already exists I **read it and follow it** — I never silently
  rebuild it. I update it only when the user says the brand changed (and I say what I
  changed).

## Step 1 — Inventory every input, then actually look at it
I list what I was given and read **all** of it. Nothing goes in the document that I did
not see in an asset or hear from the user.

| Input | How I extract from it |
|---|---|
| **Images** (logo, screenshots, style frames, product shots) | Read the image and describe it; extract the palette **with code**, not by eye (below) |
| **Videos** (existing brand videos, competitor refs) | `ffmpeg -i in.mp4 -vf "select='eq(n\,0)+eq(n\,90)',tile=2x1" -vsync 0 -frames:v 1 f.png` → look at real frames. Note pacing, transitions, type entrances, where the logo lands and for how long |
| **Docs** (brand book PDF, style guide, deck, markdown) | Read them. Lift exact hexes, font names, spacing rules, tone-of-voice and any legal/usage rules |
| **Fonts** (`.ttf`/`.otf` files) | Record the **absolute file path** — a family name alone is useless (see the trap below) |
| **A website / Figma** | Figma → [figma.md](figma.md) (variables/tokens are the palette + type ramp). A site → its CSS variables and screenshots |
| **After Effects projects** (`.aep`/`.aepx`/Lottie JSON) | The **richest** source — see §A½. Read the type ramp, palette, spacing AND the real motion curves out of it |
| **An edited video / raw footage** | Frames for palette + type; [video-editing.md](video-editing.md) if it needs trimming before use |
| **Nothing but a logo** | Build the palette and type from the logo, then **flag every gap** rather than inventing |

## Step 1½ — After Effects files: the richest brand source
An AE template is a brand's motion identity written down. Unlike a logo or a PDF it
carries **the easing curves, durations and stagger** — the things that make two videos
feel like one brand and that I would otherwise have to guess. Routes, best first:

| Route | How | Gets you |
|---|---|---|
| **`.aepx`** (AE: *File → Save a Copy As → XML*) | plain XML — parse it directly | comps, layer names + transforms, keyframes, text, precomp tree, colours. No AE needed to read it |
| **`.jsx` ExtendScript** *(if the user has AE)* | ask them to run a dump script; walk `app.project` → JSON | highest fidelity: every property, keyframe, ease, effect, marker, font name/size |
| **Lottie / bodymovin JSON** | ask for a bodymovin export | cleanest structured data (shapes, transforms, bezier easing, text) — but only AE's supported subset, no video layers |
| **`.aep`** (binary) | RIFX container; undocumented, values are packed binary | last resort. Prefer asking for `.aepx` — it is one menu item away |
| **A render of the comp** | frames via ffmpeg | if all else fails: palette + type + timing by observation |

**What I pull out of it, into the token block:**
- **colors** — layer fill/solid colours and shape fills (exact values, not sampled).
- **typography** — font family + size + tracking + leading per text layer, and the *ramp*
  they form. ⚠️ AE stores a family **name**; I still need the actual `.ttf`/`.otf` **file**
  (ask for it — see the fonts trap in step 2).
- **spacing / canvas** — comp size + fps, layer positions → margins and the grid.
- **motion (the prize)** — keyframe **timing** and **ease** per property. Convert AE
  influence/speed handles to the nearest strata ease (`outExpo`, `outBack`, a raw
  cubic-bezier), record entrance duration, stagger between siblings, and the transition
  style. That becomes the `motion:` block.
- **components** — precomps map 1:1 to strata sub-comps (a comp IS a group).
- **effects** — AE drop shadow/glow/stroke → the `shadow`/`glow`/`stroke` tokens.

⚠️ **Expressions do not translate** — they're code, not values. If a property is driven by
an expression I record the *observed* behaviour and note it in Known Gaps.

💡 **Idomoo ships an AE exporter** (`IDMVideoEncoder.aex`). If the user's goal is "render
this exact AE comp", that plugin is the right path — the brand document is for
*reusing the identity* on new videos, not reproducing one comp.

**Extract the palette with code — never eyeball hexes.** Sampling pixels is computed work,
so a throwaway script is the right tool (see [generative-fx.md](generative-fx.md) for the
pattern). Count quantised colours across the logo/frames, drop near-white/near-black
unless they're structural, and keep the top 5–8 by frequency:

```js
// palette.mjs — dominant colours from an image, exact hexes. Self-contained: ffmpeg dumps
// raw RGBA (works for PNG/JPG/WebP and for a video frame with -ss), no decoder needed.
import { execFileSync } from 'node:child_process';
const [,, file, W = 256] = process.argv;                 // downscale for speed; colours survive
const H = W;
const rgba = execFileSync('ffmpeg', ['-v', 'error', '-i', file, '-vf', `scale=${W}:${H}`,
  '-f', 'rawvideo', '-pix_fmt', 'rgba', '-frames:v', '1', '-'], { maxBuffer: 1 << 28 });
const bins = new Map();
for (let i = 0; i < W * H; i++) {
    const a = rgba[i*4+3]; if (a < 128) continue;                  // skip transparent
    const q = c => Math.round(c / 16) * 16;                        // quantise to 16 steps
    const k = `${q(rgba[i*4])},${q(rgba[i*4+1])},${q(rgba[i*4+2])}`;
    bins.set(k, (bins.get(k) ?? 0) + 1);
}
[...bins].sort((a,b) => b[1]-a[1]).slice(0, 12).forEach(([k, n]) => {
    const [r,g,b] = k.split(',').map(Number);
    console.log('#' + [r,g,b].map(v => Math.min(255, v).toString(16).padStart(2,'0')).join(''), (100*n/(W*H)).toFixed(1) + '%');
});
```

## Step 2 — Fill the template (§C below), obeying the strata traps
The template is the **contract**: same section order, same yaml token block. Four rules
that make the difference between a document that works and one that fails at compile time:

1. **Fonts are FILE PATHS.** `"font": "Inter"` does not compile. Record
   `C:/…/Inter-Bold.ttf` (or `./fonts/Inter-Bold.ttf`) per ramp entry, plus a fallback,
   plus confirmation the file covers the glyphs the brand needs (currency, accents, ₪/€,
   emoji). A missing glyph crashes the export.
2. **Sizes are px at a stated canvas.** VASCO has no `cqw`/container queries. I declare
   the **primary canvas** (usually 1920×1080) and give every size in px at it, plus the
   conversion for other ratios: `size_other = size_1920 × (width_other / 1920)`.
3. **Corner radius is a mask `rect` + `radius`.** Record the brand's radii in px at the
   primary canvas (`card: 24`, `button: pill`) and apply them as
   `"mask": { "rect": [x,y,w,h], "radius": 24 }` — a big number clamps to half the shorter
   side, giving a pill. `radius` also takes `[tl,tr,br,bl]` for one-sided rounding. It is a
   MASK key, never a layer key.
4. **CSS shadows become VASCO `shadow` effects.** A hard offset shadow `8px 8px 0 black`
   is `{ "type": "shadow", "color": "#000000", "angle": 135, "distance": 11, "size": 0, "spread": 100 }`
   — convert: `distance = hypot(x, y)`, `angle = atan2` in degrees, blur `0` → `size: 0`.
   Record the converted token, not the CSS.

**Add the section the source template leaves out: MOTION.** The template declares motion
out of scope because it was written for still frames. For video it is the single biggest
consistency lever — a brand whose type always snaps in on `outExpo ~450ms` reads
consistent even when layouts differ. So the document always carries a **Motion Language**
section (entrance ease + duration, exit, stagger, transition type, camera stance, logo
timing) drawn from the user's existing videos if they gave any. See
[motion-design.md](motion-design.md).

## Step 3 — Say what I could not know
Every gap goes in **Known Gaps** and I ask about the ones that matter. I never fabricate:
- a colour that isn't in an asset,
- a font I don't have a file for,
- a claim, stat, price or customer name (see Numerals & Claims in the template).
Placeholders instead: `— figure —`, `{metric}`, `<logo asset needed>`.

## Step 4 — Show it and get sign-off
I write `.brand/brand.md`, then summarise for the user: **palette + type + the 3 laws +
what's missing**. Cheap to fix now, expensive after ten videos are built on it. I also
save the assets I was given next to it (`.brand/assets/`) so paths stay valid.

---

# §B — Using `.brand/brand.md` on every video

1. **Read it before the storyboard** — it decides the canvas, palette, type ramp,
   components and motion, so it comes before layout choice, not after.
2. **Tokens beat invention.** Every colour, size, font, shadow and ease comes from the
   document. If a shot needs something the brand lacks, I say so and propose an addition
   rather than quietly inventing one.
3. **Layout is still free** (unless the brand fixes it): the document governs *atoms and
   laws*; [layouts.md](layouts.md) still picks the frame. That's the template's own
   principle — *atoms are sacred, composition is free*.
4. **Generated assets inherit the brand:** pass the palette to
   `strata generate image --colors "#a,#b"` and put the brand's aesthetic words in the
   prompt, so AI art doesn't drift off-identity.
5. **Run the Pre-Render Self-Audit** from the document before I call a video done —
   it's the brand's own checklist, on top of the craft check in SKILL.md.
6. **Version the scene files** per cut (`promo_v1.json` → `promo_final.json`) so a series
   built on one brand stays legible in the library.

---

# §C — The template

Copy this into `.brand/brand.md` and fill every section. Keep the section order and keep
the token block as **one fenced yaml block** (never `---` frontmatter — markdown viewers
render it as raw text and wreck the header). Delete a section only if it genuinely does
not apply; leave a one-line note saying why.

````markdown
# <BrandName> — Video Frame System

![Brand logo](<relative path in .brand/assets/>)
![Moodboard](<relative path or url>)

## Design Tokens

```yaml
version: alpha
name: # <BrandName> — video/frame layer. Companion to the brand's static design system.
description: >
  # 3-6 lines compressing the whole identity at frame scale. Name the sacred atoms
  # (palette, type pairing, borders, shadows, corner treatment, signature decorations)
  # in one breath. State what was adapted for video (composition, sizing, motion) and
  # what is out of scope.
unit: # authoring unit + primary canvas, e.g. "px at 1920x1080; 1080x1920 and 1080x1080 documented"
principle: # the one-line law, e.g. "atoms are sacred - composition is free - numbers come from the script"

canvas:
  primary: [1920, 1080]        # the canvas every px token below is stated at
  also: [[1080, 1920], [1080, 1080]]
  fps: 25
  scale_rule: "size_other = size_1920 * (width_other / 1920)"

colors:
  # semantic name -> hex, exact values from the assets (extracted with code, not by eye)
  # ground: "#0e1220"
  # ink: "#ffffff"
  # accent-primary: "#f0533a"

fonts:
  # every entry needs a REAL file path - a family name alone does not compile
  # display: { path: "./fonts/Brand-Bold.ttf", family: "Brand Bold", covers: "latin, currency" }
  # body:    { path: "./fonts/Brand-Regular.ttf", family: "Brand Regular", covers: "latin" }
  # fallback: { path: "C:/Windows/Fonts/segoeui.ttf" }

typography:
  # two ramps, px at the primary canvas. entry: { font, size, leading, tracking, upper?, color }
  # -- reading/chrome --  body, label, eyebrow, caption, counter
  # -- display --         heading-md/lg/xl, quote, stat-number, close-title
  # note the legibility floor (smallest px allowed for load-bearing text)

spacing:
  # px at the primary canvas
  # margin: 120        # outer safe padding (title-safe)
  # gap-md: 40
  # bottom-safe: 950   # y of the CTA/footer line

effects:
  # VASCO effect tokens, already converted from any CSS
  # shadow-default: { type: shadow, color: "#000000", angle: 135, distance: 11, size: 0, spread: 100 }
  # stroke-primary: { type: stroke, color: "#000000", size: 4, position: outside }
  # glow-hero:      { type: glow, color: "#ffd166", size: 6, opacity: 0.7 }

shapes:
  # corner treatment, in px at the primary canvas
  # radius: { card: 24, button: pill, media: 16 }   -> "mask": { "rect": [...], "radius": 24 }

components:
  # reusable atoms -> authored as sub-comps (a comp IS a group). reference tokens as
  # {colors.x} / {typography.x}. cover at minimum: primary card, small card,
  # eyebrow/label unit, CTA, each signature decoration, closer/end plate.
  # card-primary:
  #   background: "{colors.ground-raised}"
  #   stroke: "{effects.stroke-primary}"
  #   shadow: "{effects.shadow-default}"
  #   typography: "{typography.card-title}"
  #   description: ""   # role + coupling rules

motion:
  # THE video-specific section. what makes every video feel like the same brand.
  # entrance: { ease: outExpo, duration: 0.45, from: "position [0,60] + opacity 0" }
  # exit:     { ease: [0.7,0,0.84,0], duration: 0.3 }
  # stagger:  0.15            # between sibling elements
  # text:     "per-word rise, shape square"
  # transition: "scale-through, 0.4s"   # the ONE house transition
  # camera:   "slow 3% push on hero shots; no orbits"
  # logo:     "end plate, holds 1.5s minimum"
  # motion_blur: true
```

## Overview
<!-- 2-3 paragraphs + bullets. Name the aesthetic in one bolded phrase and enumerate the
laws every element must obey. Then the typographic voice and the colour rhythm across
shots, and the depth model. Close with "Key characteristics at frame scale:" - 5-7 bolded
one-liners covering borders/shadows, palette behaviour, display type, corner rules,
signature decorations, density stance. -->

## The Frame
### Frame Craft Bar
<!-- The eyeball tests that gate every frame: **Squint** (what dominates, hierarchy ratio),
**Silence** (where air must live, and the dense exceptions), **Restraint** (hard couplings
and ceilings), **Reference** (one vivid analog target + what failure looks like). -->
<!-- Then geometry: primary canvas + ratio, secondary ratios, safe area token, whether
decorations may bleed off-frame. -->

## Colors
<!-- How the tokens BEHAVE, not just what they are: default ground, how grounds cycle
across shots, which colour owns structure vs fills, whether accents are semantic or
interchangeable, the CTA colour, single-purpose colours, and the hard ceilings. -->

## Typography
<!-- Ramp architecture (reading/chrome vs display) then the hard rules: legibility floor,
how headline size is chosen by word count / measure, casing + weight + tracking laws per
ramp, and explicit "no ___" violations. Restate the font FILE paths. -->

## Depth & Surface
<!-- Shadow/stroke/glow per element class with px values, non-shadow depth (layering, z +
camera), the exceptions and where they are allowed. **Ceiling:** forbidden treatments. -->

## Shapes
<!-- Corner and geometry law in 1-2 bullets, including named exceptions, and the radius
tokens in px at the primary canvas (applied as `radius` on a mask `rect`). -->

## Components
<!-- Bullet digest of the token components grouped by role (content cards, chrome/eyebrow,
CTA, decorations, closer), one-line purpose each, plus per-frame minimums. Each maps to a
sub-comp in `comps`. -->

## Motion Language
<!-- The house motion, from the motion tokens: entrance ease + duration, exit, stagger,
the ONE transition, text animator style, camera stance, logo timing, motion blur.
State the tells to avoid (everything fading in together, linear easing, dead frames). -->

<!-- HOW TO DERIVE THESE when the brand does not state them. Pick the archetype the brand's
own words imply, then take its row as the starting point and write concrete values in.
Adapted from LottieFiles' motion-design skill (MIT) — the archetypes and relationships are
theirs; the frame values are UI-scale converted at 25fps and run FAST for full-frame video
work, so treat them as a floor for on-screen elements and lengthen for full-frame moves.

| Archetype | Brand words that imply it | Signature ease | Quick / Standard / Slow | Overshoot | Entrance pattern |
|---|---|---|---|---|---|
| **Playful** | fun, whimsical, bouncy, cute, friendly | `outBack` | 4f / 6f / 10f | 10-20% | bounce up from below |
| **Premium** | elegant, minimal, luxury, sophisticated | `[0.4,0,0.2,1]` | 9f / 12f / 20f | **0%** | slow fade + scale 98%->100% |
| **Corporate** | clean, professional, business, dashboard | `[0.2,0,0,1]` | 5f / 8f / 11f | 0-3% | slide + opacity |
| **Energetic** | dynamic, energetic, bold, exciting | `outExpo` | 3f / 5f / 8f | 15-30% | snap from edge + overshoot |

Then fix three constants and repeat them everywhere - consistency IS the brand motion:
1. ONE signature ease used on ~80% of moves; 2. the three-tier duration palette above;
3. ONE entrance pattern. Exits run at 65-75% of the entrance duration.

Mixing: keep ~90% to the primary archetype. A single moment may borrow another (a corporate
piece borrowing Playful for one success beat), but ease INTO the shift, never snap. -->

## Shot Treatments
> <!-- Legend: the fixed field order every treatment follows -
> "ground - composes - focal - chrome - accent - silence - motion - Fixed/Free - density".
> Plus cross-treatment laws (cycle the ground, open with an eyebrow, min decorations). -->
<!-- Then 5-8 numbered treatments, each:
### N - <Name>  (role - move: <signature compositional move>)
**Ground** token + surface. **Composes** ordered components. **Focal** what dominates and
how it is set. **Chrome** supporting labels. **Accent** where colour concentrates.
**Silence** where the air lives. **Motion** the entrance/exit for this treatment.
**Fixed** non-negotiables. **Free** what may vary. **Density** low/comfortable/dense.
Cover at minimum: opener/cover, card or catalog grid, a data/stat shot, a quote,
a process/timeline, and a closing plate. Name the matching layout from layouts.md. -->

## Composition Rules
### Do
<!-- 5-7 positive laws: the couplings, the cycling, casing, decoration minimums, CTA
colour, safe-area discipline, one focal point per shot. -->
### Don't
<!-- 5-7 violations: forbidden radii/blur/colours/casing, overlapping text, dead thirds,
off-frame boxes, fabricated numerals. -->

## Aspect-Ratio Behavior
<!-- | Treatment | 16:9 | 9:16 | 1:1 | - one cell = how the layout re-flows.
Below it: which spacing token holds on the short edge, how display type re-steps against
the legibility floor, how decoration count scales down. -->

## Approved Entities
<!-- Which real brands/customers/logos may be rendered; what must be a placeholder;
what is content-agnostic. -->

## Numerals & Claims (hard rule)
<!-- Never invent figures, stats, prices, counts or customer names. Name the placeholder
syntax (`— figure —`, `{metric}`, `N×`) and which numerals are exempt as decorative. -->

## Asset Inventory
<!-- What lives in .brand/assets/ (logo files, fonts, textures, moodboard) with the path
each scene should reference, plus what is still MISSING and blocks which treatment. -->

## Pre-Render Self-Audit
<!-- One line each, mirroring the Craft Bar + hard rules: **Squint** hierarchy -
**Silence** air - **Borders/shadows** coupling - **Color** cycling + limits -
**Type** casing/tracking/floor + real font file - **Shape** corners + decoration minimum -
**Motion** one entrance family, nothing static - **Fabrication** every numeral traces to
the script. -->

## Known Gaps
<!-- Honest limitations: what the assets did not reveal, fonts without files, ratios that
are guidance vs verified, treatments that need a missing asset, and what the user still
needs to decide. -->
````

---

## Rules
- **`.brand/brand.md` is the source of truth** once it exists — read it, follow it, and
  update it deliberately (never silently).
- **Nothing invented.** Every token traces to an asset or an explicit user answer; the
  rest is a named gap.
- **Fonts as file paths, sizes in px at a stated canvas, corners via path masks, CSS
  shadows converted** — the four traps from §A step 2.
- **Motion is part of the brand** here even though the source template omits it.
- **Keep the yaml token block machine-readable** — a later session (or another agent)
  parses it instead of re-reading the prose.
