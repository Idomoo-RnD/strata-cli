# Driving a clip from stills

Starting from an image, interpolating between two, and holding the camera still.

Part of the video-generation reference — the index, and every other part, is in [video-generation.md](../video-generation.md).

## Image-to-video — `--first-frame`

*Measured:* **`--first-frame` is literal.** Video frame 0 *is* the source image — same pose,
same composition, same light. So write **Shot 1 to begin on it** ("begin exactly on the first
frame and bring it to life") rather than describing a different opening.

⚠ **Anything outside the source frame is invented.** *Measured:* a wide push-back moved the
window to centre frame, added furniture that was never there, and rendered the character
smaller and younger. **`--first-frame` locks the character and the framing you gave it — it
does not lock the set.**

- Keep the shot list **within or tighter than** the source framing when set continuity
  matters (macro, close-up and medium all held perfectly).
- If you need a wider shot, **describe the wider room explicitly** in the prompt *and* the
  Static Description, or accept that it will be invented.

*Measured* (cartoon character, 5-shot prompt, 12 s, 720p, `--audio`): 1280×720, 24 fps,
12.05 s, 4.4 MB, generation **190 s**, 4 cuts → exactly the 5 shots requested, identity held
across all five.

## Keyframe interpolation — `--first-frame` + `--last-frame`

The most directable mode: you author both ends as images and the model only solves the motion.
Use it when you need a specific beginning **and** ending — a turn, a gesture, a reveal, or a
clip that must end on an exact frame so the next can start there.

**Generate the end frame FROM the first**, or the model spends the clip reconciling two
different worlds:

```bash
strata generate image "<the START pose>" -o a.png
strata generate image "The SAME character as image 0 … <the END pose>" --reference a.png -o b.png
```

*Measured:* `--reference` held the character exactly — same eyes, freckles, curls, wardrobe
and mug — while changing the pose from three-quarters-to-camera to profile.

**The prompt is ONE continuous move, not a shot list:**

1. `Style & Mood:` and the CAPS theme as usual.
2. Identity lock referencing **both** frames: *"the character in the FIRST FRAME and the LAST
   FRAME … identical throughout."*
3. **State it plainly: "ONE single continuous shot, no cuts."** *Measured:* honoured exactly.
4. Describe the motion **physically, in order** — what starts, what moves, what the light
   does, how it settles — naming the ends explicitly: *"She begins exactly as
   in the first frame … she ends exactly as in the last frame."*
5. `Static Description:` and `Audio:` as usual.

*Measured* (5 s, 720p): **0 cuts**, both keyframes landed (frame 0 matches the start image,
the final frame matches the end image), and it was the **fastest mode — 139 s**. The
in-between was physically sensible: head turning first, then shoulders, mug lowering into one
hand, free hand rising to the frame, curls settling.

**Notes.** Keep it **short** — 5 s is plenty for one gesture; a long duration just invents
filler between two fixed poses. The real-face rule applies to **both** images. Aspect still
snaps. And this is the cleanest way to build a controlled sequence: author frames A, B, C as
images and interpolate A→B, B→C — every clip boundary is then a frame you chose.

## Locking the camera — in the prompt, not the flag

`camera_fixed` is in the API spec, but the model Idomoo routes to **rejects it on every task
type** — *measured 2026-08-22:* text-to-video, first-frame and reference-image requests all
fail with *"camera_fixed is not supported for model … must be empty"*, surfaced as an HTTP
500 so it does not even look like an input error. `strata generate video --camera-fixed` now
refuses up front with this message instead of spending a request.

**State the lock in the prompt, and say what *does* move:**

> LOCKED-OFF CAMERA: the camera is completely static on a tripod — no pan, no tilt, no zoom,
> no push, no drift, no handheld movement; the frame edges never move. Only the steam moves.

Naming the one thing that moves matters as much as the lock: a prompt that only forbids
motion invites the model to invent some. Put it in the Static Description too, so it survives
a chained continuation ([Chaining](clip-length.md#chaining--clips-longer-than-15-s)).
