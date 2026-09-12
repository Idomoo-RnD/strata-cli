# Steps 1–2½ — assets, storyboard, sign-off, style

Part of [workflow.md](../workflow.md). These steps interleave: inventory → provisional beats →
concept → permitted prototypes → production approval → final asset waves. Prototype JSON is
allowed before production sign-off; the [design contract](design-contract.md) owns that distinction.

## 1. Assets

Inventory existing material before generating replacements. Preserve approved marks, typography,
copy and real data. Choose the construction by intent: photographed motion → footage; a precise
diagram → scene geometry; a static photograph or keyed still → image/layer animation when appropriate.
No image must become AI video simply because this is a motion-design job.

Decide clip lengths from provisional storyboard slots and trim-ins. Cover the entire slot with
source media, preferably with a little editing margin; inspect every delivered clip before committing
it. Use [video-generation.md](../video-generation.md), [traps.md](../traps.md) and the asset's own
metadata. A static result is a failure if the approved shot required subject movement; it is not a
failure merely because its pixel-change metric is low.

For a recurring person/product/set, establish the [production bible](production-bible.md) before
final clip generation. Test the highest-risk identity, lip-sync, matte or camera moment early.
Generate approved independent assets in waves; clips follow their references, continuations follow
clips. Work on unblocked authoring while jobs run. Preserve unique versioned filenames for parallel
cloud jobs so their uploads do not collide.

Record for each asset: source/license/consent, local file and existing URL, dimensions, fps, duration,
audio and alpha. Use the [upload policy](../shoot/upload.md) before any publication, including
caption re-hosting. Animation budget does not imply consent to upload customer material.

A full-frame plate or opaque framed video may stay MP4. Transparent moving footage uses `.jet`;
a still cut-out can use a PNG image. The [alpha decision](../shoot/alpha.md) explains which route
fits the subject. Font files must be available for actual copy and scripts.

## 2. Storyboard and sign-off

Present a readable direction before buying its final assets. Include:

- **Intent:** title, viewer promise, thesis, approved copy/claims and subject-specific signature.
- **Style:** palette, type scale, material/flatness, lighting if spatial, reference-control map.
- **Beat plan:** `Time | Information / focal order | Visual / motion | Voice | Sound`.
  Each beat earns its length; name shortest/longest jobs without targeting a ratio.
- **Hold plan:** locked or live, duration and purpose. A legal hold may be motionless.
- **Key layouts:** sketches from [layouts](../layouts.md), with alternatives only where useful.
- **Creative commitments:** the typographic idea, the named layout beyond title-over-media, and the
  rhythmic spine — one line each, from [design-contract](design-contract.md). The bed is generated
  and `strata beats` has run before the beat plan is timed.
- **Motion system:** named curves, frame timing, material logic, camera/transition policy,
  still elements and forbidden moves. Read [motion-design](../craft/motion-design.md).
- **Acceptance:** semantic criteria plus delivery spec. The four diagnostic lanes carry provenance
  and confidence; uncalibrated is allowed. Do not invent reference numbers to fill a blank.
- **Production state:** approvals, allowed prototypes, library, asset/probe/full-render budget.

For premium work, read [director.md](../direct/director.md): genuinely different concepts and
styleframes help the user decide. Build permitted styleframes as prototype scenes, with cloud
snapshot costs and library entries disclosed. Approve their style/timing before final production.
For a small edit, one explicit operation/spec is enough. Supplied approved material is executed.

Unattended: make creative decisions within the approved scope, record them in `decisions.md`, and
continue. Library comes from a user choice (`--library`, project `.idm-library`, or preference).
No available library/permission is a blocker, not permission to pick. Foreground or poll long jobs
in the same turn; ending on “I'll render next” does not resume an agent.

Offer [studio](../craft/studio.md) if the user wants to manipulate layout, and launch only on yes.
It waits for a human Save; do not make an unattended job depend on it.

## 2½. Style

[video-styles.md](../craft/video-styles.md) offers technique vocabulary, not mandatory costumes.
The approved brand controls atoms; the direction controls composition and motion. Three palettes
on the same timing are not three concepts. Equally, three good concepts need not span an arbitrary
energy ratio. [Case studies](../craft/case-studies.md) show how to judge implementation, with proof
levels labeled rather than implied.
