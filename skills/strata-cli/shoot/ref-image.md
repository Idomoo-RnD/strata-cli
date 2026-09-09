# Addressing a reference, and `--ref-image`

How to name a reference so the model actually cites it, then carrying a character, a product or a world across clips.

Part of the reference/dialogue/shot-control guide — the index, and every other part, is in [video-generation-advanced.md](../video-generation-advanced.md).

## 🔑 How to address a reference — get this wrong and it is never cited

| Reference | Cite it as | Limit |
|---|---|---|
| `--ref-image` | **`[Image 1]`, `[Image 2]`** — *square* brackets | ≤ 9 |
| `--ref-video` | **`<Video_1>`, `<Video_2>`** — *angle* brackets | ≤ 3 |
| `--ref-audio` | **`[Audio 1]`, `[Audio 2]`** — *square* brackets | ≤ 3 |

Numbering is **1-based, in the order the flags appear on the command line**. Mixing the
bracket styles up means the reference is silently never cited: the model still generates, it
just ignores what you attached.

---

## `--ref-image` — carry a character, a product or a world across clips

Attach images that steer subject and style, and address them as `[Image N]`. This is how you
put **your** character in **your** location without owning a single frame of the result.

```bash
strata generate video "<prompt citing [Image 1] and [Image 2]>" \
  --ref-image <character_sheet.png> --ref-image <location_plate.png> \
  --ratio 16:9 --duration 12 -o shot.mp4
```

### Give every image an explicit JOB

Name each one, say what it governs, and list its invariants — the same discipline as the
identity lock:

```text
[Image 1] is the CHARACTER SHEET and is the definitive reference for the boy in every
shot: the same very large round amber eyes behind big round glasses with thick black
rims, the same messy sandy-blond hair, the same bright orange hoodie with a white
drawstring, the same dark green cargo shorts, the same red high-top sneakers with white
soles. Match [Image 1] exactly for his face, his proportions and his wardrobe in every
single shot.

[Image 2] is the LOCATION plate and is the definitive reference for the world in every
shot: the same narrow night market alley in the rain, the same striped canvas awnings,
the same tangle of paper lanterns and hot pink, teal and amber neon, the same steaming
noodle cart, the same wet cobblestones smearing the neon. Match [Image 2] exactly for
the set, the palette and the lighting in every single shot.
```

Then keep citing them **inside the shots** — "the boy from [Image 1] small in the frame",
"straight down the alley of [Image 2]" — and restate both in the Static Description:
**state it three times**.

### Build the reference images for the job

- **Character** → a proper **model sheet**: the same character in front, three-quarter, side
  and back views on one flat grey background, same ground line, plus a couple of expression
  heads — every angle it needs, not one pose.
- **Location** → a clean **plate with NO PEOPLE in it**. Put `ABSOLUTELY NO PEOPLE, no
  characters, no figures anywhere in the frame` in the image prompt — a stray figure in the
  plate will fight the character you are inserting.
- **One subject per image**, clean and unambiguous. A reference containing two people makes
  the model guess.

Both are ordinary `strata generate image` calls, and their printed `url:` goes straight into
`--ref-image`.

### Measured

Character sheet + location plate, 5-shot prompt, 12 s, 720p, 16:9, `--audio`:

- **1280×720, 12.05 s, 6.0 MB**, generation **259 s**.
- **Location transfer is extremely faithful** — not "a neon market" but *that* market: the
  same signage, awnings, noodle cart and reflections. The opening wide read as the plate
  itself with the character walked into it.
- **Character transfer held**: glasses, amber eyes, hair, hoodie, cargo shorts, high-tops.
- ⚠ **It dropped a shot — 4 of the 5 requested.** The same model kept all 5 in text-to-video
  *and* image-to-video; with two references it lost the medium tracking shot. **Budget ~4
  shots per 12 s with references**, not 5.
- ⚠ **A small prop drifted.** The red backpack is on the sheet and named in the prompt, yet it
  vanished in the final walking-away shot. **Wardrobe survives; small accessories do not.** If
  a prop matters, give it its own shot and its own sentence, or accept losing it.

### Notes

- **Set `--ratio` explicitly.** There is no input frame to infer from — do not leave it on
  `adaptive` here.
- A reference with a **real face, a logo or a real product** → expect `--realistic-human` (the
  CLI auto-retries and says so).
- References give you *who and what*, never an exact opening frame.

### Checklist

- [ ] **No** `--first-frame` / `--last-frame` — hard `422`, whichever reference type you attach
- [ ] ≤ 9 images, addressed as `[Image N]`, 1-based in flag order
- [ ] Every image given an explicit JOB and its invariants listed
- [ ] References re-cited inside the shots AND in the Static Description
- [ ] Character reference is a multi-view model sheet, not one pose
- [ ] Location plate generated with "no people" stated in its prompt
- [ ] `--ratio` set explicitly — nothing to infer from
- [ ] ~4 shots per 12 s, not 5
- [ ] Any must-keep prop given its own shot and its own sentence

---
