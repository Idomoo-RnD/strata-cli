# Before you start — the four checks and the routing

Part of [workflow.md](../workflow.md). Start with the task, permissions and intent—not a list of
effects to include. [Design contract](design-contract.md) defines approvals and diagnostic confidence.

## The four checks that change what the job is

- **Material supplied?** Read [intake.md](intake.md). Transcribe the supplied script/storyboard and
  constraints, execute approved decisions and flag missing assets. Do not redesign by default.
  Recurring subjects/sets need [production-bible.md](production-bible.md) before final generation.
- **Mechanical edit or design?** Trim/join/crop/retime/mute alone uses
  [video-editing.md](../shoot/video-editing.md), not a new scene or concept presentation.
  Scene graphics on footage need the design and relevant engine packets too. Use `--mode` when
  wording is ambiguous; “trim a path” is not the same task as “trim a clip.”
- **Brand?** Read `.brand/brand.md` if present. If material exists but a reusable document does
  not, offer [brand capture](../brand/brand.md). Keep unknown atoms explicit; never fabricate claims.
- **Figma?** Read [figma.md](../brand/figma.md). It is a layout source: coordinate conversion,
  actual font files, exported assets and naming need inspection before motion is authored.

When layout/motion are yours, the direction packet opens the design contract, anti-slop test,
grid and motion language. Read named feature dependencies before use. Before any cloud operation,
confirm library, data permission and available budget. For public hosting or caption re-hosting,
read [upload.md](../shoot/upload.md); unattended mode does not imply informed publication consent.

## Routing — which references matter for this brief

```bash
strata brief init "<the request, verbatim>"
strata route BRIEF.md --phase intake
strata route BRIEF.md --phase direction
strata route BRIEF.md --phase feasibility --features text,camera
strata route BRIEF.md --phase authoring --features text,camera
strata route BRIEF.md --phase review
strata route BRIEF.md --phase delivery
```

`brief init` writes the request, reconstruction, intent, acceptance, diagnostics with confidence,
approvals and spend ledger. Fill what the current gate needs; uncalibrated/N/A is valid for a
metric. Amend the brief as direction changes rather than reconstructing it from the conversation.
Routing reads the reconstruction, not every word in the brief; asset lists and exclusions should
not accidentally become a new genre. Preserve actual negative constraints alongside that line.

Four output lists have different obligations:

| List | Obligation |
|---|---|
| `requiredNow` (`read` alias) | Open every listed page for this phase |
| `requiredForCapabilities` | Open before using each listed technique; included in authoring/feasibility reads |
| `lookup` | Open only for a specific question; not an unread-work backlog |
| `beforeDelivery` | Read and satisfy before declaring completion |

Default phase is direction. Use `--mode edit|design|generation|template` for task classification,
and `--features text,camera,masks,audio,generation,presenter,captions,data,alpha,tracking` for the
techniques actually chosen. CLI help lists options. The JS router also accepts a structured spec
with text/mode/phase/features/includeKinds/excludeKinds; exact kind names come from the route table.
Free-text negation remains a heuristic—check the resulting classification against the request.

`--skill-dir <path>` routes against that copy of the skill; otherwise the CLI resolves a local or
installed copy. Open printed paths as-is. A format/workflow index is not a completed syntax/DoD
read; phase packets explicitly include the relevant leaf pages. Do not read the whole catalogue
to prove diligence. Search [INDEX.md](../INDEX.md) when a particular question arises.

## Preferences and continuity

Before repeating questions, use `strata prefs list`. Record recurring library/aspect/fps/voice/
brand answers with `prefs set`; they fill future briefs. A saved preference is not publication
consent or approval of a new concept. For a series, `recipe save/use` carries the prior brief,
brand and decisions; read them, then approve the new piece's changes. Credentials, personal data
and private media do not belong in reusable skill/recipe examples.

Unattended jobs may decide creative details within approved scope and record them. A missing
library/permission/budget is a real blocker. Poll long jobs in the same turn; do not end on an
unfulfilled promise that assumes a background process will wake the agent.
