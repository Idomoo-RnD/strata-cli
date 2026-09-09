# Handing the layout to the user — `strata studio`

When to stop guessing coordinates and let the user place things, and what comes back.

Part of the frame-layouts reference — the index, and every other part, is in [layouts.md](../layouts.md).

## Letting the user lay it out — `strata studio`
**Every time** a layout is shown (wireframe sketch, options, or a `preview` grid), offer it:
*"want to lay it out yourself in the studio, or shall I go with this?"*

```bash
strata studio --width 1080 --height 1920      # vertical — NOT 16:9 by default
strata studio --width 1080 --height 1350      # 4:5 feed post
strata studio --width 1080 --height 1080      # square
strata studio scene.json                      # re-open a scene to nudge its boxes
```
The studio fits any aspect to the window and snaps to the same 12-column grid used here.

## Hand the layout to the user — `strata studio`

`strata studio` opens a local browser designer (offline, 127.0.0.1) where they drag and annotate named areas per scene, set roles/colours/notes, and press Save. I then **author from those boxes as given**, not re-inventing the layout. Launch it at the user's real canvas (`strata studio --width 1080 --height 1920`, or `--width 1080 --height 1080` for square) or on an existing `scene.json`; it is **not** 16:9 unless the piece is. Re-opening a scene I already wrote (`strata studio scene.json`) lets them nudge my boxes rather than describe the fix in words. It runs a local server and waits for Save, so I only launch it once they say yes, never in an automated run.
