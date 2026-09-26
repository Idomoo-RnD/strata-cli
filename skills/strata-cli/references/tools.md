# Choosing and using Strata tools

Use this guide to choose a command, understand its inputs and effects, or check
the environment. For a command's complete current flags, use its `--help`.

## Contents

- [Setup and command help](#setup-and-command-help)
- [Scene tools](#scene-tools)
- [Media tools](#media-tools)
- [Inspection and project tools](#inspection-and-project-tools)
- [Account and installation tools](#account-and-installation-tools)

## Setup and command help

```bash
strata version
strata help
strata generate --help
strata render --help
```

Check for an existing installation before installing or updating anything.
The standalone CLI includes its Node runtime and codec. Many video/audio
preparation tools also need `ffmpeg` and `ffprobe` on PATH. Commands report a
missing dependency; install only what the chosen operation needs.

`--help` does not generate media, read credentials, or spend cloud budget.
`--json` requests structured output. Errors go to stderr. The main error codes
are 1 for invalid input/compile failure, 2 for a missing file, 3 for authentication,
and 4 for a render timeout; inspect the actual message as well.

Relative output paths follow the command's working directory. Give an explicit
output path when there could be ambiguity, and read the path returned by the
command. Avoid replacing an input with an output accidentally.

## Scene tools

Commands in the tables omit the `strata` prefix for readability.

| Command | Use it for | Input → output | Effects and limits |
|---|---|---|---|
| `init scene.json` | Start a compact scene | Defaults → scene JSON | Local file creation |
| `add --list` / `add <block> scene.json` | Discover or insert reusable groups | Block → scene layers/comps | Local; insertion changes the scene and makes names unique |
| `math <kind> scene.json …` | 3D objects (`box`, `card`, `cylinder`), `light`, `noise`, `scatter`, `follow` | Scene plus `--name` (objects) or `--layer` (motion), and the kind's flags → one short block | Local; written only if the scene still compiles; `math --list`, `math <kind> --help` lists every input |
| `particles <preset> scene.json --name N …` | Sparks, embers, snow, rain, confetti, smoke, bubbles, stars, dust, comet, assemble | Scene, a name, and flags overriding the preset → one `particles` layer | Local; written only if the scene still compiles; `particles --list`, `particles <preset> --help` lists every input |
| `physics <kind> scene.json …` | `world`, `body`, `hang`, `attach`, `kick`, `blast`, `wind`, `shatter`, `jelly` | Scene plus `--layer` (body kinds) or `--t` (events), and the kind's flags → the `physics` block or a layer's `body` | Local; re-simulated every compile; `physics --list`, `physics <kind> --help` lists every input |
| `chart bars --data data.json --box x,y,w,h` | Build exact data graphics | Numeric data → animated layers | Local; line/donut have different inputs; geometry is created at author time |
| `validate scene.json` | Check syntax and asset problems | Scene → errors and warnings | Local; passing is not visual proof |
| `glyphs font.ttf "copy"` | Check a font against exact copy | Font/text → missing characters | Local; `glyphs scene.json` checks the scene |
| `preview scene.json --at 2 --grid` | Check layout, transforms, mask outlines | Scene → wireframe PNG | Local; not real glyph/compositing/3D projection proof |
| `studio scene.json` | Let a person edit layout visually | Scene → saved guide JSON | Local browser server; interactive, so use when user participation helps |
| `compile scene.json -o out.idm` | Make an Idomoo scene file | Compact JSON/assets → IDM | Local; embeds assets and records scene history; alias `build` |
| `snapshot scene.json --library <id> --at 2` | Check a real rendered frame | Scene/IDM → image | Cloud; credentials, chosen library, persistent upload |
| `render scene.json --library <id> -o out.mp4` | Produce the video | Scene/IDM → MP4 and URLs | Cloud; uploads, exports, renders, and spends budget |

Use [scenes.md](scenes.md) for authoring and [delivery.md](delivery.md) for
preflight and final checks.

## Media tools

| Command | Use it for | Input → output | Effects and limits |
|---|---|---|---|
| `generate image` | Create a still from a prompt/references | Text/images → image | Cloud; local image references and URLs supported |
| `edit image` | Revise an existing image | Source/change → image | Cloud; inspect unintended changes too |
| `generate video` | Create footage or a performance | Prompt/frames/references → clip | Cloud; input modes cannot all be combined |
| `generate fastvideo` | Use the older simple image-to-video path | One image/motion prompt → clip | Cloud; different capabilities from `generate video` |
| `generate avatar` | Animate a presenter from one image and speech | Image URL/audio URL → talking clip | Cloud; the source image controls framing |
| `generate voices` | Find a narration voice | Optional search → voice IDs | Cloud |
| `generate narration` | Produce speech from exact text | Script/voice ID → audio | Cloud; inspect actual duration and pronunciation |
| `generate music` | Produce a music bed or sound effect | Prompt/duration → audio | Cloud; inspect usable length and ending |
| `captions` | Transcribe speech with segment timing | Audio/video → cues/SRT | Cloud; some inputs require public re-hosting |
| `upload` | Publish an approved input that needs a URL | File → hosted URL | Public, permanent hosting; see the media policy; alias `host` |
| `sketch` | Create a visual reference for generation | Pen spec → PNG; camera plan → MP4 | Local; animatic encoding needs ffmpeg |
| `matte` | Extract a person or character from footage | Video → transparent JET | Local inference; first use may download model/runtime |
| `jet` | Encode transparent video | RGBA frames/keyed video → JET | Local; video decoding needs ffmpeg |
| `path` | Create a rendered path overlay | SVG → JET | Local; use scene mask strokes for exact solid-color line work |
| `track` | Follow a point or planar surface | Video → position/corner-pin data | Local; ffmpeg; inspect drift and occlusion |
| `beats` | Measure onsets and audio envelopes | Audio → analysis JSON | Local; ffmpeg; not speech alignment |
| `retime` | Apply a speed change/ramp | Video/ramp → video | Local; ffmpeg; changes picture/audio timing |
| `grade` | Grade or color-match footage | Video/look/reference → video | Local; ffmpeg; inspect the resulting colors |

All `generate` rows are subcommands of one parent command. Start with the relevant
section of [media.md](media.md), especially for
[talking actors](media.md#talking-actors-and-dialogue) and
[JET overlays](media.md#transparent-video-and-jet).
Direct trim/join/crop/mix operations use ffmpeg; Strata does not provide a
separate command for every editing operation.

## Inspection and project tools

| Command | Use it for | Input → output | Effects and limits |
|---|---|---|---|
| `inspect` | Understand or extract an existing IDM | IDM → baked VASCO/assets | Local; extraction writes files |
| `repack` | Encode edited baked VASCO | VASCO/assets → IDM | Local; not the compact scene authoring path |
| `schema` | Inspect valid raw VASCO properties | Codec → schema JSON | Local; compact shortcuts are separate |
| `review` | Find timing, motion, legibility, or audio issues | MP4/optional scene → report and frame strips | Local; ffmpeg; evidence needs interpretation |
| `deconstruct` | Analyze a reference clip | MP4 → measured diagnostic data | Local; ffmpeg; measurements are not universal style targets |
| `brief init` | Keep requirements and decisions across work | Request/preferences → brief file | Local; optional planning aid; `--force` overwrites |
| `route` | Look up references for a brief | Brief/phase/features → paths | Local; paths refer to that CLI version's bundled documentation |
| `prefs list/set/unset` | Reuse recurring project preferences | Key/value → stored preference | Local; writes under the Strata home on set/unset |
| `recipe list/save/use` | Reuse approved project decisions | Project → reusable recipe/project files | Local; inspect help for arguments; not an approval transfer |
| `versions scene.json` | Inspect recorded scene history | Scene → version list | Local |
| `revert scene.json --to 1 -o restored.json` | Recover an earlier scene | Version → scene file | Local; `-o` keeps the current file separate |

Use the links in this skill for its references. It is not necessary to run
`route` at every phase or read every matching genre. Stored preferences avoid
repeat questions but do not grant new publication or spending permission.

For an existing `.idm`, first determine whether its original scene JSON is
available. Editing the source keeps its keyframes and authoring structure;
unpacked VASCO contains baked values. See
[round trips](advanced.md#existing-idm-files-and-raw-vasco).

## Account and installation tools

| Command | Purpose | What it changes |
|---|---|---|
| `auth status/login` | Check or configure Idomoo access | Login writes credentials; status can contact the service |
| `library list/create` | Find or create a render destination | Cloud; use the user's selected destination |
| `skill install/refresh` | Install the CLI's bundled skill | Rewrites managed agent skill folders and prunes old content |
| `update` | Update an installed standalone binary | Network; replaces executable and refreshes installed skills |
| `uninstall` | Remove the CLI installation | Destructive installation cleanup |
| `version` | Print the CLI version | Read-only |
| `help` | Print usage | Read-only |

These commands are available when the task needs them; creating a video is not
an instruction to change the user's installation. Keep project notes outside a
managed installed skill directory.

Render library choice can come from an explicit `--library`, project
`.idm-library`, or a recorded preference. Reuse the existing choice. If none
exists, resolve it before a cloud render or snapshot.
