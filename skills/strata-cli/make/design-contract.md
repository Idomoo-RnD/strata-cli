# Design contract — intent, evidence, holds and approvals

Canonical craft/workflow policy. Other pages may suggest techniques; they do not override this
contract or the final acceptance policy in [review.md](../qa/review.md). Keep these fields in
BRIEF.md/storyboard rather than maintaining another duplicate document.

## Intent before activity

Write the audience, viewing context, viewer promise and one-sentence thesis. Record approved copy,
claims and brand atoms. Identify the signature idea that belongs to this subject. Flat graphic,
photographic, illustrative and maximalist treatments are all valid; judge against the chosen one.
Every move serves reveal, emphasis, continuity, transition, rhythm or characterization. Removing
an unnecessary move can be the strongest revision.

## A compact contract

| Field | Record |
|---|---|
| Intent | viewer promise, thesis, signature, approved claims/copy |
| Hierarchy | focal order per beat, negative space, type hierarchy at rest and in motion |
| Motion | frame rate, timing in frames, named curves, material/overshoot policy, still elements |
| Editorial | beat lengths and jobs, camera/transition rules, sound motif, reason for each cut |
| Holds | locked or live, duration, communication job, expected evidence |
| Implementation | technique → native/sugar/approximation/external route; exact recipe path |
| Assets | path/URL, provenance, permission, duration/fps/dimensions/alpha, risk |
| Acceptance | semantic success criteria, delivery constraints, diagnostics and confidence |
| State | approvals received, prototypes permitted, spend so far, blocker and next gate |

## Four diagnostic lanes — not four fabricated measurements

The historical phrase “four numbers” means shot-length range, energy, stillness and loudness.
Record the range and rhythm the information needs; shortest/longest shots each need a job, not
a target ratio. For each diagnostic use `measured / estimated / inferred / uncalibrated / N/A`,
with source, time window, scale and limitations. Uncalibrated is valid until a reference/probe
exists. No reference means no obligation to invent a measurement or spend a render just to get one.

Energy and stillness depend on image area, grain, contrast, grade and sampling. A film reference
is not a numerical target for clean vector graphics. Inspect local motion and actual frames;
never add texture merely to make a metric pass. [review.md](../qa/review.md#7-comparing-against-the-declared-position)
defines the metrics and their role. Runtime, destination audio spec and legally required copy
are delivery constraints, not proxies. A silent piece has no LUFS target to hit.

## Hold classification

| Class | How to judge it | Action |
|---|---|---|
| Intentional locked hold | Same image by design; readable, purposeful, duration earned | May pass, including a flat end card or legal hold |
| Intentional live hold | The promised subject/camera/type/material motion is visible and motivated | Inspect a consecutive-frame strip; fix absent or distracting motion |
| Media exhaustion | A clip runs out before its slot/trim-in ends | Fix source coverage or edit; not excused as an intentional hold |
| Unintended stop | Animation ends before its intended action completes | Repair timing or construction |

A detector reports low change; it does not know intent. Classify it in the review with timecodes.
Do not reclassify an accidental failure after the fact to avoid a fix. A deliberate change to the
plan is an explicit approval/decision, not a silent lowering of the bar. Shortening a hold is valid
when information and intent permit it; extending it is valid when reading requires it.

## Approvals without a circular dependency

1. Intake → provisional beats/asset inventory. Identify unknowns and constraints.
2. Concept approval → direction and proposed scope; no final clip wave yet.
3. Permitted prototype JSON → styleframes and highest-risk tests. Agree what may be built/spent.
4. Style/timing approval → commit to production assets and complete scene authoring.
5. Candidate review → consolidated fixes, final regression gate, delivery.

A supplied approved storyboard can satisfy stages already decided; do not ask the client to
approve it again just to obey ceremony. Mechanical edits need source, operation and delivery spec,
not three concepts. Scale the direction pass to the task. Unattended runs decide creative unknowns
within scope and record them; they cannot infer publication consent or a new spending allowance.

## Budget is not acceptance

Default allowance: up to two full renders, not exactly two. Track generations, snapshots, isolated
probes and full renders separately, including duration, approval and outcome. A snapshot leaves a
library entry. The user's library choice is required before it as well as before a full render.
Use the lowest-cost check that answers the actual risk; a wireframe cannot prove glyphs or audio.

Ship a first candidate that passes. Every later candidate verifies previous fixes and checks for
new blockers. Once allowance is exhausted, stop cloud spending, explain the remaining defect and
ask for a scoped probe/revision budget, or deliver a clearly marked incomplete preview. Never
label a defective video final. Details: [delivery](deliver.md), [review](../qa/review.md#6-budget-and-final-regression-gate).

## Conflicts and evidence

Engine facts → exact implementation and reproduced probes; craft defaults → reason and scope;
privacy → [upload policy](../shoot/upload.md); data truth → [personalization](../engine/personalization.md).
Record deviations and uncertainty. If tools cannot inspect sound or video directly, say what was
measured, which strips/frames were actually opened and what needs human review. No invented viewing,
no invented measurement, no “premium” approval based solely on feature count.
