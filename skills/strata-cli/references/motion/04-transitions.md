# Transitions and scene connection

> Executing these in a scene - designed transitions rather than crossfades, and the keys that
> make them - is [motion-design.md](../motion-design.md). ⚠ A clip destined for a `.jet` overlay,
> or asked of a video model as one take, must contain **no cuts at all**:
> [video-generation.md](../video-generation.md).

Contents: 1 Editorial cuts · 2 Dissolves and fades · 3 Wipes and geometric · 4 Motion-driven · 5 Textural and effect-driven · 6 Transition system design

## 1. Editorial cuts
- **Hard cut**: instant change; the default; cut on a beat or on action.
- **Cut on action**: cut mid-motion so movement carries across; hides the cut.
- **Match cut**: the next shot matches shape, color, position, or motion of the last; the most satisfying transition.
- **Jump cut**: same framing, time skip; urgency, social energy.
- **Smash cut**: abrupt tonal or volume contrast (quiet to loud).
- **J-cut and L-cut**: audio leads or lags the picture; VO or music pulls into the next scene.
- **Flash frame**: 1-2 white or black frames between shots; punctuation.
- **Montage rhythm**: rapid cuts on beat; cut length shrinks toward the climax.
- **Freeze-frame cut**: stop time, then cut or zoom out of the freeze.
- **Rewind and scrub**: reverse playback as a transition.
- **Hidden cut**: a cut concealed by a foreground object, a flash, or a blur.

## 2. Dissolves and fades
- **Crossfade**: overlapping opacity; softest, slowest; avoid mid-sequence in energetic work.
- **Dip to color**: fade to black, white, or brand color, then up; chapter breaks.
- **Light flash**: exposure blows out then recovers; energetic and cinematic.
- **Blur through**: outgoing blurs while incoming sharpens; a focus pull between scenes.
- **Color-match dissolve**: dissolve between matching palettes so the change is nearly invisible.
- **Fade through color (UI)**: outgoing fades to a surface color, incoming fades up; Material's default.

## 3. Wipes and geometric
- **Linear wipe**: an edge travels across; directional; retro when obvious.
- **Radial (clock) wipe**: a rotating edge; timers, retro.
- **Iris**: circle opens or closes; classic and playful.
- **Shape wipe**: a brand shape grows to cover the frame; the new scene lives inside it.
- **Barn door and split**: frame splits from the center or into halves.
- **Blinds and shutter**: parallel strips reveal in sequence.
- **Slice and slide**: the frame is cut into bands that slide at staggered times.
- **Push and slide**: outgoing is pushed off by incoming; spatial, navigation-like.
- **Cover and reveal**: incoming slides over the outgoing (cover) or outgoing slides away (reveal).
- **Tile flip**: the frame's tiles flip individually in a cascade.
- **Page turn**: a literal page metaphor; storybook, print.
- **Cube and card flip**: 3D rotation with perspective; spatial and tactile.
- **Fold (accordion)**: the frame folds into strips; paper, origami.
- **Shared axis (UI)**: outgoing and incoming move along one axis (X for lateral, Y for vertical, Z for depth) with a fade.

## 4. Motion-driven (camera and object)
- **Whip pan blur**: fast pan into a blur, cut, blur settles into the next scene.
- **Zoom through**: push into an element (a letter counter, a circle, a window) until it fills the frame, revealing the next scene.
- **Infinite zoom**: continuous zoom where each scene nests inside the last.
- **Scale past camera**: an element grows beyond the frame edges; the new scene is behind it.
- **Fly-through**: the camera passes between layers or through a gap.
- **Continuous one-shot**: no cuts; the camera moves from scene to scene through a built space.
- **Motion vector handoff**: exit direction of scene A = entry direction of scene B.
- **Speed ramp cut**: accelerate into the cut, decelerate out.
- **Object wipe**: a foreground element (car, hand, product) crosses the frame and the cut hides behind it.
- **Anchor object**: one element persists and reconfigures (a circle becomes a pie chart becomes a logo).
- **Morph transition**: shape A interpolates into shape B while the content changes inside.
- **Portal**: an element becomes a window into the next scene.
- **Background swap with persistent foreground**: the background changes while the subject stays.
- **Container transform (UI)**: a small element expands into the next view and collapses back.
- **Parallax departure**: layers exit at different speeds, implying the camera moved on.

## 5. Textural and effect-driven
- **Liquid and ink bleed**: fluid masks reveal; organic brands.
- **Particle dissolve**: outgoing breaks into particles that re-form as incoming.
- **Glitch cut**: RGB split, block displacement, and a frame drop over 4-8 frames.
- **Datamosh**: pixel blocks smear from one scene into the next.
- **Pixelate (mosaic)**: resolution collapses then rebuilds.
- **Light leak**: a warm flare washes over the cut.
- **Lens flare wipe**: a flare sweeps across, hiding the cut at peak brightness.
- **Bokeh crossover**: defocused highlights bridge the scenes.
- **Film burn and frame slip**: analog artifacts on the cut.
- **Invert flash**: a 1-2 frame inverted-color frame as punctuation.
- **Strobe and shutter flicker**: rapid on-off at the cut; aggressive (mind flash limits).
- **Smear and liquify**: pixels stretch across the cut.
- **Noise dissolve**: a threshold noise pattern eats the outgoing scene.

## 6. Transition system design
- **Hierarchy**: big transitions for big story beats; small cuts within a beat.
- **Vocabulary limit**: one primary and one secondary transition per project; a third only for the climax.
- **Duration budget**: 8-20 frames for most transitions; 20-40 for a hero moment.
- **Invisible is best**: the viewer notices the new scene, not the transition.
- **Motivation**: triggered by an action, a beat, or a spoken word.
- **Direction logic**: forward in the story = left-to-right or inward; backward = reversed (mirror for RTL markets).
- **Exit choreography**: exits run faster than entrances and clear the frame before the new scene's hero lands.
- **Overlap**: incoming starts before outgoing is fully gone, unless the empty frame is the point.
- **Sound**: every transition has a sound (whoosh, click, riser); silent transitions feel unfinished.
- **Brand-derived transitions**: build the wipe or mask from logo geometry.
- **Continuity anchors**: keep one thing constant across the cut (color, position of the subject, a sound) so the brain accepts the change.
- **Transition as information**: the type of transition tells the viewer what kind of change happened (lateral = sibling, zoom = child, dip = new chapter).
