# Video prompt cookbook — worked prompts by kind of video

The anatomy in [video-generation.md](video-generation.md) says how a prompt is *built*;
this file is what it looks like *for each kind of video Idomoo makes*. **Pick the entry
nearest the job, keep its skeleton, swap the subject.** Every entry states its shot budget
and what was measured when it was generated. Entries marked **pattern** follow the same
anatomy but were not individually rendered — their mechanics are measured in the linked
sections.

## Shot budget by duration

The model paces cuts itself and never divides time evenly (*measured:* a 12 s / 5-shot ask
returned 1.4–3.3 s shots). Budget requests, not timings:

| Duration | Max shots | Typical use |
|---|---|---|
| 4–5 s | 1–2 | sting, hook, insert |
| 8 s | 3 | product hero, food, testimonial |
| 10–12 s | 4–5 | B-roll set, lifestyle, montage |
| 15 s | 5–6 | music-choreographed, mini-story |

With references attached, subtract one. Asking for more merges or drops shots.

---

## Product hero — 3 shots / 8 s ✅ measured

Wide on a pedestal → EXTREME MACRO on the detail → three-quarter push-in to a specular
flash. One hard key, deep falloff, `--audio` for the object's own sound. *Measured: 3/3
shots, identity held, real tick on the audio track.*

```text
Style & Mood: premium product film, cinematic, hard key light with deep soft shadow, matte
charcoal sweep background. This video is about PRECISION and WEIGHT. The product is a
brushed-steel chronograph watch with a black dial and amber second hand; the same watch,
identical in every shot. Use three distinct shots with slow deliberate cuts, each changing
shot size and camera character. Shot 1: wide product shot, the watch standing on a dark
slate pedestal, a slow lateral dolly right, a single hard beam raking across the dial.
Cut. Shot 2: EXTREME MACRO on the DIAL only, the amber second hand ticking, light catching
the brushed texture, shallow depth of field. Cut. Shot 3: medium three-quarter shot, slow
push-in as the beam widens and the watch face turns just enough to catch one clean
specular flash, settling to stillness. Static Description: matte charcoal seamless
background, dark slate pedestal, one hard key from upper left, deep falloff, no text on
screen, the same brushed-steel chronograph with black dial and amber second hand in every
shot. Audio: a near-silent room, one soft tick per second, the faint hum of a studio lamp.
No music. No speech.
```

For a *real* product, put its sheet in `--ref-image` and add `--realistic-human` if it
carries a logo ([video-generation-advanced.md](video-generation-advanced.md)).

## Logo / brand sting — 1 shot / 5 s ✅ measured

ONE take, no cuts (matte-ready), locked camera, **only the light moves**. *Measured: 0
cuts, camera held.*

```text
Style & Mood: minimal brand sting, studio-lit, deep charcoal seamless. This video is about
ONE CLEAN REVEAL. A matte white ceramic tile bearing an embossed circular seal mark sits
centred on dark slate. ONE single continuous shot, one unbroken take, no cuts: the tile
lies flat in shadow, a single hard beam sweeps across it left to right once, the embossed
mark catching the light as the beam passes, then the light settles into a soft even key
and holds. LOCKED-OFF CAMERA: static tripod, no pan, no tilt, no zoom, no drift; the frame
edges never move. Only the light moves. Static Description: charcoal seamless, dark slate
surface, matte white tile with embossed circular seal, no text on screen. Audio: one low
soft whoosh as the beam passes, then room tone. No music. No speech.
```

Swap the tile for the real mark via `--ref-image` (+ `--realistic-human` — logos trip the
pre-filter). Generate on green if it will be keyed.

## Food / beverage — 3 shots / 8 s ✅ measured

The physics vocabulary IS the prompt: pour, ribbon, coil, drip, crumb, steam, dust.
*Measured: 3/3 shots, the honey ribbon coiled, one drip formed and fell on cue.*

```text
Style & Mood: appetite film, warm daylight kitchen, shallow depth of field. This video is
about POUR and STEAM. Use three distinct shots with deliberate cuts, each changing shot
size and camera character. Shot 1: macro side-on, thick golden honey pouring in a single
unbroken ribbon onto a stack of dark rye pancakes, the ribbon coiling as it lands. Cut.
Shot 2: EXTREME MACRO on the pancake EDGE only, honey creeping over the crumb, one slow
drip forming and falling, steam rising through backlight. Cut. Shot 3: medium overhead,
slow push-in on the full stack as a dusting of powdered sugar drifts down like snow and
settles. Static Description: warm daylight from a side window, dark wood table, stack of
dark rye pancakes on a stoneware plate, golden honey, drifting steam, no people, no text.
Audio: the soft liquid sound of the pour, a faint kitchen room tone. No music. No speech.
```

## Vertical social hook — 2 beats / 5 s / 9:16 ✅ measured

Decisive action in frame ONE, and the ending returns to the opening state so the loop
reads as continuous. *Measured: the slam opens the clip; the tilt is one move, not a cut.*

```text
Style & Mood: vertical social, handheld energy, hard flash-lit contrast. This video is
about the DROP. Shot 1: EXTREME CLOSE-UP, a matte black sneaker sole SLAMS down onto wet
asphalt filling the frame, water bursting outward from the impact in slow crisp droplets.
Cut. Shot 2: fast tilt up the same sneaker to a low-angle medium of the runner already
mid-stride away into neon-lit rain, back to camera. The clip ends on the runner mid-stride
so the loop back to the slam reads as the next footfall. Static Description: night street,
wet asphalt, scattered neon reflections, the same matte black sneaker with a gum sole in
both shots, no text on screen. Audio: one deep impact hit on the slam, then rain and
distant city. No music. No speech.
```

## Place / establishing — 1 take / 6 s ✅ measured

One continuous aerial move that *arrives* — eases lower, slows, settles. Never an orbit.
*Measured: 0 cuts, the glide settled on the lighthouse as asked.*

```text
Style & Mood: establishing aerial, golden hour, calm and vast. This video is about
ARRIVAL. ONE single continuous shot, one unbroken take, no cuts: a slow high drone glide
forward over a small coastal town at golden hour, terracotta roofs and a whitewashed
lighthouse on the point, long shadows across the harbour, small boats leaving thin wakes,
the camera easing gently lower and slowing as it approaches the lighthouse, settling as
the final frame holds on the point against the open sea. Static Description: coastal town,
terracotta roofs, white lighthouse, golden hour, calm sea, no people close to camera, no
text. Audio: wind, distant gulls, faint harbour bells. No music. No speech.
```

Chaining off this clip? Ambient life (boats moving, gulls) must be **re-stated** in the
continuation — events do not chain ([video-generation.md](video-generation.md)).

## Lifestyle with a real person — 5 shots / 12 s ✅ measured

The canonical worked example (woman at the loft window) lives in
[video-generation.md](video-generation.md) — identity lock ×3, double contrast, the hold
on the face. Use it verbatim as the skeleton.

## Talking-head testimonial — 2 shots / 8 s · pattern

The lip-sync recipe in [video-generation-advanced.md](video-generation-advanced.md) is the
measured mechanics (job in caps → demand the sync → the `{line}` in braces → a held
close-up on the MOUTH). Structure it as: Shot 1 medium, she finishes an action and turns
to camera; Shot 2 the held close-up carrying the line. For a plain to-camera piece with no
world around it, `generate avatar` is the better tool — the decision table is in the same
file.

## Explainer B-roll — 4 shots / 10 s · pattern

No faces, cuts on action, each shot one concrete verb: hands typing, a screen scrolling
(describe the UI generically — on-screen text will be invented), a device set down, a
detail insert. Same double-contrast rule; end every shot description with the action
*completing*, so the cut lands on it. Physics vocabulary from the food entry applies to
objects too.

## Cartoon / stylized character — 4 shots / 10 s · pattern

Character sheet in `--ref-image` with an explicit JOB line ([Image 1] is the definitive
reference…), no `--realistic-human` needed — cartoons pass the pre-filter (*measured*, see
[video-generation.md](video-generation.md)). Identity lock still ×3: sheets constrain
less than you hope.

## Music-choreographed — 5 shots / 15 s · pattern (mechanics measured)

The cut-on-the-beat pattern in [video-generation-advanced.md](video-generation-advanced.md):
track as `[Audio 1]` with its job declared first, motion tied to phrasing in CAPS, every
shot ending `Cut on the beat.`, the last landing on the closing phrase.

## Chained continuation · pattern (mechanics measured)

Not a kind of video — the way any of the above gets longer. `--last-frame-out` → upload →
`--first-frame`, restating the identity lock, the Static Description **and the ambient
events** ([video-generation.md](video-generation.md), Chaining). A clip that came back
short is covered with a companion clip, never stretched.
