# `strata upload` — the upload policy (temporary generation inputs only)

The upload endpoint is public, permanent and unauthenticated: anything sent to it is readable by
anyone with the URL, forever, with no delete. So it exists for exactly one purpose — handing a
generation INPUT that has no URL to `generate video` / `generate image`. Assets that already have a
URL (`generate` outputs, library assets, `.jet` files the IDM carries) are never uploaded. Read this
file before the first `strata upload` of a job; the asset workflow around it is in
[assets.md](assets.md).

## Contents

- [The rule](#the-rule)
  - [🔴 CRITICAL — the whole rule in one line](#critical--the-whole-rule-in-one-line)
  - [🛑 The two-condition test](#the-two-condition-test)
  - [Most generation inputs already have a URL](#most-generation-inputs-already-have-a-url)
  - [🛑 TEMPORARY assets only — never persistent ones](#temporary-assets-only--never-persistent-ones)
  - [When a URL is actually required](#when-a-url-is-actually-required)
  - [⚠ Public and permanent — which is why it is for temp use only](#public-and-permanent--which-is-why-it-is-for-temp-use-only)
  - [The extension must match the bytes](#the-extension-must-match-the-bytes)

## The rule

### 🔴 CRITICAL — the whole rule in one line

> **Upload only an asset we created ourselves that has no URL, and use the resulting URL
> only as an input to `generate` (image/video/avatar) — nothing else.** (`strata captions`
> uploads for itself when it must; that is the one other consumer, and it is inside the CLI.)

Both halves are binding:

- **What may be uploaded:** a file **we produced locally** (or one the user supplied) that
  **has no URL**. Never a generated asset — those already have one.
- **What the URL may be used for:** **a generation reference input only.** It goes into
  `--reference`, `first_frame`/`last_frame`, or `reference_images`/`reference_videos`/
  `reference_audio`. It does **not** go into a scene, a `src`, a deliverable, a brand file,
  or anything shown to the user as a link.

If you cannot name the `generate` call the URL is for, do not upload.

### 🛑 The two-condition test

Upload only when **both** are true:

1. the file is a **generation INPUT** — something handed to the AI API, which takes URLs:
   `generate avatar`'s image, and the video API's `first_frame`, `last_frame`,
   `reference_images`, `reference_videos`, `reference_audio`; **and**
2. it **has no URL** already.

If it is not a generation input, it does not need a URL at all. **Scene assets are local
paths** — images, MP4s, **`.jet` overlays**, fonts. The encoder reads their bytes at encode
time and embeds them in the `.idm`, so a `.jet` from `matte`/`jet` is never uploaded; it is
just a `src`.

### Most generation inputs already have a URL

**Everything `strata generate` produces is already hosted and prints its URL.** Read it off
the command's output and pass that string on. Uploading a generated file is always a
mistake: it costs an extra request, and it creates a second, permanent, public copy of
something that was already served.

```
✅ saved C:\…\presenter.png
   url: https://s3.us-east-1.amazonaws.com/assets-temp.idomoo.ai/images/…png   <-- USE THIS
```

| Asset | Already has a URL? | `upload`? |
|---|---|---|
| `generate image` (incl. `--reference` output) | ✅ prints `url:` | **no** |
| `generate video` | ✅ prints `url:` | **no** |
| `generate narration` / `generate music` | ✅ prints `url:` | **no** |
| `generate avatar` | ✅ prints `url:` | **no** |
| A rendered MP4 from `strata render` | ✅ prints `video:` / `poster:` | **no** |
| A file **the user gave us**, used as a reference or a first/last frame | ❌ | **yes** |
| Something **we built locally** and are using as a generation reference — a rendered animatic, an ffmpeg frame grab, a texture drawn by a script | ❌ | **yes** |
| A `.jet`, or any other **scene asset** (`src`) | n/a — embedded in the `.idm` | **never** |

The split is by **command**, not by luck — it is not "upload if the URL was missing":

| Returns a URL | Writes local files ONLY — never a URL |
|---|---|
| `generate image` · `video` · `avatar` · `narration` · `music` | `jet` · `matte` · `preview` · `compile` · `track` |
| `render` (`video:` + `poster:`) | anything from ffmpeg or a throwaway script |

Most of the right-hand column needs **no** URL, because its output is a scene asset that
gets embedded (`jet`, `matte`, `compile`) or is local-only by nature (`preview`, `track`).
They appear here only because *if* one of their outputs is later used as a generation
reference, it will need uploading first.

So the rule is: **capture the `url:` line when an asset is generated.** If a `generate`
command did not print one, something failed — investigate, do not paper over it with an
upload. Reach for `upload` only in the two cases that legitimately have no URL: **a file the
user supplied**, or **something produced by a local-only command** (a `.jet` from `matte`, an
ffmpeg frame grab or cut, a generated texture, a rendered animatic). Upload it once and reuse
that URL.

### 🛑 TEMPORARY assets only — never persistent ones

`upload` is a **transient handoff**: the one job is getting a local file into an API call
that will only accept a URL. It is **not** asset storage, not a CDN, and not where a
project's files live.

**Never upload:**

- **Scene assets — including every `.jet`.** A scene's `src` points at a **local file on
  disk**; the encoder reads the bytes at encode time and embeds them in the `.idm`. A `.jet`
  alpha overlay, an MP4 background, a PNG, a font: all local, all embedded. Uploading them
  and pointing `src` at a URL is wrong and gains nothing.
- **Deliverables** — finished MP4s, posters, anything the user is meant to keep. Those live
  in the project folder (and rendered videos are already hosted by `render`).
- **Brand assets** — a logo, a `.brand/` file, a font. Those belong in the repo.
- **Anything long-lived or referenced later.** Treat every uploaded URL as throwaway: good
  for this API call, not something to build on.

The irony is the point: the store itself is permanent and undeletable (below), so the
discipline has to be yours. **Temporary use, permanent consequence** — upload the minimum,
once, and only to feed a call that demands a URL.

### When a URL is actually required

Some endpoints take **only** a URL and reject base64 data-URIs — `generate avatar`'s image
is the one in this CLI today, and **`generate video`'s media inputs** (`--first-frame`,
`--last-frame`, `--ref-*`) are the other. `generate image --reference` and `generate
fastvideo` do **not** need a URL at all — they accept a local path and encode it themselves,
so never upload for their sake. And never upload for `generate video` either when the input
came from a `generate`/`render` command: that already printed a `url:` — use it.

```bash
strata upload footage_the_client_sent.mp4
# ✅ https://t.idomoo.com/9e289b70-…-7a1ed49e102f.mp4
#    1.30 MB · sniffed as video/mp4 · serving video/mp4
```

### ⚠ Public and permanent — which is why it is for temp use only

The endpoint is unauthenticated, the object is `public-read`, and there is **no expiry and
no delete**. Anything uploaded is world-readable forever — there is no way to take it back.
Every needless upload is a permanent public artefact, so the bar is: *this call will not
work without it.* **Never upload anything private,
personal or client-confidential** — and never a viewer's personalized data. Say so when
offering it; do not upload a user's file without asking.

### The extension must match the bytes

The host sniffs the real type from the file's first 261 bytes and **rejects a request whose
extension disagrees — with a bare `404` and an empty body** (measured: `.png` holding JPEG
bytes → 404; `.mov` holding MP4 bytes → 404; `.jpg` and `.jpeg` are interchangeable).

The CLI handles this: it sniffs the magic number locally and uploads under the type the
bytes actually are, telling you when it does:

```
⚠ named .png but the bytes are jpg — uploaded as .jpg
```

This matters because `generate image` sometimes returns JPEG bytes for a `-o …png` — so a
"png" on disk may not be one. The served `Content-Type` always follows the bytes.

The command also **verifies the URL serves** before handing it back (`serving image/png`),
because the documented failure mode is a successful upload whose URL then 404s.
