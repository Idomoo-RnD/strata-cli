# Steps 6–7 — render, review, and the Definition of Done

Part of [workflow.md](../workflow.md). A budget is a spending boundary, not acceptance.
The canonical review policy is [review.md](../qa/review.md); planning is in
[design-contract.md](design-contract.md).

## 6. Render

```bash
strata render scene_v1.json --library <approved-id> -o scene_v1.mp4
```

- Ask for the library before any cloud render or snapshot unless the user already chose one.
  `--library`, project `.idm-library`, then recorded preference can supply that choice. Never
  select an arbitrary library. Snapshots also upload a persistent library entry.
- Preflight locally: preview key beats, validate, glyph coverage, source duration/fps, actual
  asset paths. Use targeted snapshots/probes for things a wireframe cannot prove.
- Keep a ledger for generations, snapshots, probes and full renders. Default allowance is **up
  to two full renders**, not a required count or automatic approval of the second candidate.
- Consolidate must-fixes, preserve the keep list and version the revision. Run long tasks in the
  foreground or poll them in the same turn. A background process does not wake the agent.
- If the user asks for catalog/template tagging, read [tagging.md](../engine/tagging.md) and pass
  `--tags` to compile/render. One-off work does not need tags by default.

## 7. Review the rendered MP4

```bash
strata review scene_v1.mp4 --scene scene_v1.json -o review_v1/
```

Read the report and actually open the relevant frames/strips. Evidence includes the hook, hero,
end card, busiest motion, longest hold, cuts and settles. See [review.md](../qa/review.md) for
phone-size, muted, motion-strip and audio checks and their blind spots.

A first render may ship if it passes. Every later candidate verifies previous fixes **and** gets
a regression check for copy, claims, brand, legibility, audio, source exhaustion, continuity,
data and delivery. A new blocker is still a blocker. If the allowance is exhausted, stop spending,
explain the remaining issue, and request a scoped probe/revision budget or hand over an explicitly
incomplete preview. Never call that preview final.

## Definition of Done

- The approved message, claims and viewer promise are delivered; every beat and move has a job.
  The intended creative bar is met, not merely a clean compile or a count of advanced features.
- Copy, logo, fonts/colors and legally required material match the approved brief. Text is
  readable at destination size and through motion; safe areas reflect the actual platform.
- `validate` is clean or every warning has an explicit evidence-based disposition.
- Layer names are unique across every comp. No ignored duplicate-rename warning changes the
  personalization contract. Verify real placeholder keys before integration.
- Every clip covers its slot and trim-in, with correct fps/audio/alpha. Intentional locked holds
  may pass; source exhaustion and unintended animation stops do not. Live holds show the motion
  promised in the plan. Review reports classify detector findings rather than blindly obey them.
- The final MP4 meets approved duration, dimensions, aspect, fps, codec and audio requirements;
  first/last frames and captions are checked. A silent brief need not acquire music or a LUFS target.
- Review ran on the final candidate. Every blocking finding is resolved; accepted nonblocking
  trade-offs are recorded with reasons. No budget rule overrides this gate.
- Numeric diagnostics include provenance/confidence and comparison limits. Uncalibrated aesthetic
  targets are not fabricated; valid references are compared for relevant relationships.
- Personalized variants are proved on edge rows (long/short text, scripts, min/max/empty data).
  Shape-changing charts use the strategy in [personalization.md](../engine/personalization.md).
- BRIEF.md and decisions record approvals, permissions, actual inspected evidence, remaining
  limitations and spend. Deliver versioned MP4, source scene/IDM as agreed, assets/contract and
  review findings. Say explicitly if human listening or visual inspection remains outstanding.
- The library was the user's choice; privacy policy was followed. Preferences record repeat
  answers, not consent shortcuts. Save a reusable recipe when the project is a series.

If any required proof remains unavailable, report **incomplete / needs review**, not “done.”
