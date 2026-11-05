# Storyboards

Use this when one video is made of several scenes, or when the same video is rendered many times with
different values. A storyboard is a list of uploaded scenes, each placeholder fixed or a parameter. It is
created once and rendered as often as needed.

For one scene, `strata render --data` already personalizes it; a storyboard is not needed.

## Contents

- [The flow](#the-flow)
- [Write the timeline](#write-the-timeline)
- [Fixed values and parameters](#fixed-values-and-parameters)
- [Build](#build)
- [Render](#render)
- [Scene logic](#scene-logic)
- [Check the result](#check-the-result)
- [Errors](#errors)
- [Limits](#limits)

## The flow

```text
scenes (.json or .idm) → timeline.json → strata storyboard build → result.json (storyboard id + parameters)
                              result.json + values.json → strata storyboard render → out.mp4
```

Make each scene first with the normal scene workflow and check it with `validate` and `preview`. A
storyboard does not repair a scene: text that overflows in a scene overflows in the storyboard. Give every
scene in one storyboard the same size and frame rate.

Name every layer that will change. A placeholder's key is its **layer name**, and the timeline refers to it
by that name. Scenes play one after the other, so a storyboard lasts as long as its scenes together.

## Write the timeline

```json
{
  "name": "Seminar ad",
  "scenes": [
    { "scene": "intro.json",
      "text":  { "title": "HELLO", "name": "<<%first_name%>>" },
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
| `text`, `media` | Values by layer name. Anything not named keeps the scene's own value |

`build` lists every placeholder of every scene in the request, with the scene's own value where the timeline
names none. Never write that request by hand: media and audio left out of it render as nothing. *measured:* a
storyboard whose request named only 2 text placeholders rendered a black picture and silence (-91 dB); with
all 23 placeholders and the audio listed, picture and sound (-16 dB) were right.

## Fixed values and parameters

A parameter is written `<<%name%>>`. A value without one is the same in every render.

| Placeholder | Fixed | Parameter |
|---|---|---|
| Text | `"title": "HELLO"` | `"name": "<<%first_name%>>"` |
| Image or video | `"hero": "pal://143651/1244136/assets/alt_1508380691.png"` | `"hero": "pal://143651/<<%hero_asset%>>"` |

**A media value must start with a scheme** (`pal://`, `https://`, `http://`, `ual://`, `gal://`) or be
`rgb(r,g,b)`. A bare `"<<%hero_asset%>>"` is refused. Keep `pal://<library>/` in the timeline and pass the
rest of the path as the value. *measured:* a bare parameter returned 400; with the prefix, 2 renders in 2
libraries showed the swapped picture.

`pal://` names an asset uploaded inside an `.idm`, as `pal://<library>/<scene id>/assets/<file>`. To offer
another picture for a placeholder, put it in a scene as a hidden layer (`"visible": false`), build, and read
its path with `GET /scenes/{scene id}` or from the scene's export. Media you do not have locally can be a
public `https://` URL instead.

IDF animated fonts are never parameters: their letters are laid out when the scene compiles.

## Build

```powershell
strata storyboard build timeline.json --library <approved-id> --dry-run
strata storyboard build timeline.json --library <approved-id>
```

Use a library the user approved. `build` compiles and uploads each scene that changed, reuses a scene that
did not (the same upload cache as `render`), checks every key in the timeline against its scene, then
creates the storyboard and writes `timeline.storyboard.json` (`-o` names it). *measured:* 2 fresh 4 s scenes
uploaded and became a storyboard in one run; a second build of the same scenes reused both uploads.

`--dry-run` uploads nothing and writes the request beside the result path; scenes not yet uploaded are left
out of it. `--reupload` forces fresh uploads.

**Keep the result file.** It holds the storyboard id, the link and the parameter list. The API cannot list a
storyboard's parameters, so `render` reads them from this file.

**Each build makes a new storyboard.** There is no update and no delete, so do not build in a loop while
iterating: get each scene right with `preview` first. The user removes old storyboards in Studio.

## Render

```powershell
strata storyboard render timeline.storyboard.json --data values.json -o out.mp4
```

```json
{ "first_name": "Dana", "hero_asset": "1244136/assets/alt_1508380691.png" }
```

Every value is a string. With the result file, a missing or unknown value stops before anything is sent. With
a bare storyboard id the values are not checked. Output is 720p at most (`--height` up to 720). A render costs
1 credit per 30 seconds of video and is never retried. *measured:* an 8 s, two-scene storyboard cost 1 credit
and was ready 5 s after it was accepted, in 2 renders.

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

## Check the result

Check a storyboard render like any other ([Delivery](delivery.md)): its frames and its sound. Look at a frame
**in each scene**, not only the first. A value can overflow a layout that the default fitted, so render the
longest and the shortest real values. A black picture or silence means a placeholder lost its value.

## Errors

| Message | Meaning | Fix |
|---|---|---|
| `scene N (scene id …) has no text "x"` | The key is not a layer of that scene | Use a name the message lists; text and media go under their own field |
| `media "x" must start with pal://…` | A bare parameter on media | `pal://<library>/<<%x%>>` |
| `no value for x` / `x is not a parameter` | The values do not match the result file | Fix values.json. Nothing was sent |
| `no scene's rule matched these values` | Every scene's rule failed (error 2000) | Check the values against the rules, or keep a scene with no rule |
| `scene logic cannot be set from the CLI yet` | `logic` in the timeline | Set the rule in the storyboard editor |

Exit codes: 1 for input and usage errors and a refused render, 2 for a missing file or scene, 3 for API and
network failures.

## Limits

- Scenes play in the order listed.
- No update or delete.
- Rules are set in the editor for now.
- Not tested yet: leaving a value out at render through a bare id, fixed text and a parameter in one text,
  an image replacing a video, scenes of different sizes in one storyboard. Try these on a throwaway
  storyboard before relying on them.
