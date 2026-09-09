# Steps 6–7 — render, review, and the Definition of Done

Rendering into the right library, the one review and one fix pass that is the whole budget, and what "done" means.

Part of the production workflow — the index, and every other part, is in [workflow.md](../workflow.md).

## 6. Render

`strata render scene.json --library "<id>" -o out.mp4`.

- **Library — ask before the first render, never pick one.** Renders land in the user's Idomoo
  workspace and stay there: run `strata library list`, offer reuse or create (`library create` is
  get-or-create), persist the printed id, pass that same `--library <id>` every time. `render`
  refuses to guess: [commands.md](../commands.md), *Libraries — ask, never pick*.
- Renders take minutes — run them in the background and report the `video_url`/`poster_url`.
- **Two full renders per piece: the first to review, the second to ship.** A render is not a way
  to find out what the scene does; that is what the steps before it are for — `validate`, the
  `--grid` preview at every key frame, a `snapshot` at the first, middle and last frame of every
  declared hold, and, for the riskiest hold, one **probe** render of that shot alone
  (`duration` cut to ≤ 5 s) measured with `strata review` before the full piece is spent. After
  the first full render, `review` names every must-fix; **fix all of them in one revision pass**,
  not one class per render, then render the second and ship it. The second review **verifies that
  list** rather than starting a fresh hunt — a piece this dense will always surface something new,
  so "ship when nothing is left" never terminates while "ship when the list is fixed" does. A third full render is not a
  fix loop, it is a finding: the plan or the skill was wrong somewhere, so stop, write what and
  why in `decisions.md`, and re-plan before spending it. *Measured:* an unattended run that
  treated render-and-review as its debugger spent **six** full renders and 44 of its 83 minutes
  in that loop, on faults every one of which a snapshot, a probe or a defined number would have
  caught first.
- Only if the user asks for the scene to be **tagged** (a reusable template / catalog entry, not a
  one-off): add `--tags manifest.json` here and on `compile`, after reading
  [tagging.md](../engine/tagging.md). The manifest rides inside the `.idm`, so the library copy is
  self-describing. Both vocabularies are closed — tags are looked up, not invented.

## 7. Review the rendered MP4

A poster frame proves composition; it proves nothing about time.
`strata snapshot scene.json --library <id>` (a fast poster-only frame, cheaper than a full MP4)
is the check *before* the render; after the first render, review the MP4 itself, per
[review.md](../qa/review.md). **Motion is judged on a filmstrip of consecutive frames, never on
stills** — spacing on the strip is velocity, and anything smaller than a few per cent of the
frame has to be cropped and scaled up to be seen at all ([review.md](../qa/review.md), *Motion is
judged on a filmstrip*):

```bash
strata review out.mp4 --scene scene.json [--reference ref.mp4] -o review/
```

It detects cuts, writes a contact sheet, frames ±3 around every cut and settle, freezes, loudness,
motion energy per shot, and a timecoded `report.md`. Open it, watch the MP4 the four ways
[review.md](../qa/review.md) names, put the measured tokens beside the four declared numbers, and fix
every must-fix, citing the timecode. Compiling, validating, or using advanced features (3D,
camera, `.jet`, occlusion) is never itself a reason to approve.

Debug with `--vasco` or `strata inspect out.idm`.

## Definition of Done

- The message lands in the first 3 seconds, every shot earns its place, and every move has a job.
- **The render's measured shot-length range (`shortest`, `longest`, `rhythmRegularity`), energy,
  stillness and loudness match the four declared numbers**, or the report says which one moved and
  why. A piece that drifted back to the middle is not done, and neither is one that hit its average
  by cutting every shot at it.
- Text legible muted and inside the safe area — by placement, panel or grade, not by a scrim
  patched under it.
- `validate` is clean, or each warning is justified in the report.
- Every layer name unique across every comp — and no `⚠ renamed … duplicate layer name(s)` line
  was accepted. The compiler renames duplicates (`label`→`label_2`), which changes the
  personalization key, so the scene is fixed rather than the rename accepted.
- Clips outlast their slots; nothing loops to fill time; motion blur on every moving layer and
  `"motion_blur": true` on every animated camera; settles land; cuts sit on the audio.
- `strata review` run on the final MP4 and every must-fix from **that one review** fixed in a
  single pass. The second review verifies that list and ships; it does not open a new hunt, and
  notes that are not must-fixes are recorded in `decisions.md` rather than re-rendered
  ([review.md](../qa/review.md), *One review, one fix pass*).
- The scene filename is versioned and the library id was the user's choice (`--library`, the
  project's `.idm-library`, or the recorded `library` preference).
- `BRIEF.md` exists with no blank left in it, names every kind the piece became, and the review's
  four measured numbers are read against the four it declares. Answers the user gave that will
  repeat (library, aspect, loudness, attended or not, brand, voice) were recorded with
  `strata prefs set`; a piece that opens a series was saved with `strata recipe save`.
