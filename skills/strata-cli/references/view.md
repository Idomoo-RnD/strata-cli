# View

`strata view` opens a scene in a local browser player: play, scrub, solo
layers, flip to the rendered mp4, notes drawn on the frame. Use it to check a
scene's output as moving pictures and exported stills, and to look at a scene
together with the user. Every input is in `strata view --help`.

## Contents

- [Start it](#start-it)
- [Check the output](#check-the-output)
- [Work with the user](#work-with-the-user)
- [Drive the page](#drive-the-page)
- [Limits](#limits)

## Start it

```bash
strata view scene_v1.json --port 4421 --export-dir checks --no-open --json
```

It also opens an `.idm` as delivered, with no warnings, since an `.idm` carries no authored scene.
Pick the port; `--port 0` takes any free one and the start line names it. Run
it in the background: it serves until `strata view stop --port 4421`, or two
hours with nothing using it. Saving the scene reloads the player; a broken save
shows its error and keeps the last good frames. Starting again on the same port
with the same scene answers with the running view.

## Check the output

```bash
strata view export --port 4421 --every 1
strata view export --port 4421 --at 2.4,6.1 --solo headline --name title
strata view state --port 4421
```

`export` writes JPGs to the `--export-dir` named at start (720p at most, no
guides) and prints their paths; open them. `state` lists every top-level layer
with its in and out times, and validate's warnings with the time each one
names: export those times. `--solo` and `--hide` isolate layers by name. A save
moves the file names to the next version (`_r2_`), so a before and an after
sit side by side.

## Work with the user

Give the user the URL. Their notes, each a time, text and a box in comp pixels,
come back from `strata view notes --port 4421`. `strata view seek --port 4421
--at 3.2 --say "the title lands here"` moves their playhead and shows the
message; `strata view note … --text "fixed"` answers on the frame.

## Drive the page

With browser automation (Playwright), open the URL and call `window.strata`.
Each call resolves once that exact frame is on screen:

```js
await page.goto('http://127.0.0.1:4421/?t=3.2');
await page.evaluate(() => strata.ready());
await page.evaluate(() => strata.solo(['headline']));
await page.locator('[data-testid=stage]').screenshot({ path: 'title.png' });
const played = await page.evaluate(() => strata.play({ from: 0, seconds: 4 }));
await page.evaluate(() => strata.exportImages({ every: 1 }));
```

`await strata.help()` lists every call; `showRender(true)` shows the rendered
mp4 at the same time.

## Limits

Frames come from the local renderer that `preview` uses, with no sound, so they
are close to a cloud render but not identical. A [render and
review](delivery.md#review-the-final-candidate) stays the proof.
*measured:* on two 20 s scenes with 9 and 13 video clips, the first frame
showed 2.1–2.4 s after start, every frame was drawn in 11–13 s, playback showed
120 of 120 frames in 5 s, and 10 JPGs at 720p took 2.7–4.4 s.
