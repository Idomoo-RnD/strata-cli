---
name: strata-cli
description: Create, edit, inspect, and render video with the Strata CLI and IDM/VASCO. Use for Strata scene authoring, media generation and editing, talking actors, transparent JET video, and personalized video. Not for the separate cloud briefs API.
---

# Strata CLI

Use Strata to build video from scene JSON and media. It compiles scenes into
Idomoo `.idm` files, renders through Idomoo, and provides tools for generating,
preparing, editing, and checking assets.

## Choose the approach

Start from the requested result and the material already available. Preserve the
user's approved copy, brand, assets, and creative decisions. Resolve ordinary
choices yourself; ask when an unknown materially changes the scope, correctness,
cost, or permission needed.

- Use scene layers for precise text, graphics, layout, and data.
- Use supplied or generated media for photographed subjects, illustration,
  performances, or motion that is better carried by footage.
- Use direct media editing for a trim, join, crop, retime, grade, or audio change.
- Combine these approaches when they help the piece.

Choose the structure, style, pace, and amount of planning that fit the task.
A short edit may need no storyboard. A complex film may benefit from a brief,
reference board, shot plan, or prototype. Use these to resolve uncertainty.
Do not add a fixed number of concepts, shots, layouts, effects, or documents.

## Find the relevant guidance

Read the sections needed for the current operation. The guides are independent
references, not a reading sequence.

| Need | Reference |
|---|---|
| Choose a command, check setup, or understand local/cloud behavior | [Tools](references/tools.md) |
| Write or edit scene JSON, text, animation, shapes, masks, or groups | [Scenes](references/scenes.md) |
| Generate or edit assets, use references, add speech or music | [Media](references/media.md) |
| Make an actor speak | [Talking actors and dialogue](references/media.md#talking-actors-and-dialogue) |
| Remove a background or use transparent video | [Transparent video and JET](references/media.md#transparent-video-and-jet) |
| Use cameras, tracking, reactive motion, personalization, tagging, or handoff | [Advanced features](references/advanced.md) |
| Validate, render, inspect, or deliver an output | [Delivery](references/delivery.md) |
| Resolve an error or an unexpected result | [Troubleshooting](references/troubleshooting.md) |

These guides describe the Strata 1.0.178 tool surface. Check the installed version
and `strata <command> --help` when flags matter. Help is offline. The examples
are starting points for mechanics; adapt their appearance to the task.

## Work with the tools

Inspect inputs before committing to an approach. Keep source files and useful
generated URLs. Build a useful first version, check it, and revise from evidence.
A small technical probe is worthwhile when it can prevent a costly failure.

Use documented scene keys. Strata's compact scene format differs from raw VASCO.
`strata schema` describes the raw format; it does not explain every compiler
shortcut. Validate unfamiliar constructions before using them throughout a scene.
Do not infer support from a familiar After Effects or browser property name.

Use the user's selected library and existing authorization. Cloud generation,
snapshots, and renders can spend budget. A snapshot is a real cloud operation.
The [media guide](references/media.md#files-urls-and-public-hosting) explains the
separate public-hosting boundary. Ordinary scene assets can remain local.

Keep long-running work observable: wait for completion or poll its job/process.
Starting a background process does not complete the task or guarantee a later
agent turn. Retry a failure when there is a reason to expect a different result.

## Judge and finish

Judge creative work against its purpose: what the viewer should understand,
notice, or feel. Choose hierarchy, motion, stillness, and sound in service of
that purpose. A restrained frame, a repeated layout, a hard cut, or silence can
be appropriate. Technical complexity is not a quality measure.

Check the actual requested output. For a rendered scene, run
`strata review out.mp4 --scene scene.json` and inspect the relevant evidence.
Compilation proves structure; it does not prove legibility, motion, or sound.
Review metrics identify things to inspect; they do not decide the creative result.

Fix blocking problems within the authorized scope and budget. Recheck the final
candidate after changes. Deliver the requested files, identify what was verified,
and state any remaining limitation. Never describe an uninspected or incomplete
preview as a finished result.
