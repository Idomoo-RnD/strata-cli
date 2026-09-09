# Depth, light and atmosphere

Part of [craft.md](../craft.md). Choose flat graphic, illustrative or spatial treatment in the
[design contract](../make/design-contract.md). This page teaches spatial compositing when that
choice earns it; it does not require every frame or wordmark to simulate a physical object.

### Depth, light & atmosphere

**Flat graphic work:** hierarchy comes from scale, space, color, typography and movement. A mark
may remain exact flat color with no contact shadow, textured fill, perspective or occlusion.
Overlay titles over footage can deliberately remain graphic rather than pretending to be in-world.

**Spatial work:** identify key light direction/color, relative depth and material. A title meant
to occupy the photographed set should share plausible light, focus and occlusion with it. Useful
options—not a mandatory stack—include:

- Sample the plate's lighting and use a color overlay or adjusted fill without sacrificing reading.
- A contact shadow where an object actually meets a surface, with a direction coherent with the key.
- Texture/light movement appropriate to the depicted material rather than an arbitrary scrolling band.
- Same-source occlusion when a subject must pass in front of type; see
  [video-layouts.md](video-layouts.md). Do not pay for matting if nothing overlaps.
- Matched edge sharpness, focus falloff and grain when compositing into photographed footage.

A level texture band across letters is valid graphic fill. It is a mismatch only if the direction
promised physically integrated lighting. Similarly, a uniform grade can be a deliberate brand
system; changing grades between every beat is not a requirement. Wide negative-space frames and
close-ups should carry information, not satisfy a variety quota.

Choose atmosphere for the subject: neon can glow, an analogue treatment can have grain. Subtract
unnecessary effects; a declared maximalist direction can combine them coherently.
[Anti-slop](anti-slop.md) tests whether the choice belongs to this subject, not whether the feature
is forbidden.

**Motion blur:** visual layers default on; the camera's default is false, so explicitly set
`"motion_blur": true` on moving cameras when the chosen treatment needs blur. A deliberately crisp
or stepped treatment may opt out with its reason. Check fast frames and occluder edges: a broad
shutter can reveal content during a wipe. The historical 360° occluder probe leaked type at its
leading edge; default shutter or a different concealment construction avoids assuming blur hides it.
Verify the actual frames rather than maximizing shutter for “quality.”
