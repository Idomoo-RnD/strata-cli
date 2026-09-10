# Writing the prompt

How to describe a shot so the model produces it, with a worked example end to end.

Part of the video-generation reference — the index, and every other part, is in [video-generation.md](../video-generation.md).

## Writing the prompt

A prompt is a **shot list with an identity lock**, not a sentence. All of these sections,
in this order:

The **vocabulary** for the shots themselves — a named angle, move, light, composition or look, each with a
prompt that produces it — is [techniques.md](../techniques.md): pick one technique per axis and paste its
template into the shot line. This page is the structure around it.

### 1. `Style & Mood:` — one line
Genre, tone and lighting together: *"naturalistic documentary portrait, cinematic, warm
morning chiaroscuro — hard low sun raking through old glass against a deep unlit interior."*

### 2. One theme, in CAPS
*"This video is about STILLNESS and LIGHT."* One idea disciplines the motion; two produce mush.

### 3. Identity lock — state it THREE times
Physical detail, never adjectives:

> early thirties, pale skin with visible pores and light freckles across the nose, no makeup,
> dark brown shoulder-length curly hair worn loose and slightly frizzy, straight dark
> eyebrows, calm grey-green eyes. Loose olive-green linen shirt, sleeves pushed to the elbow,
> dark indigo jeans, small speckled cream mug in her right hand.

Then repeat the invariants **inside the close-up shot** and **again in the Static
Description**. Never assume the model remembers.

### 4. Location — one paragraph with depth
A foreground plane, the subject, and a background that falls away. Name the light source and
where it dies. Depth is what makes camera moves read as real.

### 5. Numbered shots with `Cut.` — never timecodes

> ⛔ **Skip this entirely for a clip destined to be matted into a `.jet` overlay, and for
> keyframe interpolation** — both are ONE continuous shot. See the `.jet` rule below.

`Shot 1: … Cut. Shot 2: …`, and two rules:

- **Double contrast** — every shot changes **both** shot size and camera character: wide
  low-angle → macro → face ECU → orbiting medium → wide push-back.
- **Anchor body parts in CAPS** — "close-up on her HANDS only", "her FACE filling the frame".
  Without this the framing drifts to generic mid-shots.

Add a hold on the face shot: *"hold this shot long enough to clearly read her face."*

> ⚠️ **Montage verbs cut the clip even when there is no shot list.** *"Cut to"*, *"then we
> see"*, *"meanwhile"*, *"next"*, *"later"*, *"intercut"* are read as edit instructions
> **wherever they appear**. *Measured:* a prose brief with no `Shot N:` and no `Cut.`, containing only "We see her
> unlock the door. Cut to her flipping the sign." returned **3 hard cuts in a 5 s clip**
> (scene scores 0.47 / 0.60 / 0.65). Describe **one continuous action** instead — *"she
> unlocks the door, crosses to the counter and starts the machine"*. And whenever a clip is
> meant to be a single take, **say so explicitly**: the identical prompt plus `ONE single
> continuous shot, one unbroken take, no cuts` returned **0 cuts** (max scene score 0.07).
> This is not only a `.jet` rule — it applies to **any** clip you intend as one shot.

### 6. `Static Description:` — the invariants
Set + light + identity that must hold in **every** shot. This is the line that stops wardrobe
and room mutating between cuts. End it with the negatives you need ("no text on screen").

### 7. `Audio:` — always present, always specific
Even for near-silence: *"soft slow breathing, the faint creak of an old window frame, a
distant city hum. No music. No speech."* Needs `--audio`.

### 8. Physics, not adjectives
"dust motes drift in the beam", "thumb moving slowly over the glaze", "linen folds catching
and losing the light". Concrete physical events are what the model can animate. "Beautiful"
is not.

### What to expect *(measured)*

A 12 s, 5-shot prompt returned **exactly 4 cuts → the 5 shots requested**, each on brief
(wide at the window → macro on HANDS → face ECU in profile → medium torso → wide push-back),
and identity held across all five. But shot lengths came back **uneven** — 1.38 / 2.21 / 1.96
/ 3.33 / 3.22 s. The model paces the edit itself; it does **not** divide the duration evenly,
so never plan on `duration / shots`. The fast model on the same prompt delivered **4 shots**.

**Budget ~5 shots per 12 s** (≤4 when references are attached) — asking for more merges or
drops them.

---

## Worked example — the prompt that produced the result above

```text
Style & Mood: naturalistic documentary portrait, cinematic, warm morning chiaroscuro -
hard low sun raking through old glass against a deep unlit interior. This video is about
STILLNESS and LIGHT.

The woman is the same person in every shot: early thirties, pale skin with visible pores
and light freckles across the nose, no makeup, dark brown shoulder-length curly hair worn
loose and slightly frizzy with fine flyaway strands catching the light, straight dark
eyebrows, calm grey-green eyes, a soft unsmiling mouth. She wears a loose olive-green
linen button-down shirt with the sleeves pushed to the elbow, tucked into dark indigo
jeans, and holds a small speckled cream ceramic mug in her right hand. Her face,
freckles, curly hair and olive linen shirt must be identical in every single shot.

Location: the corner of a quiet red-brick loft in early morning. A tall wooden sash
window with peeling white paint fills the left of the frame, hard sunlight pouring
through it and falling off fast into a deep shadowed interior of exposed brick, a wooden
shelf and large monstera and fiddle-leaf plants sinking into darkness. Dust motes drift
slowly through the beam.

Use five distinct shots with slow deliberate cuts, each changing both shot size and
camera character. Shot 1: full-body wide shot from inside the dark room looking toward
the window, she stands three-quarters to camera in the pool of light, one HAND resting
flat on the window frame, breathing slowly, linen shifting on her shoulder. Cut.
Shot 2: tight macro close-up on her HANDS only, the fingers of her right hand wrapped
around the warm ceramic mug, thumb moving slowly over the glaze, faint steam rising and
bending in the light, sunlight rimming her knuckles against black. Cut. Shot 3: EXTREME
CLOSE-UP on her FACE filling the frame in profile, the same freckles, grey-green eyes and
dark curly hair, eyes half-closed then slowly opening, a single flyaway strand drifting
across her cheek, hard side light sculpting her brow and cheekbone against a pitch-black
background - hold this shot long enough to clearly read her face. Cut. Shot 4: medium
shot on her upper BODY from a slow orbiting camera, shoulders turning gently as she lifts
the mug toward her chest, linen folds catching and losing the light, plants drifting past
behind her in soft focus. Cut. Shot 5: full-body wide again from a different side angle,
slow camera push back, she turns her HEAD to look out through the glass and settles into
stillness, the beam and drifting dust holding as the final frame settles.

Static Description: brick loft corner at early morning, single hard window key light from
the left, deep falloff to black, volumetric haze and drifting dust, shallow depth of
field; the same woman throughout - dark curly hair, freckles, grey-green eyes, olive linen
shirt, indigo jeans, speckled cream mug - identical in every single shot.

Audio: near silence - soft slow breathing, the faint creak of an old window frame, a
distant muffled city hum outside the glass, the small ceramic sound of the mug set down.
No music. No speech. Slow, unhurried pacing throughout.
```

---
