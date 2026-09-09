# The hard rules of `generate video`

What cannot be combined, what unlocks a rejected generation, and how media inputs must be addressed.

Part of the video-generation reference — the index, and every other part, is in [video-generation.md](../video-generation.md).

## 🚫 The one hard rule: frames and references are mutually exclusive

**`--first-frame` / `--last-frame` can NEVER be combined with `--ref-image`, `--ref-video`
or `--ref-audio`** — in any combination. The CLI blocks it before spending anything; the API
returns a `422`.

So decide before you write the prompt:

| | Gives you |
|---|---|
| **Frame-driven** (`--first-frame` / `--last-frame`) | exact control of the opening and/or closing frame |
| **Reference-driven** (`--ref-*`) | your character, world, camera plan or voice — but no exact frame |

Need both? Generate the reference-driven clip first, then **chain** off its last frame
(below) — the chained clip is frame-driven and inherits the look the references established.

## 🟢 `--realistic-human` — the answer to content rejections

Registers the input images in the Vault Asset Library before dispatch so photorealistic
people pass the privacy pre-filter; the registered assets are deleted when the task ends.

**The CLI applies it automatically** on a content rejection when the inputs qualify — one retry,
announced as `realistic_human was applied`. Pass it explicitly for **realistic humans** (any
recognisable face), **branded content** (logos, real products, brand marks), or after any
content/privacy/harmful error — the first fix, not a prompt rewrite. The rejection looks like:

```
Seedance API 400: InputImageSensitiveContentDetected.PrivacyInformation
"the input image 'content[1]' may contain real person"
```

*Measured* — the filter does **not** care that the person is AI-generated; a synthetic
portrait is rejected exactly like a photo:

| Input | Default | With the flag |
|---|---|---|
| Photoreal portrait as `--first-frame` | ❌ rejected | ✅ generates (180 s) |
| Same image, `--fast` | ❌ same error | — |
| Same image as `--ref-image` | ❌ same error | ✅ generates |
| `--first-frame` + `--last-frame`, both photoreal people | ❌ | ✅ generates, hits both keyframes |
| **Cartoon / stylized 3D character** | ✅ accepted | flag unnecessary |
| **Wide shot where the face is small** | ✅ accepted | flag unnecessary |

**Only applies to `--first-frame`, `--last-frame`, `--ref-image`.** Passing it with only
`--ref-video`, or with no media at all, is a hard error — and that is fine: *measured*, a
reference **video** full of photoreal people (including a face close-up) is accepted with no
flag at all, because the privacy pre-filter does not apply to video inputs. Products,
interiors, landscapes and face-free images are unaffected either way.

## Media inputs are URLs

`--first-frame`, `--last-frame` and every `--ref-*` take **URLs**; base64 data-URIs are
rejected. Most assets already have one — every `strata generate` command prints a hosted
`url:`, and so does `strata render`. **Use that string; never re-upload a generated asset.**

For a local input without a usable URL, read [upload policy](upload.md) before hosting. Only a
non-sensitive approved input may be published using `strata upload <file> --allow-public-upload`;
never customer-personalized/private footage. An unattended run cannot infer consent. Previously
`t.idomoo.com` URLs were accepted for image/video references; availability does not prove permission.
Scene assets stay local — see [assets.md](../assets.md).
