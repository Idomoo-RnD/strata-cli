# The production workflow — in full

A phase map, not a requirement to load the entire library. Run `strata route BRIEF.md --phase <phase>`
and read its required-now list. Feature dependencies become required before that feature is used.
Lookup lists remain optional. [Design contract](make/design-contract.md) owns intent, hold
classification, diagnostic confidence and approval order; [delivery](make/deliver.md) owns done.

## Contents

- [The four checks that change what the job is](make/before-you-start.md#the-four-checks-that-change-what-the-job-is)
- [Routing — which references matter for this brief](make/before-you-start.md#routing--which-references-matter-for-this-brief)
- [1. Assets](make/plan.md#1-assets)
- [2. Storyboard and sign-off](make/plan.md#2-storyboard-and-sign-off)
- [2½. Style](make/plan.md#2½-style)
- [3. Scene JSON](make/author.md#3-scene-json)
- [4. Preview](make/author.md#4-preview)
- [5. Validate and compile](make/author.md#5-validate-and-compile)
- [6. Render](make/deliver.md#6-render)
- [7. Review the rendered MP4](make/deliver.md#7-review-the-rendered-mp4)
- [Definition of Done](make/deliver.md#definition-of-done)

## Phase gates

| Phase | Work | Exit |
|---|---|---|
| intake | source material, preferences, delivery, permissions | known constraints and explicit unknowns |
| direction | provisional beats, subject-specific concept, layout | concept approval, prototype scope |
| feasibility | styleframe/probe JSON, selected engine capabilities | style/timing approval and risks understood |
| authoring | production assets, blocking → primary → secondary → finish | validated scene and targeted evidence |
| review | candidate MP4, visual/audio evidence, keep/fix list | acceptance or scoped revision |
| delivery | final regression check, source/artifact handoff | DoD met or explicitly incomplete preview |

Steps 1–2 below are interleaved: inventory and provisional beats precede final asset generation.
The old section numbers remain for links; they do not require buying assets before approving an idea.
A mechanical edit skips design stages and follows [video-editing.md](shoot/video-editing.md).

Unattended does not mean unsupervised publication or unlimited spend. Poll long tasks in the same
turn rather than ending on a promise; stop for a real permission/budget blocker and report it.
