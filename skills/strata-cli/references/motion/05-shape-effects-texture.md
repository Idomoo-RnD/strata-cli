# Shape, masks, effects, particles, color, materials

> What this engine can actually do - glow, blend modes, track mattes, particles, grain, masks,
> audio-reactive fills - and the key for each is [generative-fx.md](../generative-fx.md) and
> [format.md](../format.md). A cut-out subject that sits over another layer needs alpha, which
> means a `.jet`: [assets.md](../assets.md).

Contents: 1 Shape and form · 2 Masks and mattes · 3 Optical effects · 4 Particles and systems · 5 Color, light, compositing · 6 Materials and surfaces

## 1. Shape and form
- **Shape morph**: interpolate one path into another; match point counts; complex morphs need intermediate poses.
- **Path trim (stroke draw)**: reveal a line from 0 to 100%; icons, diagrams, signatures, routes.
- **Dash offset**: dashes travel along a path; flow, data transfer, marching ants.
- **Offset path (grow and shrink)**: inflate or deflate a shape; pulsing rings, outlines.
- **Corner radius animation**: square to pill; softness and state change.
- **Polygon morph**: triangle to square to circle; geometric brands.
- **Repeater (radial array)**: copies around a center; mandalas, loaders, sunbursts.
- **Grid array**: copies in rows and columns with staggered animation; patterns, halftone builds.
- **Instancing with variation**: copies vary in scale, rotation, or color by index; richer than uniform copies.
- **Blob (metaball)**: shapes merge when near; organic, liquid.
- **Wiggle path**: noise on vertices; hand-drawn feel.
- **Line boil**: the outline is redrawn every 2-3 frames; hand-made warmth.
- **Ribbons and noodles**: thick stroked paths that travel; connectors, energy.
- **Isolines (contours)**: topographic stacks that drift.
- **Line art build**: a diagram drawn piece by piece in logical order.
- **Extrusion and lathe**: 2D profile to 3D solid; logos, products.
- **Wireframe to solid**: geometry appears as lines, then fills and lights.
- **Primitives as actors**: circles, lines, and rectangles that behave like characters.
- **Shape language consistency**: radius, stroke width, and angles stay constant across a project.
- **Scale from a point**: shapes grow from a meaningful origin (a tap, a node, the logo), not from the center by default.
- **Rotation reveals**: a shape rotates into its final orientation as it appears; adds energy cheaply.

## 2. Masks and mattes
- **Track matte (alpha or luma)**: one layer defines another's visibility; footage in type, gradients in shapes.
- **Inverted matte**: hide inside the shape; cut-outs and peek-throughs.
- **Animated mask path**: the reveal edge moves with intent (a drawn stroke, a growing circle).
- **Feathered mask**: soft edges; light, fog, vignettes.
- **Mask as window**: a scene seen through a shape; depth and curiosity.
- **Shape in shape**: nested masks for layered reveals.
- **Split screen**: masks divide the frame for comparison and multiplicity.
- **Luma key and chroma key**: remove bright, dark, or colored regions; fast compositing.
- **Garbage matte**: a rough mask that discards unwanted areas.
- **Rotoscoping**: frame-by-frame masking of live subjects; lets graphics go behind people.
- **Mask from motion**: a mask that follows a moving object to keep it separated from effects.

## 3. Optical effects
- **Motion blur**: directional blur proportional to speed; sells fast moves and hides stepping; 180° shutter as baseline.
- **Radial (zoom) blur**: speed lines from a center; impact, speed.
- **Gaussian blur**: softness and depth hierarchy.
- **Lens blur (bokeh)**: depth of field with shaped highlights; premium.
- **Chromatic aberration**: RGB fringing at edges; lens realism or glitch.
- **Glow (bloom)**: bright areas bleed; neon, sci-fi, highlights.
- **Light sweep (specular pass)**: a highlight travels across a surface; logos and metallic type.
- **Light leak**: warm organic flares; film nostalgia.
- **Anamorphic flare**: horizontal streak flares; cinematic.
- **Vignette**: dark edges pull focus to the center.
- **God rays (volumetric light)**: shafts through gaps; drama.
- **Shadows (drop, contact, long)**: depth, grounding, flat-style dimension.
- **Reflection and refraction**: glass, water, chrome.
- **Fresnel rim light**: bright edges on 3D objects; separation from background.
- **Displacement**: heat shimmer, water ripple, wave, turbulence; organic distortion.
- **Liquify and smear**: pixels stretch; transitions and effects.
- **Pixel sort**: streaks of sorted pixels; glitch art.
- **Glitch stack**: RGB split, block displacement, scanlines, frame holds, slice jitter; bursts of 3-8 frames.
- **VHS tracking**: wobble, color bleed, noise bars.
- **Film grain and noise**: texture and cohesion; also hides gradient banding.
- **Dust, scratches, hair**: analog wear.
- **Halation**: glow around bright edges; film look.
- **Gate weave**: slight frame jitter; projector feel.
- **Flicker**: exposure variation; old film, neon, horror.
- **Posterize time (frame drop)**: playback at 8-12 fps; stop-motion and retro.
- **Echo, trails, onion skin**: ghost copies behind motion; speed and rhythm.
- **Time displacement**: parts of the frame at different times; surreal.
- **Stroboscope**: freeze-frame bursts.
- **Mosaic (pixelation)**: resolution as a design element.
- **Halftone and dither**: print and retro screens.
- **Duotone (gradient map)**: remap tones to two colors; editorial cohesion.
- **Hue cycle**: continuous hue rotation; loops and psychedelia.
- **Exposure flash**: brief overexposure on a beat.
- **Outline and stroke effects**: animated outlines around subjects; sticker and sports looks.
- **Emboss, inner shadow, bevel**: tactile surfaces; sparingly.
- **Scanlines and CRT curvature**: retro monitor feel.
- **Kaleidoscope and mirror**: symmetric multiplications; psychedelic, fashion.

## 4. Particles and systems
- **Emitter types**: point, line, box, sphere, from-shape, from-text.
- **Confetti**: flat cards with tumble and drag; celebration.
- **Sparks**: fast, short-lived, additive; impacts.
- **Dust motes**: slow, tiny, ambient life for any scene.
- **Smoke and fog**: soft sprites with turbulence.
- **Rain and snow**: directional fall with wind noise.
- **Bubbles**: rise with wobble and pop.
- **Bokeh particles**: defocused circles drifting; premium backgrounds.
- **Trails**: particles leave fading paths.
- **Flocking (boids)**: separation, alignment, cohesion; organic swarms.
- **Attractors and fields**: particles pulled toward points or along vector fields.
- **Text to particles and back**: dissolve and re-form reveals.
- **Data particles**: each particle is a unit of data; counts, flows, populations.
- **Physics sims**: soft body, cloth, fluid; "satisfying" content.
- **Particle budget**: density sells, but the subject must stay readable.
- **Particle lifecycle**: birth (scale in), life (drift), death (fade); a visible death avoids popping.

## 5. Color, light, compositing
- **Blend modes**: multiply (shadows, ink), screen and add (light, glow), overlay (contrast), difference (glitch); blend modes make layers feel like one image.
- **Light wrap**: background light bleeds onto subject edges; believable compositing.
- **Color continuity**: shared palette or grade across scenes; color jumps feel like a different project.
- **Background color animation**: the cheapest, strongest mood shift; swap brand colors on section changes.
- **Gradient animation**: rotate the angle, move stops, drift mesh points; living backgrounds.
- **Color pulse**: subtle saturation breathing on idle.
- **Desaturate for focus**: everything but the subject loses color.
- **Tonal hierarchy**: the subject gets the highest contrast; support mid; background low.
- **Exposure-aware compositing**: glows add light, shadows multiply; don't fake either with opacity.
- **Grain for cohesion**: a single grain pass unifies photos, vectors, and 3D.
- **Dithered gradients**: avoid banding on smooth gradients.
- **Color transition as transition**: a palette swap can replace a cut.
- **Lighting rigs for 2D**: key, fill, and rim mimicked with gradients and shadows.
- **Dark-mode motion**: glows and additive light read stronger; shadows read weaker.
- **Color as timing cue**: a color change marks a beat even when nothing moves.
- **Accent discipline**: one accent color animates; the rest stays still.

## 6. Materials and surfaces
- **Paper and print**: grain, slight misregistration, risograph color layers.
- **Glass (glassmorphism)**: frosted blur, thin borders, specular edge, refraction on movement.
- **Neumorphism**: soft extruded surfaces with dual shadows; subtle state changes.
- **Clay (soft 3D)**: matte, rounded, friendly; squash-friendly.
- **Chrome and liquid metal**: high-contrast reflections that slide as the object turns.
- **Holographic and iridescent**: hue shifts with angle; animate a gradient map by rotation.
- **Neon**: glow, slight flicker, tube-shaped strokes drawn on.
- **LED matrix and pixel**: quantized grid with per-cell fade.
- **Fabric and cloth**: waves and folds; flags, banners.
- **Liquid and fluid**: surface-tension blobs, splashes, pours; food and beauty.
- **Wood, stone, metal overlays**: soft multiply textures for warmth.
- **Sticker and die-cut**: white outline, slight drop shadow, peel-in entrance.
