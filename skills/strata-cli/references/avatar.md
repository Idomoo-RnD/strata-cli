# Avatars — a talking presenter from ONE still

`strata generate avatar` turns a **single photograph + an audio track** into a lip-synced
presenter video. It runs on HeyGen's **Avatar IV** engine, which takes an **arbitrary
image** — not a passport headshot — and a `--motion` prompt that drives **body movement and
hand gestures**.

## When avatar, and when `generate video` instead

Avatar is **one still, lip-synced to an exact track** — a presenter holding a pose and
talking to camera. It is not the only way to make a person speak: `generate video
--ref-image --ref-audio` lip-syncs a character who **acts** — moves through a scene, several
shots, even two speakers answering each other
([the protocol](video-generation-advanced.md#--ref-audio--characters-that-speak-your-audio)).

- **Piece to camera, spokesperson, explainer host → avatar.** Cheaper (one still, one call),
  framing is fixed and predictable, and the audio track IS the output audio.
- **Anything PERSONALIZED → avatar.** Idomoo swaps media by layer name, so a stable presenter
  plate is what the swap targets; a re-generated performance is not a swappable plate.
- **Talking inside a scene, dialogue, a character who moves → `generate video`.**

**The image is the whole game.** The engine animates what you give it: a flat, front-facing,
brightly-lit crop produces a flat talking bust; an angled, cinematically-lit shot in a real
room produces something that reads as filmed. Most of this file is therefore about the
image prompt.

```bash
# the verified three-step chain — each step feeds the next
strata generate image "<cinematic presenter prompt>" --aspect 16:9      # -> prints an image URL
strata generate narration "<script>" --voice <voice_id>                 # -> prints an audio URL + duration
strata generate avatar "<image URL>" --audio "<audio URL>" --aspect 16:9 \
      --motion "explains with open hand gestures, warm and animated"
```

---

## The image — how to make one that doesn't look flat

**Ignore the "front-facing headshot, neutral expression" advice** you'll find in HeyGen's
docs: that belongs to their older *trained photo-avatar* flow, not Avatar IV.
**Verified working:** a three-quarter medium shot in an office, a side-turned half-body
mid-gesture under hard studio light, and a **seated low-angle in near-profile** with a teal
rim light. All three animated cleanly and kept their environment and lighting.

Build the prompt in HeyGen's own terms — *subject · setting · camera · mood* — and cover:

| | Do | Why |
|---|---|---|
| **Angle** | three-quarter / 45° turn, low angle, seated, looking back into the lens | a flat frontal crop is the "AI presenter" tell |
| **Framing** | chest-up minimum; **half-body or seated is better** | `--motion` needs a body to move; a head alone can only nod |
| **Hands** | **visible and FREE** — mid-gesture, resting on knees, holding something | measured: an arms-crossed source barely gestures at all, whatever you put in `--motion` |
| **Environment** | a real room with depth — office, studio, workshop, bokeh windows, a visible light stand | the video **fills the frame**, so this background IS your set |
| **Light** | key + rim, side light, deep shadows, practical lamps in shot | flat even light is what makes stock AI faces look fake |
| **Lens** | "85mm at f2", "50mm", "35mm", shallow depth of field | gives the model a photographic look to preserve |
| **Skin** | ask for "natural skin texture and pores" | counters the plastic over-smoothed look |
| **Eye-line** | into the lens for direct address; slightly off for reflective/intimate | |

**Match the image aspect to the video aspect** — generate `--aspect 16:9` for a 16:9 avatar,
`9:16` for vertical. Those are the only two the endpoint accepts (no 1:1).

**Avoid:** a flat front-facing crop · **arms crossed** · hands over the face · sunglasses ·
hair across the mouth · more than one face in frame · a full back-profile · low resolution ·
heavy motion blur on the face.

### Three shot recipes that worked
```
# 1 — corporate/credible, three-quarter medium
Editorial photograph of a confident woman in her early thirties, three-quarter turn toward
camera, medium shot from the chest up, standing in a bright modern office with softly blurred
windows behind her, warm key light from the left plus a subtle rim light on her shoulder,
shot on 85mm at f2, natural skin texture and pores, calm slight smile, looking into the lens

# 2 — energetic explainer, side-turned with gesture
…wider half-body shot, body angled 45 degrees away from camera with shoulders turned and head
looking back into the lens, one hand raised mid-gesture explaining, concrete-and-glass studio,
large softbox key from the right creating dramatic side light and deep shadows, 50mm,
cinematic teal-and-amber grade

# 3 — intimate/documentary, low angle seated
…low angle three-quarter shot from slightly below eye level, seated on a stool leaning forward
with forearms on her knees, dark studio, strong teal rim light behind her and a warm key from
the front left, shallow depth of field, 35mm, subtle film grain, intimate documentary mood
```

### The same presenter across a whole series
Pass the first image as a **`--reference`** and say *"the same woman as image 0"* — verified to
hold the face across different angles, lighting and framing. That is how a brand keeps one
recurring presenter over many videos while varying the shot. Record the chosen presenter
prompt + reference in **`.brand/brand.md`** ([brand.md](brand.md)) so later sessions reuse them.

---

## Mechanics (all verified against the live API)

- **Both inputs must be URLs the service can fetch.** A local file is **rejected** — the
  endpoint does not accept base64 data-URIs the way `generate image`/`generate video` do (it
  answers HTTP 500). Use the URLs those commands print. The CLI catches this and says so.
- **`--audio` is required** and defines the length: the output duration equals the audio's.
  `strata generate narration` prints exactly the URL you need.
- **The response tells you the duration** — `duration: 9.04s`. **Size the scene to it**, the
  same rule as narration.
- **`image_key`** comes back too. Pass it as `--image-key` on later runs to reuse the same
  uploaded photo (skips the upload; and it's how you make several videos of one presenter).
- **`--motion`** drives body/gesture. `--enhance-motion` asks the service to expand your
  prompt. Keep it consistent with the pose in the image — don't ask for big gestures from a
  seated, hands-resting shot.
- **`--start-silence` / `--end-silence`** pad the clip (end defaults to 0.3s) — useful to
  leave room for a logo sting or a cut.
- **Output observed: 1280×720 at 25 fps.** So **set the scene's `fps` to 25** (or conform the
  clip with ffmpeg per [video-editing.md](video-editing.md)) — a mismatch drifts lip sync.

## Using it in a scene
- **Full-frame presenter (the usual case).** The image was generated *with* a background, so
  the avatar fills the frame: one `video` layer at the full canvas, then compose over it —
  lower-third name bar, kinetic key phrases, logo bug, an end card. See
  [blocks.md](blocks.md) and [video-layouts.md](video-layouts.md) §8.
- **Presenter over a designed layout.** If you want the person cut out and placed on a brand
  background, matte them: `strata matte avatar.mp4 --width 640 -o presenter.jet` → use as a
  `.jet` overlay ([assets.md](assets.md)). Costs a matting pass, so only when the design
  needs it.
- **Don't cover the face.** Keep text in the opposite third from the head, and remember the
  subject may drift as they gesture — check a frame with `strata preview`/`snapshot`.
- The avatar clip carries its own audio; don't also add the narration as an `audio` layer or
  you'll double it.

## Rules
- **Never ship a flat front-facing crop** — angle it, light it, give it a room and free hands.
- **URLs, not local files**, for both `--audio` and the image.
- **Scene fps 25** and scene duration = the returned audio duration.
- **One face per source image.**
- Ask the user whose likeness this is. A real person's face needs their consent; if they
  haven't got a photo they own, generate a presenter instead.
