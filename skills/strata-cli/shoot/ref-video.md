# `--ref-video` — controlling the shots

Driving the camera plan and the cut timing of a generated clip from a video you supply.

Part of the reference/dialogue/shot-control guide — the index, and every other part, is in [video-generation-advanced.md](../video-generation-advanced.md).

## `--ref-video` — control the SHOTS

A reference video hands the model the thing a prompt is worst at conveying: **the edit**. Cut
timing, shot sizes, camera height and camera movement all transfer. Cite as `<Video_1>` —
**angle** brackets. Two ways to use it.

### Use 1 — mimic footage the user gave you

Cite it as the shot plan and **write no shot list of your own** — if you enumerate shots you
are overriding the very thing you attached:

```text
MIMIC <Video_1> AS THE SHOT PLAN. Copy the edit of <Video_1> exactly: the same number
of shots in the same order, the same shot sizes in the same sequence, the same cut
rhythm and timing, and the same camera behaviour in each shot. Do not invent a
different structure. Only the subject and the location change; the shot flow is
<Video_1>'s.
```

*Measured*, with a 4-shot reference and a completely different subject:

| | reference | output |
|---|---|---|
| cuts | 1.500 / 3.458 / 6.917 s | **1.500 / 3.458** / 8.000 s |
| shot progression | wide → macro detail → face ECU → departing wide | **identical, 1:1** |

The first two cuts came back **frame-identical**, while subject and location changed completely.

If no usable URL exists, follow [upload policy](upload.md): only non-sensitive material with
informed consent may use `strata upload clip.mp4 --allow-public-upload`. Never customer/private
footage. *Measured:* a reference
video is exempt from the privacy pre-filter — photoreal people, including a face close-up,
were accepted with no flag. (`--realistic-human` with only `--ref-video` is a hard error.)

### Use 2 — block the shot yourself with a grey-box animatic

When *you* want the camera plan, build it with `strata sketch` (below) and feed the render in
as `--ref-video`.

**The instruction that makes it work is telling the model what the reference _isn't_.**
Without this it copies the grey untextured look:

```text
<Video_1> IS A GREY-BOX ANIMATIC - A CAMERA AND STAGING PLAN ONLY. It is untextured
geometry and flat blocks on a plain background. Each labelled block is a PLACEHOLDER
with its name written on it, standing exactly where that thing stands and moving exactly
along the path that thing moves. IGNORE the look of <Video_1> completely - ignore the
background, the grey blocks and the text labels. Take from <Video_1> ONLY:
- the number of shots and the exact moments it cuts;
- the camera position, height, angle and lens for each shot;
- the camera movement inside each shot, at the same speed;
- the ground position and travel path of every labelled placeholder.

REPLACE EACH LABELLED BLOCK WITH THE REAL THING, at the same position, the same scale
and following the same path:
- the placeholder labelled BOY is the boy from [Image 1];
- KERB, BUILDING, TRAFFIC LIGHT and BUS STOP are those features of [Image 2].
```

Close the Static Description with the negative too: *"fully textured and fully lit, nothing
grey, no placeholder blocks, no text on screen."* *Measured:* zero leakage — no blocks, no
labels, no plain background in the output.

*Measured*, a 3-shot sketch with cuts designed at 2.00 / 4.00 s:

- output cut at **2.042 / 3.958 s** — within **one frame** at 24 fps;
- all three camera setups reproduced (low wide push-in → high top-down → side medium arc);
- the **box mapping was honoured**: a small box on the table became the pocket watch, two tall
  boxes at the back became shelves of clocks flanking frame left and right, at the same scale
  and position.

### The strongest combination: sheets + plate + sketch

References compose. Character sheet as `[Image 1]`, location plate as `[Image 2]`, animatic as
`<Video_1>`: the images say **who and where**, the video says **how it is shot**.

*Measured* on a 10 s street-crossing sketch: character came off the sheet (glasses, hoodie,
sneakers, backpack), world came off the plate (zebra stripes, pedestrian light, bus shelter,
shopfronts), and the first cut matched at **3.500 s exactly**.

### ⚠ What does NOT transfer cleanly

- **Look leaks from the reference.** With real footage as the reference, a macro insert came
  back on the *reference's* wet cobbles rather than the new location's surface. State the
  intended surfaces and palette in the Static Description.
- **Style breaks on close inserts.** Macro and top-down inserts came back with a near-photoreal
  adult hand instead of the cartoon character's hand, even while the wides and the face
  close-up stayed on-model.
- **Extreme camera positions get moderated.** A near bird's-eye (camera 13 m up) came back as a
  modest raised three-quarter angle. Design the sketch within believable camera heights.
- **Per-actor placement is a hint, not a constraint.** Staging held once the actors were the
  *subject* of the shot; in the establishing wide the crossing came back empty in the
  foreground with figures far down the street. Do not rely on the animatic to place extras.
- **Cut timing drifts on later cuts.** The first cut matched exactly in every test; the last
  cut lands anywhere from 0.6 s early to 1.1 s late. **Trust cut 1; treat the rest as
  approximate.**
- **Sprite labels can leak as on-screen TEXT.** *Measured:* a 5 s run rendered the word `KERB`
  onto the asphalt despite "no text on screen" in the Static Description — the same sketch at
  10 s did not. The negative reduces this, it does not guarantee it. **Check the output for
  stray label text**, and keep labels short and generic.
- **Slowest mode.** *Measured:* 315 s, 357 s, 367 s and once 541 s, versus 139 s for keyframes.

### Checklist

- [ ] Cited as `<Video_1>` — **angle** brackets
- [ ] **No** `--first-frame` / `--last-frame` (reference media, hard `422`)
- [ ] ≤ 3 reference videos
- [ ] Mimicking footage? Then write **no shot list** — the reference is the shot list
- [ ] Using an animatic? "IGNORE the look of `<Video_1>` completely" + an explicit list of
      what to take from it
- [ ] Every placeholder mapped by label to a real thing, ideally to `[Image N]`
- [ ] Static Description ends with the negative: nothing grey, no blocks, no text on screen
- [ ] Camera heights in the sketch kept believable
- [ ] Surfaces and palette stated, so the reference's look does not leak in
- [ ] Only cut 1 relied on for exact timing
- [ ] Output checked for stray label text burned into the picture

---
