# Choreography, composition, depth, camera

> **Engine anchors.** Where things sit on the canvas - grid, margins, safe lines - is
> [layouts.md](../layouts.md) and [video-layouts.md](../video-layouts.md). The VASCO camera is an
> **exact pinhole with a measured calibration** and negative z moves a layer TOWARD it: read
> [format.md](../format.md) before any 3D or camera keyframe, or the shot comes out the wrong size.

Contents: 1 Staging and focal control · 2 Composition in motion · 3 Depth and dimensionality · 4 Camera language

## 1. Staging and focal control
- **One focal point per moment**: the eye tracks one moving thing; everything else holds or moves subtly.
- **Sequence is hierarchy**: the order elements enter is the order of importance.
- **Container before content**: backgrounds, cards, and frames enter first; their content follows.
- **Back to front on entrance**: build depth from background forward; strip front-to-back on exit, or mirror the entrance.
- **Exit reverses entrance** as the default; alternatively all exits move toward the next scene's origin (motivated exit).
- **Clean the frame**: remove competing elements before the key moment lands.
- **Eye trace**: know where the eye is at the end of a shot and start the next shot's action there.
- **Contrast of motion**: one still element among movers, or one mover among stills, is the strongest focus tool there is.
- **Amplitude hierarchy**: importance = bigger travel plus longer duration; details get smaller, faster moves.
- **Group vs individual**: move a group as one unit for cohesion; stagger individuals for liveliness; never mix at random.
- **Lead and follow**: one element leads and others follow with lag; the lag tells the story of the leader's weight.
- **Choreographic patterns**: sequential (A then B), overlapping (B starts at A's 60%), simultaneous (one event), interleaved (A1 B1 A2 B2), convergent (many to one), divergent (one to many).
- **Reveal order = story order**: cause before effect, question before answer, the thing before its label.
- **Motivated motion**: every move answers "why now, why there": a cursor, a cut, a beat, a spoken word, a physical cause.
- **Motion silence**: frames where nothing moves are part of the choreography.
- **Scene-level anticipation**: a small camera drift or dim before a big reveal.
- **Handoffs**: attention passes from element to element like a baton; the receiver starts as the giver settles.
- **Entrance density**: never more than three distinct entrances in flight at once.

## 2. Composition in motion
- **Grid and modules**: animate along the grid; elements land on grid lines so rest states look designed.
- **Rule of thirds and power points**: focal points land on them; movement toward them feels resolved.
- **Negative space**: moving an element into empty space is a reveal in itself; crowding kills motion.
- **Balance shift**: a move throws the frame off balance, then restores it (tension and resolution).
- **Symmetry vs asymmetry**: symmetric motion reads formal and corporate; asymmetric reads editorial and dynamic.
- **Scale hierarchy**: three sizes on screen at most; the largest is the subject.
- **Gestalt in motion**: common fate (things moving together are a group), proximity, similarity, continuity, closure (the eye completes a masked shape).
- **Z-order clarity**: stacking order never flips without a reason.
- **Reading patterns**: Z-pattern for image-led frames, F-pattern for text-heavy; stagger accordingly (mirror for RTL).
- **Framing devices**: foreground frames, windows, and masks that crop the subject add depth and focus.
- **Three planes**: back, mid, front are enough to feel rich.
- **Cropping big**: oversized type or objects cut by the frame edge feel premium and confident.
- **Margin system**: rest positions respect one margin system per project.
- **Visual weight of motion**: fast or large motion carries weight; balance it with still mass elsewhere.
- **Directional flow**: a consistent dominant direction per section (left-to-right, inward, upward) keeps the piece coherent.
- **Layout morph**: the grid itself can animate (columns merge, modules resize) as a scene change.
- **Safe composition**: moving text stays inside title-safe; decoration may bleed.

## 3. Depth and dimensionality
- **Flat 2D**: no depth cues; clean and graphic; depth via order and scale only.
- **2.5D**: flat layers spaced in Z with a real camera; parallax and focus appear for free.
- **Full 3D**: real geometry, lighting, materials; expensive and potent; products and logos.
- **Parallax ratio**: far layers move 10-30% of the camera, near layers 100-150%; exaggerate for drama.
- **Atmospheric perspective**: distant layers are lighter, lower contrast, softer, cooler.
- **Depth of field and rack focus**: blur all but the subject; pull focus to move attention.
- **Overlap and occlusion**: the simplest depth cue; front things cover back things.
- **Scale cues**: nearer = larger; pair scale with parallax to sell the move.
- **Contact shadows**: a soft shadow grounds an element; a shadow that grows as the element lifts says "hover".
- **Long shadows**: flat-design depth without lighting.
- **Reflections**: a faded, flipped copy below the subject; premium product feel.
- **Ground plane and horizon**: define where things can stand; a tilt reveals it.
- **Isometric and axonometric**: parallel projection, no perspective distortion; systems, maps, architecture.
- **Fake 3D**: card flips with perspective, extrusions from stacked copies, coins built from ellipses.
- **Depth stagger**: elements enter from different Z depths (scale from 0.8 vs 1.2) to feel spatial.
- **Fog and volumetric depth**: haze layers between planes add air.
- **Z-push**: the camera travels through the layer stack as a transition.
- **Depth-aware blur**: blur amount tied to distance from the focal plane; sells 2.5D instantly.

## 4. Camera language
- **Locked (static)**: most graphic work; motion lives in the elements.
- **Push-in and pull-out**: toward = intimacy and emphasis; away = context and release.
- **Pan and tilt**: rotate on the axis; reveals space; eased starts and stops.
- **Dolly, truck, pedestal**: translate forward, sideways, vertically; a truck beats a pan for parallax.
- **Orbit and arc**: circle the subject; hero products and 3D logos.
- **Crane and boom**: rise or descend with a tilt; grand reveals.
- **Dolly zoom (Vertigo)**: dolly in while zooming out; background warps while the subject stays; unease or epiphany.
- **Whip pan**: very fast pan with motion blur; cut inside the blur.
- **Handheld and drift**: low-amplitude noise on position and rotation for life; keep under 1-2% of frame.
- **Zoom vs dolly**: zoom changes field of view (flattens), dolly changes position (parallax); mixing them is a look.
- **Lens choice**: wide (dramatic, spatial, distorted) vs long (compressed, calm, product-like).
- **Roll (dutch)**: rotate around the view axis; tension, disorientation, energy.
- **Look-at and target tracking**: the camera follows a subject with a small lag.
- **Camera shake**: impact response; 3-8 frames of decaying amplitude, synced to a hit sound.
- **Camera easing**: cameras never move linearly; long ease-in-out or spring.
- **Motivated camera**: follows an action, a reveal, or a story beat; never wanders.
- **Lead room and headroom**: leave space in the direction of motion.
- **Screen direction**: subjects keep moving the same way across cuts unless a turn is shown.
- **Establishing to detail**: wide sets the scene, then push to detail; reversing it is a reveal.
- **Camera as transition**: fly through a gap, a letter, or a portal into the next scene.
- **Virtual rig**: camera parented to a null so move, shake, and orientation live on separate layers.
- **Viewport as camera (UI)**: zooming into a card to open it preserves spatial continuity.
- **Camera hold**: after a move, rest 0.5-1 s before the next move so the eye settles.
