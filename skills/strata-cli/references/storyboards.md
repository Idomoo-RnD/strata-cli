# Storyboards

Use this when one video is made of several scenes, or when the same video is rendered many times with
different values. A storyboard is a list of uploaded scenes, each placeholder fixed or a parameter. It is
created once and rendered as often as needed.

For one scene, `strata render --data` already personalizes it; a storyboard is not needed.

## Contents

- [The flow](#the-flow)
- [Write the timeline](#write-the-timeline)
- [Fixed values and parameters](#fixed-values-and-parameters)
- [Try values locally](#try-values-locally)
- [Build](#build)
- [Render](#render)
- [Scene logic](#scene-logic)
- [Change a storyboard](#change-a-storyboard)
- [Check the result](#check-the-result)
- [Errors](#errors)
- [Limits](#limits)

## The flow

```text
scenes (.json or .idm) → timeline.json → strata storyboard build → result.json (storyboard id + parameters)
                              result.json + values.json → strata storyboard render → out.mp4
                                                          strata review out.mp4 --storyboard result.json
```

Make each scene first with the normal scene workflow and check it with `validate` and `preview`. A
storyboard does not repair a scene: text that overflows in a scene overflows in the storyboard. Give every
scene in one storyboard the same size and frame rate.

Name every layer that will change. A placeholder's key is its **layer name**, in any comp, and the timeline
refers to it by that name. Scenes play one after the other, so a storyboard lasts as long as its scenes together.

## Write the timeline

```json
{
  "name": "Seminar ad",
  "scenes": [
    { "scene": "intro.json",
      "text":  { "title": "HELLO", "name": "Welcome, <<%first_name%>>!" },
      "media": { "hero": "pal://143651/<<%hero_asset%>>" } },
    { "idm": "middle.idm" },
    { "id": 1243624, "text": { "title": "THE END" } }
  ]
}
```

| Field | Meaning |
|---|---|
| `name` | The storyboard's name |
| `scenes` | Played in this order |
| `scene`, `idm` or `id` | The source, exactly one per scene: a scene .json to compile, an .idm to upload, or a scene id already in the library. Paths are relative to the timeline's folder |
| `text`, `media` | Values by layer name. Every layer not named, media and audio included, keeps the scene's own value |

`build` lists every placeholder of every scene in the request, with the scene's own value where the timeline
names none. Never write that request by hand: media and audio left out of it render as nothing. *measured:* a
storyboard whose request named only 2 text placeholders rendered a black picture and silence (-91 dB); with
all 23 placeholders and the audio listed, picture and sound (-16 dB) were right.

## Fixed values and parameters

A parameter is written `<<%name%>>`. A value without one is the same in every render. Fixed words and a
parameter can share one text: *measured:* `"Welcome, <<%first_name%>>!"` rendered as "Welcome, Dana!".

| Placeholder | Fixed | Parameter |
|---|---|---|
| Text | `"title": "HELLO"` | `"name": "<<%first_name%>>"` |
| Image or video | `"hero": "pal://143651/1244136/assets/alt_1508380691.png"` | `"hero": "pal://143651/<<%hero_asset%>>"` |

**A media value must start with a scheme** (`pal://`, `https://`, `http://`, `ual://`, `gal://`) or be
`rgb(r,g,b)`. A bare `"<<%hero_asset%>>"` is refused. Keep `pal://<library>/` in the timeline and pass the
rest of the path as the value. *measured:* a bare parameter returned 400; with the prefix, 2 renders in 2
libraries showed the swapped picture.

`pal://` names an asset uploaded inside an `.idm`, as `pal://<library>/<scene id>/assets/<file>`; the file
name gets a number added when it is uploaded, so it cannot be known before. To offer another picture for a
placeholder, put it in the scene as a hidden layer (`"visible": false`) and build: the result file's `own`
lists every placeholder's path, hidden layers included. Media you do not have locally can be a public
`https://` URL instead.

A media parameter has no default, so the **storyboard editor's preview** reports it as "inaccessible or
corrupt". Renders with a value are not affected; set a default in the editor to preview there.

Text whose length varies needs room: `shrink: true` alone fits a long value onto one small line; add
`breakline: true` to let it wrap first. IDF animated fonts are never parameters: their letters are laid out
when the scene compiles.

## Try values locally

```powershell
strata validate intro.json --data values.json
strata preview intro.json --data values.json --at 2
```

`validate --data` refuses a key that is not a layer of the scene and warns when a text value is too wide.
`preview --data` draws the scene with the values: text replaced by layer name, an image or video by its
path. Try the longest and shortest real values before building.

## Build

```powershell
strata storyboard build timeline.json --library <approved-id> --dry-run
strata storyboard build timeline.json --library <approved-id>
```

Use a library the user approved. `build` compiles and uploads each scene that changed, reuses a scene that
did not (the same upload cache as `render`), checks every key in the timeline against its scene, then
creates the storyboard and writes `timeline.storyboard.json` (`-o` names it). A compiled scene writes its
`.idm` beside the scene .json, as `compile` does. *measured:* 2 fresh 4 s scenes uploaded and became a
storyboard in one run; a second build of the same scenes reused both uploads.

**Run `--dry-run` first.** It uploads and creates nothing, compiles scenes that are not uploaded yet, checks
every key and lists the parameters; it writes the request to `<result>.request.json` and never touches the
result file. Values a scene only gets on upload read `(after upload)`. *measured:* a misspelt key was refused
by a dry run before any upload. `--reupload` forces fresh uploads.

**Keep the result file.** It holds the storyboard id, the link, the parameter list, and each scene's `own`
values. The API cannot list a storyboard's parameters, so `render` reads them from this file.

## Render

```powershell
strata storyboard render timeline.storyboard.json --data values.json -o out.mp4
```

```json
{ "first_name": "Dana", "hero_asset": "1244136/assets/alt_1508380691.png" }
```

Every value is a string. With the result file, a missing or unknown value stops before anything is sent; with
a bare storyboard id the values are not checked. Output is 1080p at most (`--height` up to 1080); it defaults to
the storyboard's own height from the result file.

A render costs **1 credit per started 30 seconds** (the API counts a credit as up to 30 s of video, so 40 s
costs 2) and is never retried. *measured:* 8 s cost 1 credit and was ready 5 s after it was accepted, in 2
renders.

The hosted video URL is built from the output name; the command adds the storyboard id and a stamp so a
render never overwrites an earlier one. `--json` returns `mp4_path`, `video_url`, `poster_url`, `duration`
and `cost`.

## Scene logic

A rule shows or hides a scene depending on a parameter. Rules are **applied at render** but **cannot be set
from the CLI yet**: the API refuses them when a storyboard is created, and `build` refuses a `logic` field.
Set a rule in the storyboard editor for now.

*measured:* with the rule "`cli_name` contains `demo`", the value `demo yes` rendered and `other` was refused
with error 2000, in 1 render of each.

When no scene matches, nothing renders. Keep one scene with no rule when a video must always come out. Which
scenes play when only some match has not been tested.

## Change a storyboard

There is no update: **build again**, which makes a new storyboard. `strata replace` on one of its scenes does
not change it, because the storyboard keeps every placeholder value it was built with, and every layer is a
placeholder. *measured:* after replacing a scene with a new background colour, the same storyboard still
rendered the old colour, in 1 render. There is no delete either; the user removes old storyboards in Studio.

## Check the result

```powershell
strata review out.mp4 --storyboard timeline.storyboard.json -o review
```

`--storyboard` checks every scene at its offset (settles, text, voice layers) and writes a frame strip for
each join (`join_NN.png`). A dissolve between scenes is often not detected as a cut, so open each strip.
Then check the video as [Delivery](delivery.md) describes: frames in **every** scene, the sound, and the
longest and shortest values. A black picture or silence means a placeholder lost its value.

## Errors

| Message | Meaning | Fix |
|---|---|---|
| `scene N (…) has no text "x"` | The key is not a layer of that scene | Use a name the message lists; text and media go under their own field |
| `media "x" must start with pal://…` | A bare parameter on media | `pal://<library>/<<%x%>>` |
| `no value for x` / `x is not a parameter` | The values do not match the result file | Fix values.json. Nothing was sent |
| `no scene's rule matched these values` | Every scene's rule failed (error 2000) | Check the values against the rules, or keep a scene with no rule |
| `scene logic cannot be set from the CLI yet` | `logic` in the timeline | Set the rule in the storyboard editor |
| Editor preview: resource "inaccessible or corrupt" | A media parameter has no default | Renders are fine; set a default in the editor to preview there |

Exit codes: 1 for input and usage errors and a refused render, 2 for a missing file or scene, 3 for API and
network failures.

## Limits

- Scenes play in the order listed.
- No update or delete; `replace` does not change a built storyboard.
- Rules are set in the editor for now.
- Not tested yet: an image replacing a video, scenes of different sizes in one storyboard, which scenes play
  when only some rules match.
