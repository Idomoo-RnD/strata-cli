# Advanced features

Use this when a particular feature is needed. Advanced features are options,
not a quality requirement. Use the section that answers the current problem.

## Contents

- [Native support and approximations](#native-support-and-approximations)
- [Camera and parallax](#camera-and-parallax)
- [Tracking a surface or subject](#tracking-a-surface-or-subject)
- [Audio-driven motion](#audio-driven-motion)
- [Personalization and chart geometry](#personalization-and-chart-geometry)
- [Tagging reusable scenes](#tagging-reusable-scenes)
- [Brand, design, and reference handoff](#brand-design-and-reference-handoff)
- [Existing IDM files and raw VASCO](#existing-idm-files-and-raw-vasco)

## Native support and approximations

Strata supports transformed layers, nested compositions, text animators, masks,
track mattes, path strokes, effects, cameras, and baked animation. Footage can
carry motion or appearance that is better made elsewhere.

For a requested effect, identify what must remain editable, exact, or variable.
Choose the simplest supported construction that meets those needs:

| Effect or behavior | Useful construction |
|---|---|
| Fixed reveal window or rolling digit strip | Clipped sub-composition or static track matte |
| Written path or progress arc | Stroked shape with animated trim |
| Highlight following words | Authored graphic behind text, with matching timing |
| Surface replacement in footage | Tracked corner pin on a composition |
| Depth between flat elements | 3D layers and camera |
| RGB channel split | Separate copies with channel-selective `styles` effects |
| Physical extrusion, realistic light, complex depth of field | Suitable rendered/generated media or a deliberate layered approximation |

Do not present duplicated flat layers as true geometry, or a blur overlay as a
physical depth-of-field calculation. Use an approximation if it suits the result.
For exact scene syntax, see [scenes](scenes.md). A novel engine construction
needs a focused check before it becomes the basis of a whole piece.

## Camera and parallax

Use a camera when different depths should respond differently to movement.
For a uniform push on flat artwork, a transformed parent comp can be sufficient.

The scene camera position is an **absolute comp coordinate**, with negative Z
looking toward the scene. Center it at `[width/2, height/2, -distance]`, not
`[0, 0, -distance]`. Camera projection affects layers marked `is_3d: true`.

The existing render probes established this pinhole relationship:

```text
focal = (height / 2) / tan(fovDegrees * pi / 360)
apparentScale = focal / (layerZ - cameraZ)
```

`fov` is vertical. For a 720-high comp at 60 degrees, focal distance is about
623.54. A camera at `[640, 360, -623.54]` gives a Z=0 plane approximately its
authored size in a 1280×720 comp. Negative layer Z moves toward the camera and
enlarges it; positive Z moves away. Keep the visible geometry in front of the
camera.

Size background planes for their projected extent. A distant plane that is
only canvas-sized can expose edges. An anchor still makes `position` an absolute
anchor target; moving an anchored layer in depth should preserve its intended X/Y.
Depth does not replace the layer list's compositing order.

Use [the camera fixture](../examples/camera-parallax.json) for complete syntax.
It is an offline scene fixture; a wireframe preview does not prove camera
projection. Check a permitted real snapshot when projection is uncertain and
the rendered video when motion or edge coverage matters. Camera `motion_blur`
must be set explicitly on the camera layer; visual layers enable it by default.

## Tracking a surface or subject

```bash
strata track plate.mp4 --comp 400x400 -o surface.json
strata track plate.mp4 --point 320,240 -o point.json
```

Surface tracking outputs an `effect` containing a corner pin. Insert that effect
into a solid or comp layer's `effects`. Wrap text/images in a sub-comp with
explicit duration when they need the same pin. Point tracking outputs
`animate.position`, which represents displacement from the tracked start.

Choose the content's dimensions and place it at the tracked source coordinates.
For an anchored layer, convert displacement into absolute position by adding
the intended anchor target to each point key. Otherwise a displacement near
`[0,0]` can pull the artwork to the wrong part of the frame.

Corner-pin points use this order: upper left, upper right, lower left, lower
right. Check the actual output fields when integrating rather than rearranging
them into an assumed clockwise order.

Tracking coordinates and time belong to the footage analyzed. A subsequent
crop, resize, retime, or frame-rate change can invalidate them. Prefer tracking
the prepared source that will be used in the scene. Otherwise transform the
coordinates and timing consistently.

Inspect the track near the beginning, end, fast motion, occlusion, and lighting
changes. A score can identify uncertainty, but a plausible number does not prove
attachment. Correct failed segments, choose a more stable feature, or use a
different construction as appropriate. Tracking alone does not solve foreground
occlusion; that may need [a mask or matte](media.md#transparent-video-and-jet).

## Audio-driven motion

Use this when a property should follow the actual sound, such as a meter, pulse,
or frequency display. Analyze the accepted track:

```bash
strata beats bed.wav --fps 25 --bands 12 -o bed.beats.json
```

An animation channel may be an audio-driver object instead of keyframes. This
is an `animate` fragment for a layer:

```json
{
  "animate": {
    "scale": {
      "audio": "./bed.beats.json",
      "band": 0,
      "range": [[1, 1], [1, 3]],
      "attack": 0.02,
      "release": 0.18
    }
  }
}
```

`range` maps envelope minimum/maximum to the property's values. Use matching
value dimensions for the channel. `attack` and `release` are smoothing times
in seconds; choose them for responsiveness and stability. Setting both to zero
uses the unsmoothed envelope. `band: "auto"` assigns successive bands to repeated
copies when using the repeater.

Other band choices are `low`, `mid`, `high`, or `rms` for overall level.
`gate` suppresses low-level activity; `gain` multiplies the envelope before
clamping. A direct audio file is accepted too, but requires ffmpeg at compile
time; saved beat analysis avoids that dependency.

Audio drivers are sampled in **composition time**, not simply restarted at each
layer's `start`. Use their `offset` in seconds when the audio's placement requires
it. Match the analyzed asset to the one actually heard; an edited or retimed
track may need new analysis.

Frequency bands are normalized individually. Their displayed heights therefore
show activity within each band, not absolute loudness comparisons between bands.
Use actual meters for loudness. Onsets are not speech-word boundaries.

## Personalization and chart geometry

`render --data` replaces exported placeholders by **layer name**. Keep globally
unique, descriptive names and check the actual exported keys. An unknown key
is an error, not an instruction to invent a slot.

An illustrative row:

```json
{
  "headline": "Welcome, Sam",
  "heroImage": "https://example.invalid/approved-hero.png"
}
```

Replace the illustrative URL with an accessible, permitted asset. Supply one
object or an array of row objects; this interface does not directly read CSV.

```bash
strata validate template.json --data rows.json
strata render template.json --library "<selected-id>" --data rows.json --emit-timeline timeline.json -o variant.mp4
```

The batch uploads/exports the template once and starts a generation for each row.
An array produces numbered outputs such as `variant_1.mp4`. The emitted timeline
shows the keys and values the render API receives; this is a **real render**,
not a free discovery request.

Text replacements become text values; media replacements are URLs. Current
substitution code also handles exported audio keys, but this does not generate
new speech or establish its alignment. The shipped guidance's demonstrated
batch flow is text/media replacement. For per-viewer speech, generate the
individual performance or narration first and verify the exported audio/media
contract and timing with a representative row.

Keep independently editable content in independent slots. A headline baked into
an image cannot be replaced as text. Test long names, different scripts, missing
values, and actual font coverage. A text swap does not automatically redesign
the layout or ensure readability.

### Charts

`chart` creates authored geometry. For example:

```bash
strata chart donut --value 72 --box 100,100,300,300 scene.json
strata chart bars --data bars.json --box 100,100,900,400 --font font.ttf scene.json
strata chart line --series series.json --box 100,100,900,400 scene.json
```

`bars.json` is an array of objects such as
`[{"label":"Mon","value":42},{"label":"Tue","value":65}]`.
`series.json` is a numeric array such as `[3,7,5,9]`. Donut `--value` is a
percentage. With a scene filename, the command inserts layers; without one,
it prints the generated layers. Inspect the result and adapt its timing.

Inspect `chart --help` for the selected chart mode.
The geometry is baked when the layers are emitted. Changing `"72%"` through
`render --data` does **not** change a wedge's angle, a bar's length, or line points.

Choose one of these according to the contract:

- Rebuild the scene's geometry for each row when shapes must remain authored.
- Generate a deterministic chart image per row and replace an image slot when
  a flattened chart is acceptable.
- Use label-only replacement when geometry is intentionally unchanged.

Calculate chart images from the data; image synthesis is not a numeric plotter.
Maintain units, scales, totals, and truthful intermediate animation. A rotating
full circle does not depict a partial percentage. A masked bar can reveal a
known final length; a trimmed path can reveal a known arc or line.

Digit rolling can finish on replacement text by ending `character_offset` at
a multiple of ten. It is a digit effect, not an arithmetic count through all
intermediate values. See [text animators](scenes.md#text) and the
[path fixture](../examples/path-reveal.json) for related mechanisms.

## Tagging reusable scenes

Use tagging when the requested result is a catalog/recombination template or the
user asks for tags. Ordinary one-off videos do not need a tagging manifest.

```bash
strata validate template.json --tags manifest.json
strata compile template.json --tags manifest.json -o Light_Hero_Cta_01.idm
```

A manifest for a scene containing the named image and text slots:

```json
{
  "ae_scene_name": "Light_Hero_Cta_01",
  "description": "",
  "tags": ["media", "text", "cta"],
  "colors": [
    {"tag": "color 1", "hex": "#17324D", "role": "ink"},
    {"tag": "color 2", "hex": "#E87539", "role": "accent"},
    {"tag": "color 3", "hex": "#FFFFFF", "role": "surface"}
  ],
  "media": [
    {"key": "heroImage", "description": "", "tags": ["media 1"]}
  ],
  "text": [
    {"key": "headline", "description": "", "tags": ["text 1", "font 1", "color 1"]},
    {"key": "ctaLabel", "description": "maximum 4 words", "tags": ["button text", "font 2"]}
  ]
}
```

The template name describes the reusable composition, not one customer's brand.
Keys are exact, case-sensitive layer names. Keep `description` empty unless a
hard constraint needs words. The tool stamps `protected_regions`, `audio`, and
`transition`; omit those fields from the input.

Both tag vocabularies are closed:

| Scope | Allowed tags |
|---|---|
| Scene | `intro`, `logo`, `fade to black`, `media`, `text`, `media full frame`, `data`, `avatar`, `list`, `cta`, `dontcut`, `noteditable`, `button` |
| Placeholder | `media N`, `text N`, `font 1`–`font 3`, `color 1`–`color 7`, `allcaps`, `button text`, `logo`, `avatar`, `folder:<name>` |

Scene tags direct downstream use: `intro` first, `logo` at the end before a CTA,
`fade to black` last, `dontcut` in full. Each media placeholder needs `media N`,
`logo`, or `avatar`; each text placeholder needs `text N` or `button text`.

The same content index on two placeholders means **the same replacement**.
Use different text/media indices for independent values. Sharing a font or
color tag shares styling, not content.

Document every text layer. Document media placeholders, excluding baked decor:
generated gradient/sprite assets, video/JET assets, and tag assets are treated
as decor by the current validator. Solids are not text/media placeholders.
Validate coverage rather than assuming every visible layer is replaceable.

The tool checks actual compiled text colors and corrects their color tags
against the manifest palette. Keep each color tag's meaning consistent across
a catalog and inspect reported corrections.

Compilation embeds the manifest as `tag.ttf` and writes a JSON sidecar. Choose
an output basename distinct from the scene source, as in the example, or use
`--tags-out` for the sidecar; do not overwrite `template.json`. Inspect extracted
`tag.ttf` to read the manifest back. `render --tags ... --emit-timeline ...`
can confirm downstream placeholder tags in an authorized rendered test.

## Brand, design, and reference handoff

Start with supplied assets and decisions: approved copy, logos, fonts, colors,
references, target formats, and editable roles. Preserve factual data and
required wording. Resolve unclear source details before silently substituting
them.

A small brand note is useful when work spans multiple outputs. Record approved
font files, palette, asset paths, and relevant usage constraints. Reuse it when
appropriate; no separate brand document or generated board is required for a
simple edit.

For Figma or another design source, use available authorized access or supplied
exports. Strata has no general `figma import` command. Record frame bounds and
convert coordinates into the target scene consistently:

- Subtract the source frame's origin, then apply the chosen scale.
- Scale positions and sizes together; deliberately resolve aspect changes.
- Map groups to sub-comps when shared transforms or clipping are needed.
- Use actual font files and variants, not CSS family names.
- Convert supported SVG geometry to scene shapes when it must stay editable;
  use suitable raster exports for baked artwork.
- Transfer opacity and color deliberately, and check clipping and text wrapping.

Imported layout does not specify animation. Add movement when the brief calls
for it, using the design's hierarchy and editable requirements.

For reference-video reconstruction, inspect the provided reference and establish
what the user wants preserved: timing, layout, subject, motion, or overall feel.
`deconstruct` and `review --reference` supply measurements; neither automatically
rebuilds the video. Extract useful frame/timing observations, recreate exact
text/data as authored layers, and choose footage or generated assets for other
parts. Compare the result at corresponding times and relevant viewing sizes.

Use the amount of planning needed to handle those constraints. Do not invent
claims or alter the meaning of source material to make an edit more dramatic.
A handoff should include the assets and editable source needed for the agreed
future work, with clear distinction between baked and replaceable elements.

## Existing IDM files and raw VASCO

```bash
strata inspect existing.idm --assets extracted -o extracted/doc.json
strata repack extracted/doc.json -o revised.idm
strata schema
```

`inspect` exposes **baked VASCO**, not the original compact scene or its keyframes.
It extracts files by asset ID and writes `assets.json`. Preserve those IDs and
the document's links when editing. `repack` resolves relative asset URIs against
the document's directory.

Use `compile` for compact authoring JSON and `repack` for extracted VASCO.
Passing VASCO into `compile` is rejected. Keep compact source when available;
rebuilding original timing intentions from per-frame arrays is different work.

The codec stores basenames. Older/third-party files can contain collisions that
make some original bytes unavailable after decoding. `inspect` warns; renaming
the surviving file cannot recover missing bytes. The current compiler stages
unique asset names and deduplicates identical bytes, avoiding two known exporter
failure paths. Do not manually add duplicate copies into an extracted document
without understanding its asset IDs.

Unknown compact keys can pass through as raw fields; this is access to the
VASCO schema, not evidence that a guessed property works. Inspect
`strata schema`, compile with `--vasco` when useful, and validate the exact raw
construction. Avoid hand-editing binary IDs, checksums, or encoded bytes.
