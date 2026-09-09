# Round-tripping and raw VASCO

Opening an existing `.idm`, rebinding its assets, and dropping to the full VASCO surface when the compact format has no sugar for something.

Part of the scene-format reference — the index, and every other part, is in [format.md](../format.md).

## Unpacking and repacking an existing `.idm`

```bash
strata inspect film.idm --assets un/ -o un/doc.json   # -> every asset + the VASCO doc
#   ...edit un/doc.json, or swap a file in un/...
strata repack un/doc.json -o film2.idm                # -> re-encoded .idm
```

`inspect --assets` writes **every** asset id-prefixed (`00_clip.mp4`, `01_logo.png`, …) plus
an `assets.json` manifest mapping `asset_id -> file`. `repack` rebinds **by id** using that
manifest, so swapping a file on disk is enough to swap the asset. Asset URIs resolve against
the document's own directory (or `--assets <dir>`), so an unpacked folder repacks as-is.
*Verified:* a 6-asset scene (video + 3 images + font + audio) unpacked byte-identical,
survived an edit, repacked, and rendered.

⚠️ **Repack is an escape hatch, not an editing workflow.** A VASCO document is **baked
output**: `{"t":0,"v":1,"ease":"outExpo"}` has already become ~100 per-frame 4×4 matrices,
`box`+`position` are folded into one transform, `font`/`src` are integer ids. *Measured:* a
1,880-byte scene compiles to a 69,475-byte VASCO doc with 3,575 baked animation numbers.
So **text edits and asset swaps are fine; motion is not hand-editable**, and you lose
`validate`'s glyph/overlap/position warnings and the tagging flow. **If the source scene JSON
exists, edit that and recompile — always.** `compile` refuses a VASCO document rather than
silently writing an empty `.idm`.

### ⛔ Two assets with IDENTICAL BYTES crash the exporter (error 3000)

*Measured by bisection:* a 6-asset scene where two assets held the same bytes under different
filenames failed export every time with `error_code 3000 "Scene exporter error"` — which
names nothing and reads like a corrupt scene. The same 6 assets with all-distinct content
exported fine. **The compiler now dedupes assets by content hash**, so the same file reused
under two names collapses to one id — the crash is gone and the `.idm` shrinks (measured:
4,189,841 → 3,812,502 bytes). Worth knowing when reading someone else's `.idm`, or if you
ever build one outside this CLI. The other three causes of the same code:
[traps.md](../traps.md#error-3000-is-a-generic-exporter-code).

### ⚠️ The encoder stores only an asset's BASENAME

`./a/logo.png` and `./b/logo.png` both land as `logo.png`. The `.idm` is fine — bytes and ids
stay distinct — but `idm2vasco` returns assets keyed by that name, so on extraction one
file's **bytes are unrecoverable** (*measured:* 2 assets in, 1 file out, silently). The
compiler now stages a copy under a unique name (`logo_2.png`) so every `.idm` we produce is
fully unpackable, and `inspect` warns loudly if it meets a third-party `.idm` that has this
problem.

## Raw VASCO passthrough

Any layer/comp key not consumed by the sugar above is copied **verbatim** into the compiled VASCO — useful for real VASCO properties the sugar doesn't cover, e.g. `is_3d`, `motion_blur`, `placeholder`, `offset_frame`, `track_matte`, `playback_mode`, `baseline`, `field_of_view`, `shutter_angle`.

⚠️ **This is the #1 source of compile errors.** The VASCO schema is **strict (`additionalProperties: false`)**, so passthrough only works for keys that are *genuinely* VASCO properties. An invented or mistyped key — `z`/`zIndex`, `x`/`y`, `width`/`height` on a layer, `comment`, `id`, `label`, `radius` **on a layer** (it is valid only inside a mask `rect`), `src` on a non-media layer, `font`/`size`/`text` on a non-text layer — is passed through and then **rejected**, failing the compile with `unknown key "…"`. This holds inside masks too, so a typo like `feathr` is named rather than silently ignored. Stick to documented sugar or real VASCO properties.

Each layer type allows only a fixed set of properties (run `strata schema` for the authoritative list). The common ones:
- **all visual layers:** `bounds`/`box`, `anchor_point`, `transform` (via position/scale/rotation/anchor), `opacity`, `color`, `blend_mode`, `mask_id`, `effect_ids`, `animations`, `motion_blur`, `is_3d`, `first_frame`, `num_of_frames`, `offset_frame`, `placeholder`, `track_matte`, `name`, `type`, `visible`
- **text adds:** `font_id`/`font`, `font_size`/`size`, `text`, `alignment`/`align`, `tracking`, `leading`, `breakline`, `shrink`, `min_font_size`, `rtl`, `ellipsis`, `styles`, `animators`, `baseline`
- **media adds:** `asset_id`/`src`, `playback_mode`/`loop`; **camera:** `field_of_view`/`fov`

Verify before encoding: `strata validate scene.json` (offline schema check — names any bad key), or `compile --vasco` / `validate --print` to see the generated VASCO.
