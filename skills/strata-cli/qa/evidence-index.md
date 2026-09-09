# Evidence index — canonical claims and proof levels

A number near “measured” does not prove a claim. This register identifies corrected contracts,
owners and reproducible checks. Existing render measurements in engine pages remain historical
observations with their stated scope; this index does not retroactively certify all of them.

## Proof levels

- **Offline regression:** reproduced against the local implementation, not the live cloud.
- **Historical render:** an existing recorded observation; retain its scope, do not claim a new run.
- **Cloud proof pending:** needs an approved library/budget and actual rendered artifacts.
- **Craft policy:** reasoned default or acceptance policy, not an engine measurement.

## Registered contracts

| ID | Canonical owner | Claim / policy | Reproduction or acceptance test | Scope |
|---|---|---|---|---|
| DATA-001 | [personalization](../engine/personalization.md#chart-strategy--choose-before-authoring) | chart emission bakes geometry; substitution changes placeholder values | `node test/skill-v3.mjs`, fixed max 100/height 200: 25 emits 50px, 75 emits 150px; label substitution alone never rebuilds shapes | offline; actual batch still needs two-row cloud proof |
| PRIVACY-001 | [upload](../shoot/upload.md) | no public publication of customer/sensitive material; consent gate before direct/caption re-hosting | `node test/skill-v3.mjs`, refused local and non-S3 caption input without consent; compatible existing URL passes without upload | offline guard + policy; does not classify sensitivity automatically |
| TYPE-001 | [type recipes](../recipes/type.md) | typewriter requires intermediate hold keys, not just start/end | `node scripts/check-skill-recipes.mjs`, sampled selection and caret at intermediate frames | offline baker/compile; glyph/range rendering pending cloud proof |
| TYPE-002 | [type recipes](../recipes/type.md) | flip reveal follows square/pinned-end selection pattern | fixture schema/compile and initial selection checks; render first/middle/last in approved library | renderer-sensitive proof pending; not labeled newly verified |
| ROUTE-001 | [routing](../make/before-you-start.md) | phase-specific leaf reads, options excluded from text, typographic and inline negation handled | `node test/skill-v3.mjs`, command parser and route matrix | offline; natural-language classification remains heuristic |
| HOLD-001 | [design contract](../make/design-contract.md#hold-classification) | intentional locked/live holds differ from exhaustion/unintended stops | planning scenarios: locked legal card passes; exhausted source does not | craft policy; detector thresholds unchanged |
| REVIEW-001 | [review](review.md#6-budget-and-final-regression-gate) | budget never overrides final regression gate | revision-two missing-copy scenario must withhold final approval | craft/release policy; needs agent evaluation |
| DOC-001 | [workflow](../workflow.md) | exact paths, reachable graph, checked snippets and generated indexes | `node scripts/check-skill.mjs`; negative fixtures in `test/skill-v3.mjs` | structural checks, not semantic proof |
| EVAL-001 | [case studies](../craft/case-studies.md) | skill improvements need agent-authored outputs and blind judgments | `evals/agent/run.mjs` isolated adapter trials; renderer suite is separate | harness tests offline; creative preference trial pending |

## Adding an engine claim

Reproduce it yourself before adding it. Record CLI/codec version, minimal input/command, expected
and observed results, asset hashes where relevant, and a retained evidence path. Cloud findings
need the actual rendered frame/clip and library approval; schema/baker success is not visual proof.
Add a failing regression where possible. A policy/default instead names its reason and exceptions.
Generate echoes from canonical data where feasible; otherwise cross-reference the owner and review
all occurrences. Lint can reject stale phrases, but cannot prove every possible paraphrase agrees.
