# Editing and extending a clip — last resort

Changing or lengthening a clip that already exists, and why this is the last thing to reach for.

Part of the reference/dialogue/shot-control guide — the index, and every other part, is in [video-generation-advanced.md](../video-generation-advanced.md).

## Editing and extension — ⚠ LAST RESORT

Put the source clip in `--ref-video` and cite it as `<Video_1>`; the prompt either **changes**
it (edit) or **continues** it (extension).

### 🛑 Reach for these LAST

Both are the slowest mode (~300–330 s *measured*), both re-generate the whole clip rather than
touching the original, and both give less control than every mode above. **Exhaust the other
features first**, in this order:

| Want to… | Use this instead | Why it beats editing/extension |
|---|---|---|
| A different look / colour / material | **Re-generate** with the prompt you actually want | Same cost, full control, no drift from a source |
| Keep a character across clips | **`--ref-image`** | Purpose-built for identity; cheaper to steer |
| Keep a *world* across clips | **`--ref-image`** with a location plate | Same |
| Continue a shot with EXACT continuity | **Chain on `--last-frame-out`** | *Measured:* pixel-exact handoff, no identity drift |
| Reuse the camera plan | **`--ref-video` as a shot plan** | You keep authorship of the content |
| Fix a content rejection | **`--realistic-human`** | A flag, not a re-generation |

Only when none of those fits — the user hands you a finished clip and wants **that clip**
changed or continued — is this the tool.

### Editing — change one thing, freeze everything else

*Measured.* Source: a studio watch clip. Instruction: brushed steel → polished gold, change
nothing else.

| At | Source | Edited |
|---|---|---|
| 1.5 s | steel case, navy dial | **gold case**, navy dial, same pose |
| 5.0 s | macro, steel hands | **gold hands and markers**, same crop |
| 8.5 s | pulled back with reflection | **gold**, same framing, same reflection |

It held the boundary exactly: case, bezel, crown and hands turned gold while the **dial stayed
navy and the strap stayed dark leather**, as specified. Framing, camera moves, background and
reflections were unchanged at every matched timestamp.

What made it work — the prompt is a **freeze list plus one change**:

```text
EDIT <Video_1>. Keep EVERYTHING about it identical - the same three shots, the same cut
points, the same camera moves at the same speed, the same framing, the same lighting
setup, the same background and the same reflections.

CHANGE ONE THING ONLY: <the change, described physically>.

Change nothing else. Do not re-time it, do not re-frame it, do not add or remove a shot,
do not move the camera differently.
```

Then repeat the invariants in the Static Description. **One change per call** — a list of
edits is a re-generation wearing a disguise, so write the prompt you want instead. (A **still**
is edited the same way, and `strata edit image <img|url> "<what changes>"` writes the freeze list
for you — [assets.md](../assets.md).) Global
changes (grade, season, weather, material, time of day) land far more reliably than local ones
("remove the cup from the table" re-generates the whole shot and the rest drifts with it).

### Extension — the next N seconds of the same take

*Measured:* a 10 s continuation of a 12 s clip. **0 cuts**, opens on the source's final
framing, and the grade, lens and location carry over without a frame handoff.

```text
CONTINUE <Video_1>. This video is what happens NEXT, immediately after its final frame -
the same subject, the same location, the same light, the same lens and grade. It must
feel like the next ten seconds of the same take, not a new video.
<then: what happens, physically, in order>
Match <Video_1> exactly for <the identity invariants>.
```

⚠ **Extension duplicated the subject.** *Measured:* between ~4.8 s and ~7.2 s the clip
contained **two women** — one in a dark top, one in the olive shirt from the source — before
resolving back to one as she exited frame. The continuation was otherwise correct.

So identity is **not** guaranteed through a large action:

- **Continuity of a PERSON matters → chain on `--last-frame-out`**, which gave a pixel-exact
  handoff with no duplication.
- **Reference-video extension** carries the whole clip's motion, grade and feel — use it when
  the *look* must continue and the action is small.
- The duplication appeared during the **largest movement**. If you must extend a person shot,
  keep the duration short and the action simple, and **check the middle of the clip**.

### Checklist

- [ ] Every other feature ruled out first (table above) — this is the last resort
- [ ] Source clip in `--ref-video`, cited as `<Video_1>`
- [ ] EDIT: an explicit freeze list, then exactly **ONE** change, then "change nothing else"
- [ ] EDIT: invariants repeated in the Static Description
- [ ] EXTEND: "what happens NEXT, immediately after its final frame … the same take"
- [ ] EXTEND: identity invariants restated, duration short, action simple
- [ ] EXTEND: **middle of the clip checked for a duplicated subject**
- [ ] Budget ~300 s — the slowest mode

---
