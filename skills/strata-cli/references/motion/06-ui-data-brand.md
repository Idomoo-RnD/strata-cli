# UI motion, data motion, brand motion systems

> **This is the closest file in the atlas to what Idomoo actually sells.** A brand document
> overrides every token suggestion here - [brand.md](../brand.md) - and the personalized-data
> craft below is executed with layer-name keys and batch rows:
> [personalization.md](../personalization.md).

Contents: 1 UI and product motion · 2 Data and infographic motion · 3 Brand motion systems

## 1. UI and product motion
- **Micro-interaction anatomy**: trigger → rules → feedback → loop or mode; every interactive motion has all four.
- **State transitions**: hover, press, focus, active, disabled; each gets a small motion (2-10% scale, color, shadow).
- **View enter and exit**: push, slide, fade-through, shared axis, container transform.
- **Shared element transition**: an element persists from list to detail; the strongest spatial continuity tool.
- **Container transform**: a card expands into a full screen and collapses back.
- **Navigation metaphors**: stack (push and pop), tabs (lateral), modals (rise from bottom), sheets (partial rise), drawers (side).
- **Spatial model**: every screen has a place; back returns the way you came.
- **Gesture-driven motion**: motion tracks the finger 1:1, then settles with a spring on release.
- **Interruptible and redirectable**: a new tap redirects mid-animation without restart.
- **Feedback**: ripple from the touch point, pulse, color flash, haptic pairing.
- **Loaders**: spinner (indeterminate), progress bar (determinate), skeleton shimmer (structure first), dots, branded loader.
- **Perceived performance**: skeletons and optimistic updates make waits feel shorter.
- **Success and error**: a check draws on success; a shake (3 oscillations, ~300 ms) on error; color confirms.
- **Toggles and switches**: the thumb slides with a spring; the track color crossfades.
- **Expand and collapse**: height animates with content fade; accordions stagger.
- **List changes (FLIP)**: insert, remove, reorder with object constancy; removed items collapse, neighbors slide.
- **Drag and drop**: lift (scale plus shadow), follow, drop (settle), neighbors reflow.
- **Tooltip, toast, snackbar**: quick entrance (150-250 ms), auto-dismiss, faster exit.
- **Carousel and pager**: snap points, edge resistance, a peek of the next item.
- **Pull to refresh**: elastic stretch, spinner, release settle.
- **Scroll-linked motion**: progress-driven animation (scrollytelling), sticky sections, parallax on scroll, reveal on entering the viewport.
- **Condensing headers**: collapse on scroll down, expand on scroll up.
- **Onboarding sequences**: illustration plus copy beats with one consistent rhythm.
- **Empty states**: a gentle loop that invites action.
- **Duration scale (Material)**: short 50-200 ms, medium 250-400 ms, long 450-700 ms; emphasized easing for hero moves, standard for the rest.
- **Easing tokens**: standard (0.2, 0, 0, 1); decelerate for entrances; accelerate for exits; emphasized for expressive moments.
- **UI choreography**: container first, content staggered 20-40 ms; exits reverse and run faster.
- **Fluid interface rules (Apple)**: instant response, interruptible, gesture-faithful, continuous, spatially consistent.
- **Reduced motion**: honor the OS preference; replace movement with fades, keep durations short, never remove meaning.
- **Motion tokens**: duration, easing, distance, stagger, and scale as named design tokens; one source for product and video.
- **Focus and cursor motion**: focus rings grow in; cursor-following highlights.
- **Game UI juice**: screen shake, hit flash, particles, number pops, squash on press; scale to context.
- **Idle motion**: subtle breathing so screens don't feel dead; under 2% movement.
- **Progressive disclosure motion**: details unfold from the element that summoned them.
- **Undo affordance**: reversal animates back along the same path so the user trusts the undo.

## 2. Data and infographic motion
- **Count-up**: ease-out, 1-2 s, decelerating last digits; tabular figures.
- **Progress fills**: bars and rings fill from zero with ease-out; goal markers appear first.
- **Bar chart build**: sequential (story) vs simultaneous (comparison); grow from the baseline; label after the bar.
- **Line chart draw**: path trim left to right; the area fills after the line; dots pop on key points.
- **Pie and donut sweep**: slices sweep clockwise from 12 o'clock, largest first or in story order.
- **Scatter emerge**: points scale in with a slight stagger; highlight clusters after.
- **Map motion**: pins drop with a small bounce; routes draw; regions fill; zoom from country to city.
- **Heatmap reveal**: fade by magnitude or wipe by time.
- **Table stagger**: rows enter top to bottom 40-60 ms apart; highlight the row that matters.
- **Sorting and filtering**: object constancy; bars slide to new positions rather than redrawing.
- **Chart type morph**: bars become a line, a pie becomes a bar; continuity between views.
- **Annotation**: a leader line draws, then the label fades in; everything else dims.
- **Focus and dim**: non-focus data drops to 20-30% opacity.
- **Threshold crossing**: color or weight flips as a value passes a goal line.
- **Before and after**: split, slider wipe, or crossfade with a label.
- **Timeline scrub**: a playhead travels and values update.
- **Pictogram counts**: icons fill rows to show quantities.
- **Magnitude-aware timing**: bigger changes take a little longer; tiny changes snap.
- **Honesty rules**: no truncated axes without a marker, no 3D tilt that distorts, no animation that implies growth that didn't happen.
- **Legibility floors**: numbers at least 3-4% of frame height on mobile; two decimals maximum.
- **Personalized data moments**: the viewer's own name, number, or chart is the hero; longest entrance, most hold, cleanest frame.
- **Units and format**: currency symbols, separators, and localized formats fixed before animating.
- **Axis animation**: axes and gridlines draw in before data; they rescale with a short ease when ranges change.
- **Comparison pairing**: compared values move together (same stagger, same easing) so the eye reads the relationship.

## 3. Brand motion systems
- **Motion principles**: three to five adjectives describing how the brand moves (decisive, warm, precise); every decision is tested against them.
- **Signature move**: one recognizable gesture (a slide on the logo's angle, a bounce, a lift) used across touchpoints.
- **Motion tokens**: brand durations, easings, staggers, distances, rotations as named values; extend design tokens (DTCG-style) with motion.
- **Energy level**: calm, balanced, or energetic; sets default duration and easing strength.
- **Motion scale**: micro (UI), functional (explainer), expressive (campaign), hero (logo and title).
- **Logo reveal families**: build (parts assemble), draw (stroke), morph (shape becomes logo), mask (reveal through shape), particle, 3D turn, reveal from brand shape, type-only.
- **Logo sting (bumper)**: 2-4 s; entrance, hold, exit; paired with a sound logo.
- **End frame and CTA system**: fixed layout, timing, and hold (2-3 s); the most-reused asset.
- **Lower thirds system**: entrance, hold, exit rules; safe area; name vs title hierarchy.
- **Transitions from logo geometry**: wipes and masks derived from the mark's shapes and angles.
- **Shape language**: radius, angles, stroke weights, and how they move (rigid vs elastic).
- **Color motion rules**: which colors animate, in what order, how backgrounds swap.
- **Typography motion rules**: one entrance, one exit, one emphasis per brand.
- **Sonic pairing**: the sound logo and the motion logo end on the same frame.
- **Context modes**: functional (quiet, fast) vs expressive (slow, rich); a brand has both.
- **Do and don't rules**: no overshoot on the wordmark, no spinning the logo, minimum hold, etc.
- **Consistency vs novelty**: 80% system, 20% campaign surprise.
- **Accessibility commitments**: a reduced-motion variant, flash limits, captions.
- **Motion guidelines document**: principles, tokens, examples, do and don't, templates; the source of truth for agents and humans.
- **Brand motion as a verb**: describe the brand's motion as an action ("it lifts", "it unfolds", "it snaps") so everyone animates the same way.
- **Template resilience**: every branded template survives the longest name, the biggest number, and the missing value.
- **Brand personality sliders**: energy, weight, precision, warmth, density, playfulness; set once, inherited everywhere.
