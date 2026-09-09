# Every command, in one line each

Generated from `src/command-metadata.mjs`; do not edit this table by hand.
Part of [commands.md](../commands.md). `strata <command> --help` uses the same metadata,
runs offline with no side effects and exits 0. Add `--json` for structured output.
Errors go to stderr: 1 invalid input/compile, 2 missing file, 3 auth, 4 render timeout.

## Every command, in one line each

| Command | Purpose / input → output | Runtime / side effects | Example / reference |
|---|---|---|---|
| `compile` (aliases: `build`) | Compile a compact scene; scene JSON → IDM | local; codec. Writes IDM and scene history; --vasco adds a .vasco.json sidecar; embeds referenced asset bytes | `strata compile scene_v1.json -o scene_v1.idm` · [detail](../make/author.md) |
| `validate` | Check syntax, assets and warnings; scene → diagnostics | local. Reads assets; no cloud render | `strata validate scene_v1.json` · [detail](../make/author.md) |
| `inspect` | Unpack an IDM; IDM → baked VASCO and assets | local. Writes extracted files when requested | `strata inspect scene.idm --assets extracted` · [detail](../engine/interop.md) |
| `repack` | Re-encode baked VASCO; VASCO + files → IDM | local. Embeds referenced files; not a keyframe authoring route | `strata repack extracted/doc.json -o revised.idm` · [detail](../engine/interop.md) |
| `init` | Create a starter scene; starter → JSON | local. Writes scene | `strata init scene.json` · [detail](../engine/scene.md) |
| `render` | Render a scene or personalized batch; scene/IDM → MP4 + hosted URLs | cloud; credentials. Uploads scene into chosen library; spends render budget | `strata render scene_v1.json --library <approved-id> -o scene_v1.mp4` · [detail](../make/deliver.md) |
| `snapshot` | Prove a real rendered frame; scene → poster | cloud; credentials. Uploads a library entry; not a free local preview | `strata snapshot scene_v1.json --library <approved-id> --at 2` · [detail](../make/deliver.md) |
| `preview` | Inspect layout, transforms and mask outlines; scene → wireframe PNG | local. Writes preview; not glyph or 3D projection proof | `strata preview scene.json --at 2 --grid` · [detail](../make/author.md) |
| `studio` | Interactive layout designer; scene → guide JSON | local browser. Starts a server; waits for user Save; ask before launching | `strata studio scene.json` · [detail](../craft/studio.md) |
| `add` | Insert a reusable sub-comp; block → scene layers | local. Modifies scene; uniquifies names | `strata add lower-third scene.json` · [detail](../engine/blocks.md) |
| `glyphs` | Check font coverage; font/copy → missing glyphs | local. Read-only | `strata glyphs ./font.ttf "Hello €"` · [detail](../engine/text.md) |
| `jet` | Encode alpha video; frames/video → JET | local; ffmpeg for video input. Writes lossy alpha video; match scene fps | `strata jet green.mp4 --method chroma --key 0,255,0 --fps 24 -o subject.jet` · [detail](../shoot/alpha.md) |
| `matte` | Extract a person/character matte; video → JET | local inference; model download; ffmpeg. Downloads model/runtime if absent; writes alpha video | `strata matte person.mp4 -o person.jet` · [detail](../shoot/alpha.md) |
| `track` | Track a surface or point; video → corner-pin/position data | local; ffmpeg. Writes tracking data; inspect drift | `strata track plate.mp4 --point 320,240` · [detail](../recipes/tracking.md) |
| `beats` | Measure onsets and audio envelopes; audio → beat/envelope JSON | local; ffmpeg. Writes analysis | `strata beats bed.wav --fps 24 --bands 12` · [detail](../craft/generative-fx.md) |
| `captions` | Transcribe segment-level speech timing; audio/video → cues | cloud; credentials; ffprobe for local preflight. Local/non-S3 inputs require explicit consent before public permanent hosting; never sensitive data | `strata captions <approved-S3-url> -o cues.json` · [detail](../shoot/captions.md) |
| `upload` (aliases: `host`) | Host an approved non-sensitive generation input; file → URL | cloud. PUBLIC permanent publication, no delete; explicit informed consent required | `strata upload approved-reference.png --allow-public-upload` · [detail](../shoot/upload.md) |
| `sketch` | Make a grey-box camera animatic; shot plan → MP4 | local; ffmpeg. Writes animatic; later generation reference requires upload consent | `strata sketch plan.json -o plan.mp4` · [detail](../shoot/sketch.md) |
| `path` | Render a path overlay; SVG → alpha JET | local. Rescales artwork; use scene mask trim for exact solid-color paths | `strata path mark.svg -o mark.jet --duration 2` · [detail](../cli/generating-assets.md) |
| `retime` | Apply a deliberate speed ramp; video → retimed video | local; ffmpeg. Re-encodes picture and audio | `strata retime clip.mp4 --ramp "0:1,2:0.5,3:1" -o ramp.mp4` · [detail](../shoot/video-editing.md) |
| `grade` | Color match or grade footage; video + look → video | local; ffmpeg. Re-encodes output; inspect color fidelity | `strata grade clip.mp4 --match reference.mp4 -o graded.mp4` · [detail](../cli/generating-assets.md) |
| `chart` | Emit animated chart layers; numeric data → scene layers | local. Geometry is baked at emission; render --data changes labels, not heights/sweeps | `strata chart bars --data data.json --box 100,100,900,400 --font font.ttf` · [detail](../engine/personalization.md) |
| `review` | Inspect timing, cuts, motion, legibility and mix; MP4 → report and frame strips | local; ffmpeg. Writes evidence; does not decide aesthetic approval | `strata review scene_v1.mp4 --scene scene_v1.json -o review` · [detail](../qa/review.md) |
| `deconstruct` | Analyze a reference; MP4 → diagnostic tokens | local; ffmpeg. Writes measurements; not a universal style target | `strata deconstruct reference.mp4 -o reference.json` · [detail](../direct/director.md) |
| `route` | Get phase-specific required reads; brief → required/capability/lookup/delivery paths | local. Read-only; default phase direction; explicit mode resolves ambiguous text | `strata route BRIEF.md --phase authoring --features text,camera` · [detail](../make/before-you-start.md) |
| `brief` | Create the persistent design contract scaffold; request/preferences → BRIEF.md | local. Writes brief; --force overwrites; consent/approvals are not inherited | `strata brief init "A restrained typographic ident"` · [detail](../make/before-you-start.md) |
| `prefs` | Remember repeat answers; key/value → preferences | local. Writes ~/.strata/prefs.json on set/unset | `strata prefs list` · [detail](../make/before-you-start.md) |
| `recipe` | Reuse approved project decisions; project → reusable brief/brand recipe | local. Writes ~/.strata/recipes or project files; not an approval transfer | `strata recipe list` · [detail](../make/before-you-start.md) |
| `versions` | Inspect scene history; scene → versions | local. Read-only | `strata versions scene.json` · [detail](../engine/scene.md) |
| `revert` | Restore a previous scene; version → scene | local. Writes a new history head; preserves previous history | `strata revert scene.json --to 1 -o restored.json` · [detail](../engine/scene.md) |
| `edit` | Edit an existing image; image + change → hosted image | cloud; credentials. Sends source to AI; local source encoded directly; edits can drift | `strata edit image hero.jpg "Remove the background prop" -o revised.jpg` · [detail](../shoot/images.md) |
| `generate` | Generate image, footage, voice or music; prompt/references → local files + hosted URLs | cloud; credentials. Spends generation budget; sends inputs to AI. Video frames and references cannot mix; ref-audio needs an image/video companion. No --style or --camera-fixed; describe them in the prompt. Save with -o or --out-dir | `strata generate video "A continuous product close-up" --duration 5 --ratio 16:9` · [detail](../cli/generating-assets.md) |
| `auth` | Manage/check credentials; credentials → auth status | local storage; cloud status. Login writes credential store; never log secrets | `strata auth status` · [detail](../cli/setup.md) |
| `library` | Choose an Idomoo library; account → library IDs | cloud; credentials. Create is get-or-create; use user-approved destination | `strata library list` · [detail](../cli/setup.md) |
| `schema` | Print the VASCO schema; codec → schema JSON | local. Read-only; compact sugar is documented separately | `strata schema` · [detail](../format.md) |
| `skill` | Install/refresh managed skills; bundled skill → agent directories | local; distribution fallback may use network. Replaces managed skill files; back up personal notes first | `strata skill refresh` · [detail](../cli/setup.md) |
| `update` | Update the installed binary and skills; release → local installation | network. Replaces binary and managed skills | `strata update` · [detail](../cli/setup.md) |
| `uninstall` | Remove the CLI installation; installation → removal | local. Destructive; user authorization required | `strata uninstall` · [detail](../cli/setup.md) |
| `version` (aliases: `--version`, `-v`) | Print the CLI version; binary → version | local. Read-only | `strata version` · [detail](../cli/setup.md) |
| `help` (aliases: `--help`, `-h`) | Show command help without side effects; command → usage | local. No cloud access or file writes; exit 0 | `strata chart --help` · [detail](../commands.md) |
