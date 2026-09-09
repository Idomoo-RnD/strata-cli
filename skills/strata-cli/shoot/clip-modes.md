# Modes, errors and the pre-flight checklist

The two unrelated things called "fast", what the errors mean, and what to confirm before spending a generation.

Part of the video-generation reference — the index, and every other part, is in [video-generation.md](../video-generation.md).

## Two different things are called "fast" — don't confuse them

| | What it is |
|---|---|
| **the fast model** (`generate video`, the **default**) | the same command and all its modes, on the fast Seedance model. ~1.5× quicker; it rejects anything above 720p (`400 InvalidParameter`), which is irrelevant under the 720p clamp. *Measured:* 4 of 5 requested shots delivered, so a piece that needs every shot passes `--best` |
| **`generate fastvideo <image>`** | a **different, older endpoint**. One image + a motion line → a clip |

**"Fast mode" means `generate fastvideo`**, used **only when the user or a workflow explicitly
asks for it** — never on your own initiative.

What `fastvideo` cannot do: no text-to-video (an image is required), no keyframes, no
references of any kind, no `--audio`, no `--last-frame-out`, no `--realistic-human`, and no
shot list — none of the prompt anatomy on this page applies to it. It **does** take a local
file (auto-encoded), which `generate video` does not.

Because it is a single continuous move with no cuts, a `fastvideo` clip is safe to matte into
a `.jet` overlay — but it gives you no control over the matte-friendliness of the shot, so the
source image has to carry that instead.

## Errors

A wrong `--model` fails as `500` wrapping `Seedance API 404: InvalidEndpointOrModel.NotFound`.
A wrong *parameter* also surfaces as `500` wrapping a `400`. **Always read the nested detail —
the outer status tells you nothing.** Validation errors from the CLI's own checks (frames +
references, `--realistic-human` misuse, a local path where a URL belongs) fail instantly and
cost nothing.

## Checklist

- [ ] Frame-driven **or** reference-driven — never both
- [ ] `Style & Mood:` opens it; one theme in CAPS
- [ ] Identity written physically and repeated **three** times (block, close-up, Static)
- [ ] Location has a foreground, a subject and a background that falls away
- [ ] Shots numbered with `Cut.` — no timecodes — each changing size **and** camera
- [ ] Body parts anchored in CAPS
- [ ] …unless it is a `.jet` overlay or a keyframe clip → **ONE continuous shot, no cuts**
- [ ] `Static Description:` restates set + identity; negatives stated
- [ ] `Audio:` specific, with `--audio` (omit both for a `.jet` overlay)
- [ ] Physical events, not adjectives
- [ ] ≤5 shots per 12 s (≤4 with references)
- [ ] Input has a real face, a logo or a real product → expect `--realistic-human`
- [ ] Source image already in the target aspect (`adaptive` snaps, it does not preserve)
- [ ] Shot list stays within the source framing, or the wider set is described explicitly
- [ ] `--last-frame-out` if this clip will be chained (24 h expiry)
- [ ] Run it in the background — 3–9 minutes
