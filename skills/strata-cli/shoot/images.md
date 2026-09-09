# Generating and editing images

A still from a prompt, and changing one that is already 90 % right instead of rolling a new object.

Part of the asset-generation reference — the index, and every other part, is in [assets.md](../assets.md).

## `strata generate image "<prompt>" [flags]`
A still PNG (async, ~10–20s).

| flag | meaning |
|---|---|
| `--aspect` | `16:9 4:3 3:4 1:1 9:16 21:9` (default `1:1`). Match the comp (e.g. `9:16` for vertical). |
| `--colors "#hex,#hex"` | brand palette to bias the result |
| `--reference <img\|url>` | **reference image(s)** — repeatable (and comma-splittable); each a local file or URL |
| `-o <file>` / `--out-dir <dir>` | output path / folder (default `./strata_assets/`). **A relative path resolves against the shell's working directory, not the project folder** — generations launched in waves from a backgrounded or parallel shell can inherit a different cwd and land somewhere else entirely. Pass an absolute path when generating in waves, and read the `✅ saved <path>` line: it is always absolute, so it tells you where the file really went |

### Reference images — art style, characters, composition (the important part)
Reference images steer the result toward a **look**, a **character**, a **logo**, or a
**composition**. Use them whenever:
- the user gives an image — a brand character, mascot, logo, product photo, style frame, or a
  previous generation — and wants that look/subject, **or**
- you need a **recurring character or consistent art style across shots**. Generate every shot
  from the **same reference(s)** so they stay on-model — pure text prompts drift shot to shot.

**Drive them by INDEX in the PROMPT.** References are numbered by order: the 1st `--reference`
is **image 0**, the 2nd **image 1**, etc. Cite the index in the prompt text:
- *"using **image 0**'s art style, draw a dog"* — style transfer onto a new subject.
- *"put the character from **image 1** into **image 0**'s scene"* — combine across references.

References go in the **`images` array** of `generate-image`. (The API's separate
`reference_image` param is *not* used — it routes to a different, sometimes-unpaid model.)

**Verified behaviour (tested live):**
- **Same person / character** — *"the same person as image 0, at a cafe with a laptop"*
  preserves her exact face, curly red hair and freckles in a brand-new pose and scene. Pass
  the same reference into **every** image to hold a character on-model across shots.
- **Same art style** — *"in image 0's art style, draw a car"* transfers the reference's
  palette and linework to a different subject (a pastel thick-outline house → a matching car).
- To draw a **new** subject rather than reproduce the reference's subject, say "image 0's
  **style/look**", not just "image 0".
- **Multiple references compose** — pass several and combine them: *"the character from image 0
  in image 1's art style"*, *"image 0 and image 1 standing together"*.
- Local **PNG/JPG/WebP** (auto-base64) and hosted URLs both work.

Examples:
```bash
strata generate image "using image 0's art style, draw a dog" --reference ./mascot.png
strata generate image "image 1's character standing in image 0's scene" --reference ./bg.jpg --reference ./hero.png
strata generate image "hero banner, brand palette" --aspect 16:9 --colors "#2563eb,#16a34a"
```

## `strata edit image <img|url> "<what changes>"` — an image that is 90 % right is EDITED, not re-rolled

```bash
strata edit image ./hero.png "the coffee cup and saucer are matte black instead of white" -o hero_v2.png
strata edit image <the url generate image printed> "the phone screen shows a large white 7:30 with Fri 03 beneath it"
```

There is no separate edit endpoint: this is **one `generate-image` call** with the source as
reference `image 0` and a prompt that tells the model to hold everything it was not asked to
change. The command supplies that wording (`image 0, identical in every way — the same framing,
the same composition, the same objects in the same places, the same lighting, the same grade and
the same depth of field. Only one change: …`), sends the source's own aspect ratio, and measures
the result against the source afterwards. **Local file or URL both work** — a local file is
base64-encoded, so an image already on disk needs no `upload`.

Reach for it whenever a generation is nearly there. Re-prompting from zero throws away the take
that was approved; an edit keeps it. The four jobs it does:

| the ask | the instruction |
|---|---|
| **remove** something — a prop that clutters the frame, a stray hand, a logo | *"remove the white coffee cup and saucer completely; show only the empty table where they were"* |
| **add** something — a prop, a shadow, a reflection | *"a small folded newspaper lies on the table in front of the phone"* |
| **change the copy** on a screen, a label, a sign, a package | *"the phone screen shows a large white 7:30 with Fri 03 beneath it"* |
| **change a material or colour** — a cup, a garment, a housing, the backdrop | *"the coffee cup and its saucer are matte black instead of white"* · *"isolated on a flat solid chroma green background, no shadow on the background"* (the cut-out route below) |

**What an edit actually costs you — measured.** Mean absolute luma difference from the source,
0–255, read in three regions the instruction never mentioned (one 896×1200 photograph, one hop
each). The floor for *the same image differing only by compression* is ≈3.3:

| the edit | drift in the untouched regions | reads as |
|---|---|---|
| the copy on the phone screen swapped | **1.5–5.1** (full frame 4.0) | the same photograph, new type |
| the coffee cup removed | **2.7–5.4** (full frame 9.4) | the same photograph, one thing gone |
| a newspaper added | full frame 14.5 | the same photograph plus a bright object (which lights the table) |
| the cup's material changed, white → matte black | **10.6–12.0** (full frame 20.4) | the same shot, re-graded a touch |
| **a second edit off the first edit** | **28.3 in a region never mentioned** | ❌ a different take — the camera pulled back and the grade warmed |
| **one object swapped FOR ANOTHER** (the succulent → a white orchid) | **40.2 full frame** | ❌ re-framed: the camera moved in, the phone grew, the cup shifted |

Four rules follow from those numbers:

- **Send the source's ratio — the command does, so do not fight it.** `aspect_ratio` defaults to
  `1:1` and the reference does **not** reliably override it: *measured*, the same edit of an
  896×1200 source came back **1024×1024, the composition re-cropped, drift 33.8** with the default,
  and **896×1200 at drift 2.5** once the ratio was sent. `strata edit image` reads the source's
  pixel size (ffprobe, or the PNG/JPEG header) and sends the nearest supported ratio; it prints
  what it used. `--aspect` overrides it, and a deliberate **re-crop is a different job** — say so.
- **One edit, off the ORIGINAL, with every change in it.** Chaining is where an edit becomes a
  re-roll (28.3 above). If two things must change, name both in one instruction.
- **Swapping one object for a different object is the edit most likely to re-render** (40.2 above)
  — it is really a removal plus an addition, and the model re-composes around the new thing. Name
  the invariants that matter in the instruction (*"the camera does not move, the phone stays exactly
  where and as big as it is"*), read the drift, and accept the take only if the number agrees.
- **Read the drift line the command prints.** Under ~5 it is the same picture; 10–15 is a re-grade
  you should look at; **over 15 the command warns, and it means the model re-rendered** — re-run
  naming the invariant that slipped (*"the camera does not move"*, *"the grade is unchanged"*).
- **The bytes may be JPEG.** *Measured:* edits of a PNG source came back at `.jpeg` urls. With
  `-o out.png` the CLI warns and gives the ffmpeg line; for anything that needs real alpha later,
  keep the returned extension or convert deliberately.

Extra `--reference` images are `image 1..n` (a logo, a palette board) and the instruction cites
them by index. `--raw` sends your prompt verbatim when you want to write the invariants yourself.
**Video is not this command** — a clip is edited, extended or restyled by the video model with
`generate video --ref-video` and its own `EDIT <Video_1>. Keep everything identical except …`
wording ([video-generation-advanced.md](../video-generation-advanced.md)).

---
