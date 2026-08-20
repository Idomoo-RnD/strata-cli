# Getting speech timing from an asset — `strata captions`

Transcribes speech **with timings** from an audio *or* video asset. Use it whenever the
scene needs to know **when words are spoken**: caption/subtitle bars, lower-thirds that
appear on a line, cutting to the beat of a VO, sizing a shot to the narration, or checking
that a generated clip actually said what it was asked to say.

```bash
strata captions <file|url> [-o cues.json] [--srt subs.srt] [--raw cc.txt] [--json]
```

```
$ strata captions ./talk.mp4 -o cues.json
  0.54s - 4.76s  Our platform turns every customer into an audience of one
✅ 1 cue(s), speech ends at 4.76s
```

`-o` writes the cues as JSON, ready to drive layer timing:

```json
[ { "t0": 0.539, "t1": 4.759, "text": "Our platform turns every customer into an audience of one" } ]
```

`--srt` writes standard SRT (for burning subtitles with ffmpeg, or handing to an editor).
`--raw` keeps the API's own file. `--json` prints everything machine-readable.

---

## ⚠️ SEGMENT timings, not per-word

Each cue is a **phrase or sentence**, not a word. That is the single most important thing
to know before designing around it.

- ✅ **Good for:** caption/subtitle bars, a lower-third that appears while a line is spoken,
  holding a shot for the length of a sentence, knowing when the VO ends.
- ❌ **Not good for:** per-word kinetic text, karaoke-style highlighting, one-word-per-cut
  editing.

There is no cheap way to subdivide a cue into words — *measured* against hand-authored
ground truth: splitting a segment proportionally by character count is **118 ms mean / 501 ms
worst**, and snapping those estimates to `strata beats` onsets is **124 ms / 407 ms**. Half a
second out is plainly wrong on screen. If a piece truly needs per-word timing, **author the
word times by hand** (a small `[{w,t0,t1}]` file) rather than pretending these cues are
word-accurate.

`strata beats` is not an alternative here: it finds **energy onsets**, not words —
*measured* on speech it produced 18 onsets for 13 words with no text at all. Use it for
rhythm, not for language.

## It takes VIDEO directly

No extraction step. Point it at an `.mp4` and it transcribes the speech inside it. Only
reach for ffmpeg if you want the audio for something else.

## Any source works — the hosting is handled

The underlying API only accepts a real S3 URL, but the command absorbs that:

| You have | What happens |
|---|---|
| A URL from `strata generate` (narration, video, avatar) | used as-is — already S3 |
| A **local file** (yours or the user's footage) | uploaded, then addressed in S3 form |
| A `t.idomoo.com` URL (something already uploaded) | rewritten in place — **no second upload** |
| Any other URL (e.g. a `v.idomoo.com` render) | downloaded, then hosted |

⚠️ **Uploading publishes.** The store is public, permanent, unauthenticated and has no
delete. The command says so when it uploads. **Never caption anything private or
client-confidential**, and prefer passing a URL that already exists over re-uploading.
A file with **no audio track at all** is rejected *before* any upload happens.

## Typical uses

**Caption the narration you just generated** — no upload, the URL is already good:
```bash
strata generate narration "<script>" --voice <id>     # prints url:
strata captions "<that url>" -o cues.json
```

**Check a generated clip said the right line.** Seedance speaks the line written in
`{curly braces}` in the prompt ([video-generation-advanced.md](video-generation-advanced.md));
this is how you verify it, rather than listening:
```bash
strata captions ./clip.mp4          # compare the transcript to the line you asked for
```

**Time a caption bar to the VO.** Each cue becomes a text layer's `start` and `duration`:
`start = t0`, `duration = t1 - t0`. Keep the bar in the lower safe area
([layouts.md](layouts.md)), and remember captions exist because **most social video is
watched muted** — they are not optional decoration.

**Transcribe user-supplied footage** to find the quotable moment before cutting it
([video-editing.md](video-editing.md)).

## Notes

- Runs in a few seconds — it is not a long async job like image or video generation.
- Repeat calls on the same source return the same caption URL.
- The API writes its file next to the source and hands back a URL; the command fetches and
  parses it for you. The format is VTT-*like* but not VTT (cues are prefixed `//` and the
  text wraps inside a cue), so parse it with this command rather than a stock SRT/VTT reader.
- **Empty transcript** means no intelligible speech was found — music and ambience return
  nothing. The command fails rather than writing an empty cue list.
