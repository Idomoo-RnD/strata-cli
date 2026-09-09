# Scene — the document envelope

The top level of a scene: canvas, fps, duration, versioning, and how colours are written.

Part of the scene-format reference — the index, and every other part, is in [format.md](../format.md).

## Scene

```json
{
  "width": 1280, "height": 720,      // max 1920 each
  "fps": 25,                          // 1..120, default 25
  "duration": 4,                      // seconds → num_of_frames (or set "num_of_frames")
  "name": "main",
  "layers": [ ... ],                  // bottom-first: first layer is the background
  "comps": { "card": { /* same shape as scene */ } }   // optional sub-compositions
}
```

Passthrough at comp level: `shutter_angle` (0–2, default 0.5 ≈ 180°), `shutter_phase`. (`transition {start,end}` also exists on a comp; its semantics are undocumented and tagging auto-stamps it — do not set it by hand.)

**File naming — the scene's FILENAME is its identity in the cloud.** `render`/`snapshot`
upload under `basename(<scene file>)` and derive the output MP4/JPG name from it, so the
filename is what the user browses in their Idomoo library. Give every new cut its own
versioned file (`promo_v1.json`, `promo_v2.json`, `promo_final.json`) instead of
overwriting — otherwise the library fills with indistinguishable entries and each render
clobbers the last MP4. (`name` above is the *comp* name, internal — it does not label the
upload.)

### Version control — automatic, two layers *(measured 2026-08-24)*

**1. Folder history.** Every `compile`/`render` snapshots the scene JSON into
`.strata/versions/vNNN/` beside it, with an index (time, scene hash, render URL once one
exists). Identical content never makes a new version — the hash is over the *canonical*
JSON, so whitespace churn is not an edit. `strata versions <scene>` lists them;
`strata revert <scene> --to N` restores one (the current state is snapshotted first, so
revert can never lose anything; `-o other.json` branches instead of replacing).

**2. Embedded stamp.** Every compiled `.idm` carries a `strata.meta.ttf` asset — the same
disguise as the tag manifest — holding `{version, parent, created, scene_hash, tool}`.
*Measured:* it survives the Idomoo exporter (uploads and renders normally), round-trips
through `idm2vasco`, and `strata inspect` prints it — so a bare `.idm` received from anyone
identifies its own version and ancestry. The filename rule above remains the **library's**
identity (Idomoo shows the filename); the stamp is the **file's** identity wherever it
travels. Two `.idm`s with the same `scene_hash` are the same cut regardless of their
numbers — numbering is per-folder.

## Colors

Hex anywhere a color is expected: `#rgb`, `#rrggbb`, `#rrggbbaa`. Layer/text-style colors are RGB (alpha dropped); effect and animator colors keep alpha. Raw `[r,g,b(,a)]` arrays (0..1 floats) also accepted.
