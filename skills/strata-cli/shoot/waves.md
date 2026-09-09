# Generate in waves — when generation is the chosen treatment

Part of [assets.md](../assets.md). Inventory/provisional beats and concept approval precede final
asset waves. Prototype assets can be authorized separately; [design contract](../make/design-contract.md).

## Generate in WAVES, not in sequence — parallel is the default

Independent approved work can run concurrently; dependents wait for verified inputs. A prior
10-scene run took roughly 10 minutes in waves versus 60 serially. This is a scheduling example,
not a guarantee of unlimited service concurrency or a license to spend without approval.

1. References/sheets, approved independent plates, TTS and music that the direction actually needs.
2. Clips whose reference inputs now exist and passed their checks.
3. Continuations, mattes and dependent edits after the originating clip is verified.

While waiting, author unblocked layout and timing work. Foreground or poll jobs in the same turn;
backgrounding alone does not make an agent resume. Keep unique, versioned filenames for concurrent
cloud jobs. A previous collision test submitted five `scene.json` uploads to one library and failed
with exporter 3000; distinct names avoided those collisions. See [traps](../traps.md).

Before a result feeds another wave, check identity, slot duration/trim-in, fps, required audio,
movement and alpha. Preserve the source and existing hosted URL. Record failures, retries and
actual spend in the ledger; don't silently repeat expensive generation for a cosmetic note.

## Choose still, layer animation or footage

A photograph need not become a clip automatically. Choose a locked hold for purposeful reading,
layer animation for a graphic/collage treatment, or footage when the subject must move materially.
A personalized still slot remains replaceable as an image; it may be locked or layer-animated.

| Need | Route |
|---|---|
| Framing fixed, photographed motion needed | video with approved first frame |
| Identity fixed, angles free | reference-driven video |
| No exact source image needed | text-to-video, without generating a disposable still first |
| Precise diagram or icon | scene geometry or approved vector/raster asset |
| Deliberate still/collage | image layers and only the motion the direction requires |

Use the correct generation mode; frames and references cannot mix. Old fastvideo is an explicit
choice, not a mandatory fallback. Check every scene `src` against the approved media decision:
pointing at a leftover still is wrong if the shot requires the video, but the existence of an
unused generated clip does not force it into the cut. Publication follows [upload.md](upload.md).
