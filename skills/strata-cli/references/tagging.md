# Tagging — making a scene reusable

A **tagged** scene carries a manifest saying what it is FOR and which layers are
swappable. A downstream mix-and-match system reads it to combine scenes into a video
and drop each viewer's content into the right slots. Untagged, a scene is a one-off.

Tag a scene when the user asks for it — "tag this", "make it a template", "add it to
the catalog", "so we can reuse/recombine it". Otherwise skip it entirely.

```bash
strata validate <scene.json> --tags manifest.json          # dry run — check the manifest
strata compile  <scene.json> --tags manifest.json -o Light_Hero_Cta_01.idm
strata render   <scene.json> --library <id> --tags manifest.json
```

`--tags` embeds the manifest **inside** the `.idm` as an asset named `tag.ttf` and (on
`compile`) writes the sidecar `<out>.json` beside it. `render --tags` uploads the scene
tagged; Idomoo then echoes each placeholder's tags back in the `--emit-timeline` body,
which is how you confirm it landed.

Read the manifest back out of any `.idm` with `strata inspect <file.idm> --assets <dir>`
— a file named `tag.ttf` appears next to the real assets; its content is the manifest.

---

## The manifest

```json
{
  "ae_scene_name": "Light_Hero_Cta_01",
  "description": "",
  "tags": ["media", "text", "cta"],
  "colors": [
    { "tag": "color 1", "hex": "#3D0C8B", "role": "ink" },
    { "tag": "color 2", "hex": "#FF00FF", "role": "accent" },
    { "tag": "color 3", "hex": "#F0F0FF", "role": "surface" }
  ],
  "media": [
    { "key": "heroImage", "description": "", "tags": ["media 1"] }
  ],
  "text": [
    { "key": "headline", "description": "", "tags": ["text 1", "font 1", "color 3"] },
    { "key": "ctaLabel", "description": "maximum 4 words", "tags": ["button text", "font 2"] }
  ]
}
```

`protected_regions`, `audio` and `transition` are **auto-stamped** — leave them out.
`colors` entries for `color 1..3` plus any color you reference are always emitted.

| Field | What to write |
|---|---|
| `ae_scene_name` | The **GENERIC template name** (`Light_List_Left`, `Dark_Stat_Center`) — *never* the brand, product or topic. This is a catalog entry, not this video. |
| `description` | **Normally empty.** Tags are the instructions. Use it only for a hard constraint the tags cannot express ("only for credit-card topics", "must follow the step-1 scene"). Less is better. |
| `key` | The **exact layer name** in the scene. Case-sensitive, must match. |

---

## The two closed vocabularies

Both are CLOSED — anything else is rejected at compile. Tags are **instructions**, not
descriptions: do not invent "hero shot", "bold", "left side".

**Scene tags** (root `tags`, at least one):

| Tag | Meaning |
|---|---|
| `intro` | May only appear FIRST |
| `logo` | Appears LAST (before a `cta` scene if one exists) |
| `fade to black` | Last scene, fades out |
| `media` / `text` | Contains media / text placeholders |
| `media full frame` | Media is the main focus (full-bleed) |
| `data` | Percentages & numbers |
| `avatar` | Contains an avatar slot |
| `list` | A list of items in text |
| `cta` | Has a CTA button; usually last |
| `dontcut` | Must play in full — never shortened |
| `noteditable`, `button` | Legacy, rarely used |

Combine what applies: a text+media middle scene is `["media", "text"]`.

**Placeholder tags** (inside `media[]` / `text[]`):

```
media 1, media 2, …    sequential media index
text 1, text 2, …      sequential text index
font 1 | font 2 | font 3
color 1 … color 7
allcaps                renders in ALL CAPS
button text            a CTA button's label
logo                   the brand logo goes here
avatar                 an avatar goes here
folder:<name>          fill only from that workspace folder
```

- Every `media[]` entry needs `media N` **or** `logo` **or** `avatar`.
- Every `text[]` entry needs `text N` **or** `button text`.
- `font N` / `color N` / `allcaps` are optional refinements.

### The reuse rule (the one that bites)

**The same index tag on two placeholders means the SAME content in both.** `media 1` on
two layers = the same image twice. `text 2` on two layers = the same string repeated.
Three list rows that hold different copy need `text 1`, `text 2`, `text 3` — they may
still share `font 1` and `color 1`, because styling tags are not content tags.

---

## Coverage — what must be documented

- **Every text layer** must appear in `text[]`. No exceptions.
- **Every media layer** must appear in `media[]` — *except baked decor*, which should be
  **left out**. Documenting decor tells the downstream system it is replaceable, and your
  gradient scrim comes back as someone's product photo.

Decor = a media layer whose asset is a generated gradient (`grad_*`), a decor sprite
(`sprite_*` or under `/sprites/`), **any video asset** (baked i2v footage and `.jet`
alpha overlays), or `tag.*` itself.

Solid layers are neither text nor media — they are never documented.

---

## Colors are corrected, not trusted

`strata` reads each text layer's **actual compiled colour** and fixes the tag: nearest
brand hex within RGB distance 40 wins (so `#FFFFFF` matches a `#F0F0FF` surface). A wrong
tag is replaced, a missing one added, and text in no brand colour has its colour tags
stripped. Every change is printed:

```
🎨 text "ctaLabel": color tag added "color 3" (layer renders #FFFFFF)
```

So do not agonise over colour tags — get the hexes in `colors[]` right and let the
truth-up settle the rest. Keep `color 1` meaning the same hex across a whole catalog,
or downstream recolouring by tag will be inconsistent.

---

## Naming the output

The sidecar pairs with the `.idm` by basename, so compile to the **catalog name**:

```bash
strata compile scene.json --tags manifest.json -o Light_Hero_Cta_01.idm
# -> Light_Hero_Cta_01.idm + Light_Hero_Cta_01.json
```

Compiling to the default `scene.idm` would put the sidecar at `scene.json` — on top of
the scene source — so strata refuses and tells you to name it. Use `--tags-out <path>`
if you need the sidecar somewhere else.

---

## Checklist before shipping a tagged scene

- [ ] `ae_scene_name` is a generic template name, not the brand or topic
- [ ] `description` empty unless a real constraint exists
- [ ] every text layer in `text[]`; every non-decor media layer in `media[]`
- [ ] decor (gradients, sprites, video, `.jet`) NOT documented
- [ ] indices sequential and only shared where the content really repeats
- [ ] `strata validate <scene> --tags <manifest>` clean
- [ ] rendered with `--tags --emit-timeline` and the tags came back in the timeline
