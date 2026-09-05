# Personalized / data-driven — route

**This is the route when** the brief says personalized, per viewer, each customer, their name or stats, a chart, graph, dashboard, KPI, batch, rows. Against [explainer.md](explainer.md): values change per viewer, or one scene renders many times.

**It expects** a data contract: which fields vary, and their extremes. **It delivers** one template scene rendered per row: the *Data story / KPI* row in [blueprints.md](../blueprints.md); **personalized-template**, **data-report** or **odometer-count** in [video-styles.md](../video-styles.md).

## Settle these before the storyboard (into BRIEF.md; unattended → decide and record in decisions.md)
- Which layers vary per viewer, and the longest and shortest value each receives.
- Which data visuals are `strata chart` layers and which are swapped image files.
- Where the personal moment lands: the hero frame, early but not at the very start.
- Whether values arrive in more than one script or direction.

## Where it usually sits on the range
Toward the *film, quiet* row around the personal reveal: the viewer's own number is the hold, the cleanest frame, the longest entrance; connective beats run shorter. The brief decides; declare the four numbers from a measurement (review.md §7).

## Read, in this order
- [personalization.md](../personalization.md): layer names as the contract, `--emit-timeline`, the `render --data` batch.
- [motion/06-ui-data-brand.md](../motion/06-ui-data-brand.md): *2. Data and infographic motion*.
- [editorial/nonfiction-formats.md](../editorial/nonfiction-formats.md): *Shared factual integrity rules*.
- [traps.md](../traps.md): the personalised-visual and layer-name entries, before the first compile.

## Watch for
- a ring or gauge drawn from shapes that must change per viewer: [traps.md](../traps.md), *The traps, in full*
- two layers sharing a name in different comps: [personalization.md](../personalization.md), *The keys are LAYER NAMES*
- a Hebrew or Arabic value in a template proved only against Latin: [personalization.md](../personalization.md), *Right-to-left values*

## Done when
- `strata render --data` with the longest, shortest, empty and largest rows: nothing clips, overflows or shrinks past its floor
- `--emit-timeline` lists exactly the keys the integration will send
- `strata snapshot --data` at the reveal shows the viewer's own value, and the reveal is the longest shot, its frames moving
