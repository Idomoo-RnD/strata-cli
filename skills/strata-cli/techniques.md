# Techniques — 150 film techniques as prompts

A lookup library, not a reading list. Every technique is one page: its film history, a prompt template
with `[Subject]` as the placeholder, when to use it, how to direct a generator toward it, and the three
mistakes that usually break it. Open **one** page for the technique in hand; nothing here is read end to end.

## How to use it

1. **Find the technique** in the table below — `grep -i` this file for an angle (*low angle*), a mood (*unease*),
   a lighting word (*Rembrandt*), a director (*Villeneuve*) or a genre (*noir*). The *use it when* column answers
   "which one" as well as "where".
2. **Open that page** — about 750 words. *Directing the generator* is what turns a name into a prompt that lands.
3. **Paste the template** into the shot line of the generation prompt ([clip-prompting.md](shoot/clip-prompting.md),
   step 5), with `[Subject]` replaced. The identity lock, location and audio sections of that prompt are unchanged.

## Combining techniques

- **One decision per axis.** A shot has a camera position, a light and a composition — pick one technique from each,
  not three from one. Two camera techniques in one prompt usually means the generator obeys neither.
- **A genre or style page sits above the others.** It sets the palette, stock and grammar that the camera, lighting
  and composition choices then serve; read it first when a brief names a look.
- **A technique is one decision.** Do not stack a second distortion on it — a Dutch angle plus a fisheye plus a
  crash zoom is noise, not intensity.
- **Video vs image.** Every template works for both. For video, *Directing the generator* says whether the camera
  is locked or moving — follow it; uncontrolled drift reads as a generation error, not as direction.

## What is here

| folder | category | pages |
|---|---|---|
| `techniques/camera/` | Camera Work | 41 |
| `techniques/lighting/` | Lighting | 30 |
| `techniques/composition/` | Composition | 21 |
| `techniques/editing/` | Editing | 17 |
| `techniques/storytelling/` | Storytelling | 12 |
| `techniques/fx/` | Visual Effects & Promptable FX | 8 |
| `techniques/genres/` | Genres & Styles | 21 |

150 pages. Levels: Basic · Intermediate · Advanced.

## Camera Work (41)

| technique | level | use it when | page |
|---|---|---|---|
| **Aerial Shot** | Intermediate | Choose an aerial shot when geography matters as much as the subject. | [aerial-shot.md](techniques/camera/aerial-shot.md) |
| **Bird's Eye View** | Intermediate | Use a bird's eye view when the scene contains a pattern that ground-level framing would hide. | [birds-eye-view.md](techniques/camera/birds-eye-view.md) |
| **Close-Up** | Basic | Use a close-up when a face, gesture, or object carries the meaning of the scene. | [close-up.md](techniques/camera/close-up.md) |
| **Dutch Angle** | Basic | Use a Dutch angle when the world, character, or power relationship has slipped out of balance. | [dutch-angle.md](techniques/camera/dutch-angle.md) |
| **Dolly Shot** | Intermediate | Use a dolly-in when a realization, threat, or emotional detail needs to become unavoidable. | [dolly-shot.md](techniques/camera/dolly-shot.md) |
| **Establishing Shot** | Basic | Open with an establishing shot when viewers need to understand where the scene happens, what time or weather shapes it, and how the key spaces connect. | [establishing-shot.md](techniques/camera/establishing-shot.md) |
| **Extreme Close-Up** | Basic | Cut to an extreme close-up when one tiny detail must carry the scene: a pupil changing, a finger tightening, sweat forming, or a needle touching skin. | [extreme-close-up.md](techniques/camera/extreme-close-up.md) |
| **Extreme Long Shot** | Basic | Use an extreme long shot when the environment should overwhelm the person inside it. | [extreme-long-shot.md](techniques/camera/extreme-long-shot.md) |
| **Eye-Level Shot** | Basic | Choose eye level when the camera should meet a person without glorifying, diminishing, or judging them. | [eye-level-shot.md](techniques/camera/eye-level-shot.md) |
| **Handheld Shot** | Basic | Reach for handheld movement when the camera must feel physically present inside an unstable moment. | [handheld-shot.md](techniques/camera/handheld-shot.md) |
| **Head-On Shot** | Intermediate | Use a head-on shot when a character must confront the audience or advance with undeniable force. | [head-on-shot.md](techniques/camera/head-on-shot.md) |
| **High Angle Shot** | Basic | A high angle is useful when the viewer should understand both a subject and the forces surrounding them. | [high-angle-shot.md](techniques/camera/high-angle-shot.md) |
| **Insert Shot** | Basic | Use an insert when the audience must notice a detail that changes how the scene is understood. | [insert-shot.md](techniques/camera/insert-shot.md) |
| **Long Shot** | Basic | Choose a long shot when the audience needs the subject's full body and enough environment to understand movement, distance, or social position. | [long-shot.md](techniques/camera/long-shot.md) |
| **Low Angle Shot** | Basic | Use a low angle to make a person, structure, or object command the frame. | [low-angle-shot.md](techniques/camera/low-angle-shot.md) |
| **Master Shot** | Intermediate | Build a master shot when a scene depends on ensemble timing, spatial continuity, or the audience choosing where to look. | [master-shot.md](techniques/camera/master-shot.md) |
| **Medium Shot** | Basic | The medium shot is the default when dialogue and physical behavior need equal attention. | [medium-shot.md](techniques/camera/medium-shot.md) |
| **Over-the-Shoulder** | Basic | Use over-the-shoulder framing to place the audience inside a conversation while preserving who faces whom. | [over-the-shoulder.md](techniques/camera/over-the-shoulder.md) |
| **Overhead Shot** | Intermediate | Choose an overhead shot for close top-down views of hands, food, maps, tools, documents, bodies, or choreographed action. | [overhead-shot.md](techniques/camera/overhead-shot.md) |
| **Pan Shot** | Basic | Use a pan when attention must travel horizontally without relocating the camera. | [pan-shot.md](techniques/camera/pan-shot.md) |
| **P.O.V. Shot** | Intermediate | Choose a point-of-view shot when viewers must share a character's act of looking rather than merely observe them. | [pov-shot.md](techniques/camera/pov-shot.md) |
| **Rack Focus** | Intermediate | Use rack focus when two subjects share one composition but audience attention must transfer between them. | [rack-focus.md](techniques/camera/rack-focus.md) |
| **Slow Motion** | Basic | Apply slow motion when a brief action contains detail or emotional weight that normal speed would hide. | [slow-motion.md](techniques/camera/slow-motion.md) |
| **Steadicam** | Advanced | Use Steadicam movement when a character must travel through complex space without the vibration of handheld or the track-bound feel of a dolly. | [steadicam.md](techniques/camera/steadicam.md) |
| **Tracking Shot** | Intermediate | Use a tracking shot when movement through a place is part of the story: pursuit, discovery, routine, escape, or procession. | [tracking-shot.md](techniques/camera/tracking-shot.md) |
| **Vertigo Effect** | Advanced | Deploy the vertigo effect at a moment of shock, dread, realization, or altered perception. | [vertigo-effect.md](techniques/camera/vertigo-effect.md) |
| **Crane Shot** | Advanced | Choose a crane shot when a scene needs a grand reveal that changes scale and perspective in one controlled movement. | [crane-shot.md](techniques/camera/crane-shot.md) |
| **One-er (Oner)** | Advanced | Use a oner when real-time continuity should create immersion, pressure, or virtuoso flow. | [one-er.md](techniques/camera/one-er.md) |
| **Tilt Shot** | Basic | Use a tilt when attention needs to travel vertically while the camera stays in place. | [tilt-shot.md](techniques/camera/tilt-shot.md) |
| **Whip Pan** | Intermediate | Choose a whip pan for a sudden shift of attention, a visual punchline, an impact, or a high-energy transition between connected spaces. | [whip-pan.md](techniques/camera/whip-pan.md) |
| **Two-Shot** | Basic | Use a two-shot when the relationship between two people matters more than either individual performance alone. | [two-shot.md](techniques/camera/two-shot.md) |
| **Three-Shot** | Intermediate | Choose a three-shot when the drama comes from a trio rather than three separate characters. | [three-shot.md](techniques/camera/three-shot.md) |
| **Cowboy Shot** | Basic | Use the cowboy shot when a character's hands, hips, stance, or carried weapon matters alongside the face. | [cowboy-shot.md](techniques/camera/cowboy-shot.md) |
| **Medium Close-Up** | Basic | Choose a medium close-up when emotional dialogue needs intimacy without the pressure of a full close-up. | [medium-close-up.md](techniques/camera/medium-close-up.md) |
| **Choker Shot** | Intermediate | Use a choker shot when a face must become a confined emotional space. | [choker-shot.md](techniques/camera/choker-shot.md) |
| **Push In** | Intermediate | Use a push-in when a realization, decision, threat, or confession grows more important while the shot continues. | [push-in.md](techniques/camera/push-in.md) |
| **Pull Out** | Intermediate | Choose a pull-out when new context should reframe an intimate moment. | [pull-out.md](techniques/camera/pull-out.md) |
| **360-Degree Shot** | Advanced | Use a full orbit when the world should seem to spin around one emotional center. | [360-degree-shot.md](techniques/camera/360-degree-shot.md) |
| **Static Shot** | Basic | Choose a static shot when stillness should make viewers study composition, duration, and behavior inside the frame. | [static-shot.md](techniques/camera/static-shot.md) |
| **Crash Zoom** | Intermediate | Use a crash zoom to punch into a revelation, reaction, weapon, strike, or comic surprise with deliberate visual aggression. | [crash-zoom.md](techniques/camera/crash-zoom.md) |
| **Worm's Eye View** | Intermediate | Choose a worm's eye view when the camera should occupy the smallest possible position beneath something immense. | [worms-eye-view.md](techniques/camera/worms-eye-view.md) |

## Lighting (30)

| technique | level | use it when | page |
|---|---|---|---|
| **Three-Point Lighting** | Basic | Use three-point lighting when you need a flexible, readable foundation for portraits, interviews, dialogue, product work, or narrative coverage. | [three-point-lighting.md](techniques/lighting/three-point-lighting.md) |
| **Key Light** | Basic | Define the key light whenever one source must establish the scene's dominant visual logic. | [key-light.md](techniques/lighting/key-light.md) |
| **High-Key Lighting** | Basic | Choose high-key lighting for optimism, cleanliness, glamour, romantic ease, clinical futurism, or a dreamlike lack of darkness. | [high-key-lighting.md](techniques/lighting/high-key-lighting.md) |
| **Low-Key Lighting** | Intermediate | Use low-key lighting when darkness should occupy the frame as an active dramatic force. | [low-key-lighting.md](techniques/lighting/low-key-lighting.md) |
| **Chiaroscuro** | Advanced | Choose chiaroscuro when light and darkness should feel symbolic as well as descriptive. | [chiaroscuro.md](techniques/lighting/chiaroscuro.md) |
| **Rembrandt Lighting** | Intermediate | Use Rembrandt lighting when a portrait needs dimensionality, restraint, and psychological weight. | [rembrandt-lighting.md](techniques/lighting/rembrandt-lighting.md) |
| **Silhouette** | Basic | Use a silhouette when identity should give way to shape, gesture, or archetype. | [silhouette.md](techniques/lighting/silhouette.md) |
| **Golden Hour** | Basic | Choose golden hour when warmth, tenderness, nostalgia, freedom, or natural grandeur should shape the scene. | [golden-hour.md](techniques/lighting/golden-hour.md) |
| **Blue Hour** | Intermediate | Use blue hour for transitional scenes that need cool calm, loneliness, anticipation, or mystery without full darkness. | [blue-hour.md](techniques/lighting/blue-hour.md) |
| **Practical Lighting** | Intermediate | Choose practical lighting when the sources should belong visibly to the world of the scene. | [practical-lighting.md](techniques/lighting/practical-lighting.md) |
| **Hard Light** | Basic | Use hard light when sharp shadows and exposed texture should create pressure, heat, severity, or graphic style. | [hard-light.md](techniques/lighting/hard-light.md) |
| **Soft Light** | Basic | Choose soft light when faces and materials need gentle modeling, intimacy, romance, or natural overcast calm. | [soft-light.md](techniques/lighting/soft-light.md) |
| **Uplighting** | Basic | Use uplighting when illumination should feel instinctively wrong. | [uplighting.md](techniques/lighting/uplighting.md) |
| **Side Lighting** | Intermediate | Use side lighting when a face or figure needs strong dimensionality, moral tension, or a visible split between competing states. | [side-lighting.md](techniques/lighting/side-lighting.md) |
| **Lens Flare** | Basic | Choose lens flare when a bright source should feel physically present inside the optics rather than merely lighting the set. | [lens-flare.md](techniques/lighting/lens-flare.md) |
| **Fill Light** | Basic | Bring in fill light when the key creates the right direction but the shadows hide too much story information. | [fill-light.md](techniques/lighting/fill-light.md) |
| **Backlight** | Basic | Backlight is right when a subject must separate from a dark or visually busy background, or when the scene needs an ethereal halo. | [backlight.md](techniques/lighting/backlight.md) |
| **Bounce Light** | Intermediate | Use bounce light for interiors and portraits that should feel naturally illuminated without an obvious movie source. | [bounce-light.md](techniques/lighting/bounce-light.md) |
| **Cross Lighting** | Intermediate | Choose cross lighting when one source cannot explain the layered energy of a night exterior, stage, fashion frame, or neon interior. | [cross-lighting.md](techniques/lighting/cross-lighting.md) |
| **Kicker Light** | Intermediate | Reach for a kicker light when the subject almost separates from the background but a full backlight would feel excessive or unmotivated. | [kicker-light.md](techniques/lighting/kicker-light.md) |
| **Motivated Lighting** | Intermediate | Motivated lighting belongs in any scene where cinematic control must remain invisible. | [motivated-lighting.md](techniques/lighting/motivated-lighting.md) |
| **Ambient Light** | Basic | Choose ambient light when the location's existing brightness and color should define the scene before any dramatic intervention. | [ambient-light.md](techniques/lighting/ambient-light.md) |
| **Available Light** | Intermediate | Available light is the disciplined choice when added movie lighting would disturb a location's truth, performance, or natural rhythm. | [available-light.md](techniques/lighting/available-light.md) |
| **Broad Lighting** | Basic | Use broad lighting when a portrait should feel open, warm, accessible, or gently flattering rather than secretive. | [broad-lighting.md](techniques/lighting/broad-lighting.md) |
| **Short Lighting** | Intermediate | Short lighting fits portraits that need mystery, tension, a slimmer face shape, or a sense that the character is withholding information. | [short-lighting.md](techniques/lighting/short-lighting.md) |
| **Color Temperature** | Intermediate | Work with color temperature when emotional geography matters as much as brightness. | [color-temperature.md](techniques/lighting/color-temperature.md) |
| **Dappled Light** | Intermediate | Dappled light works when the environment should leave a living pattern on the subject. | [dappled-light.md](techniques/lighting/dappled-light.md) |
| **Edge Light** | Intermediate | Choose edge light when shape matters more than frontal detail, or when a dark subject needs refined separation from a dark background. | [edge-light.md](techniques/lighting/edge-light.md) |
| **Eye Light** | Intermediate | Eye light matters in close portraits where the audience must connect with a character's gaze. | [eye-light.md](techniques/lighting/eye-light.md) |
| **Gobo Lighting** | Advanced | Gobo lighting is useful when a plain wall or simple set needs narrative texture without adding physical construction. | [gobo-lighting.md](techniques/lighting/gobo-lighting.md) |

## Composition (21)

| technique | level | use it when | page |
|---|---|---|---|
| **Rule of Thirds** | Basic | Use the rule of thirds when a centered frame feels static but the composition still needs immediate clarity. | [rule-of-thirds.md](techniques/composition/rule-of-thirds.md) |
| **Symmetry** | Basic | Symmetry is strongest when order itself carries emotion. | [symmetry.md](techniques/composition/symmetry.md) |
| **Leading Lines** | Basic | Choose leading lines when the eye needs a clear route through a complex environment. | [leading-lines.md](techniques/composition/leading-lines.md) |
| **Framing Within Frame** | Intermediate | Use framing within frame when the environment should control how the audience sees a subject. | [framing-within-frame.md](techniques/composition/framing-within-frame.md) |
| **Negative Space** | Intermediate | Negative space works when what surrounds the subject should feel as important as the subject itself. | [negative-space.md](techniques/composition/negative-space.md) |
| **Shallow Focus** | Basic | Shallow focus is useful when one face, eye, hand, or object must dominate a crowded or intimate frame. | [shallow-focus.md](techniques/composition/shallow-focus.md) |
| **Deep Focus** | Intermediate | Deep focus belongs in scenes where multiple planes carry simultaneous story information. | [deep-focus.md](techniques/composition/deep-focus.md) |
| **Mise-en-Scène** | Advanced | Mise-en-scène is the right lens when the entire visible world must communicate character, class, history, or conflict before anyone speaks. | [mise-en-scene.md](techniques/composition/mise-en-scene.md) |
| **Golden Ratio** | Advanced | Use the golden ratio when a composition needs organic flow rather than the visible grid of thirds or the formality of symmetry. | [golden-ratio.md](techniques/composition/golden-ratio.md) |
| **Depth of Field** | Basic | Depth of field is a core choice whenever the audience needs guidance about where to look and how much of the space matters. | [depth-of-field.md](techniques/composition/depth-of-field.md) |
| **Foreground Interest** | Intermediate | Foreground interest helps when a scene feels flat, observed from outside, or short on spatial tension. | [foreground-interest.md](techniques/composition/foreground-interest.md) |
| **Balancing Elements** | Intermediate | Balancing elements is useful when an asymmetrical frame feels lopsided even though the subject sits in the right place. | [balancing-elements.md](techniques/composition/balancing-elements.md) |
| **Diagonal Lines** | Basic | Diagonal lines suit action, pursuit, imbalance, conflict, and scenes that should resist calm horizontal or vertical order. | [diagonal-lines.md](techniques/composition/diagonal-lines.md) |
| **Triangular Composition** | Intermediate | Triangular composition works when three people, objects, or visual anchors need a stable relationship and clear hierarchy. | [triangular-composition.md](techniques/composition/triangular-composition.md) |
| **Centered Composition** | Basic | Centered composition is powerful when the subject should confront the viewer, command ritual importance, or become the fixed axis of an ordered world. | [centered-composition.md](techniques/composition/centered-composition.md) |
| **Headroom** | Basic | Headroom matters whenever a person occupies the frame, especially in interviews, dialogue, portraits, and moving coverage. | [headroom.md](techniques/composition/headroom.md) |
| **Lead Room** | Basic | Lead room is essential when a subject looks, walks, drives, or moves toward one side of the frame. | [lead-room.md](techniques/composition/lead-room.md) |
| **Visual Weight** | Intermediate | Think in visual weight when a composition feels wrong despite correct spacing or grid placement. | [visual-weight.md](techniques/composition/visual-weight.md) |
| **Repetition and Pattern** | Intermediate | Repetition and pattern works when order, scale, routine, conformity, or obsession should dominate the frame. | [repetition-and-pattern.md](techniques/composition/repetition-and-pattern.md) |
| **Figure-Ground Relationship** | Intermediate | Figure-ground control matters whenever the subject risks disappearing into the background or should deliberately merge with it. | [figure-ground.md](techniques/composition/figure-ground.md) |
| **Contrast** | Basic | Contrast is useful when a frame needs immediate hierarchy or when two forces should feel emotionally opposed. | [contrast-composition.md](techniques/composition/contrast-composition.md) |

## Editing (17)

| technique | level | use it when | page |
|---|---|---|---|
| **Cross-Cutting** | Intermediate | Cross-cutting is the right structure when separate events happen at the same time and gain force by being compared. | [cross-cutting.md](techniques/editing/cross-cutting.md) |
| **Jump Cut** | Basic | Use a jump cut when smooth continuity would hide the anxiety, speed, boredom, or fragmentation inside a moment. | [jump-cut.md](techniques/editing/jump-cut.md) |
| **Dissolve** | Basic | A dissolve fits transitions where two images should coexist before one replaces the other. | [dissolve.md](techniques/editing/dissolve.md) |
| **Fade In/Out** | Basic | Fade in or out when the story needs a clear boundary rather than a direct visual connection between shots. | [fade-in-out.md](techniques/editing/fade-in-out.md) |
| **Montage** | Basic | Montage is built for change that matters but does not need to unfold in real time. | [montage.md](techniques/editing/montage.md) |
| **Smash Cut** | Basic | Use a smash cut when the transition itself should trigger shock, comedy, dread, or a sudden change of scale and time. | [smash-cut.md](techniques/editing/smash-cut.md) |
| **Long Take** | Advanced | A long take works when real-time duration, spatial continuity, or performance tension should trap the audience inside the event. | [long-take.md](techniques/editing/long-take.md) |
| **Freeze Frame** | Basic | Freeze frame belongs at an instant the audience should inspect, remember, or carry beyond normal time. | [freeze-frame.md](techniques/editing/freeze-frame.md) |
| **Split Screen** | Intermediate | Split screen is effective when the audience needs simultaneous access to separate locations, angles, timelines, or perspectives. | [split-screen.md](techniques/editing/split-screen.md) |
| **Reaction Shot** | Basic | Use a reaction shot when the audience should experience an event through its effect on a person rather than through spectacle alone. | [reaction-shot.md](techniques/editing/reaction-shot.md) |
| **Cutaway** | Basic | Use a cutaway when the main action needs a breath, a clue, or a pressure point outside the immediate exchange. | [cutaway.md](techniques/editing/cutaway.md) |
| **Cut-In** | Basic | Use a cut-in when an element already present in the master view deserves sudden emphasis. | [cut-in.md](techniques/editing/cut-in.md) |
| **Wipe** | Basic | Use a wipe when the transition itself should be felt: a chapter change, geographic jump, comic beat, or deliberate nod to classic adventure serials. | [wipe-transition.md](techniques/editing/wipe-transition.md) |
| **Iris** | Basic | Use an iris when you want to end on a face, object, or gesture with unmistakable theatrical emphasis. | [iris-transition.md](techniques/editing/iris-transition.md) |
| **Time-Lapse** | Intermediate | Use time-lapse when the story needs to show a process whose scale exceeds ordinary screen time: daylight crossing a room, clouds building, streets filling, or a structure changing. | [time-lapse.md](techniques/editing/time-lapse.md) |
| **Fast Motion** | Basic | Use fast motion for compressed routines, frantic escapes, mechanical work, comic physicality, or bursts of momentum that would drag at normal speed. | [fast-motion.md](techniques/editing/fast-motion.md) |
| **Reverse Motion** | Basic | Use reverse motion when ordinary physics should feel wrong: shattered material rebuilding, spilled liquid climbing upward, smoke returning to a source, or a fallen figure rising. | [reverse-motion.md](techniques/editing/reverse-motion.md) |

## Storytelling (12)

| technique | level | use it when | page |
|---|---|---|---|
| **Flashback** | Basic | Use a flashback when past experience must explain present behavior, reveal missing context, or challenge what the audience assumed. | [flashback.md](techniques/storytelling/flashback.md) |
| **Foreshadowing** | Intermediate | Use foreshadowing when a later reveal should feel earned rather than arbitrary. | [foreshadowing.md](techniques/storytelling/foreshadowing.md) |
| **Breaking the Fourth Wall** | Intermediate | Use breaking the fourth wall when the character should recruit the audience as confidant, witness, judge, or accomplice. | [breaking-fourth-wall.md](techniques/storytelling/breaking-fourth-wall.md) |
| **In Medias Res** | Basic | Use in medias res when the opening needs immediate pressure, movement, or mystery. | [in-medias-res.md](techniques/storytelling/in-medias-res.md) |
| **Cliffhanger** | Basic | Use a cliffhanger at an act, episode, or scene break when the unresolved outcome can pull the audience forward. | [cliffhanger.md](techniques/storytelling/cliffhanger.md) |
| **Flashforward** | Intermediate | Use a flashforward when knowing the destination will make the journey more charged. | [flashforward.md](techniques/storytelling/flashforward.md) |
| **Non-Linear Narrative** | Advanced | Use a non-linear narrative when reordered time creates mystery, emotional contrast, or thematic resonance that chronology cannot provide. | [non-linear-narrative.md](techniques/storytelling/non-linear-narrative.md) |
| **Parallel Storylines** | Advanced | Use parallel storylines when different characters, places, or times can illuminate the same theme from contrasting angles. | [parallel-storylines.md](techniques/storytelling/parallel-storylines.md) |
| **Frame Narrative** | Intermediate | Use a frame narrative when the act of telling matters as much as the tale. | [frame-narrative.md](techniques/storytelling/frame-narrative.md) |
| **Voiceover Narration** | Basic | Use voiceover narration when the audience needs access to thought, memory, commentary, or a perspective the visible action cannot supply. | [voiceover-narration.md](techniques/storytelling/voiceover-narration.md) |
| **Motif** | Intermediate | Use a motif when a recurring object, color, sound, shape, or gesture can become the story’s private vocabulary. | [motif.md](techniques/storytelling/motif.md) |
| **Symbolism** | Intermediate | Use symbolism when an abstract idea needs a physical image the audience can feel: enclosure for confinement, water for renewal, or a divided reflection for fractured identity. | [symbolism.md](techniques/storytelling/symbolism.md) |

## Visual Effects & Promptable FX (8)

| technique | level | use it when | page |
|---|---|---|---|
| **Color Grading** | Intermediate | Use color grading when a scene needs a unified emotional identity, a distinct period, or a visual boundary between storylines and locations. | [color-grading.md](techniques/fx/color-grading.md) |
| **Desaturation** | Basic | Use desaturation for bleak realism, memory, documentary severity, graphic stylization, or a world emotionally drained of vitality. | [desaturation.md](techniques/fx/desaturation.md) |
| **Sepia Tone** | Basic | Use sepia tone when the image should immediately suggest history, memory, an old photograph, or romantic distance from the present. | [sepia-tone.md](techniques/fx/sepia-tone.md) |
| **Film Grain** | Basic | Use film grain when pristine digital surfaces feel too sterile for the story, or when a period, documentary, intimate, or nostalgic mood benefits from tactile imperfection. | [film-grain.md](techniques/fx/film-grain.md) |
| **Bokeh** | Basic | Use bokeh when the subject needs separation from a busy environment or when background lights should become emotional atmosphere rather than information. | [bokeh.md](techniques/fx/bokeh.md) |
| **Forced Perspective** | Intermediate | Use forced perspective when characters or objects must appear impossibly large or small while sharing one believable frame. | [forced-perspective.md](techniques/fx/forced-perspective.md) |
| **Lens Distortion** | Intermediate | Use lens distortion when optical imperfection should add personality, unease, intimacy, or period texture. | [lens-distortion.md](techniques/fx/lens-distortion.md) |
| **Morphing / Dissolve Effect** | Advanced | Use morphing when transformation itself is the dramatic event: a body changing identity, matter dissolving into particles, a face becoming another face, or an object reforming from liquid or light. | [morphing.md](techniques/fx/morphing.md) |

## Genres & Styles (21)

| technique | level | use it when | page |
|---|---|---|---|
| **Film Noir** | Intermediate | Use film noir for crime, betrayal, investigation, fatal attraction, or any story built on compromised choices and approaching doom. | [film-noir.md](techniques/genres/film-noir.md) |
| **German Expressionism** | Advanced | Use German Expressionism when the environment should externalize fear, paranoia, grief, or mental fracture. | [german-expressionism.md](techniques/genres/german-expressionism.md) |
| **Cinéma Vérité** | Intermediate | Use cinéma vérité when truth should emerge through interaction rather than polished observation. | [cinema-verite.md](techniques/genres/cinema-verite.md) |
| **French New Wave** | Intermediate | Use French New Wave style when the film should feel youthful, immediate, self-aware, and resistant to studio polish. | [french-new-wave.md](techniques/genres/french-new-wave.md) |
| **Surrealism** | Advanced | Use surrealism when subconscious feeling, desire, dread, or contradiction matters more than literal cause and effect. | [surrealism.md](techniques/genres/surrealism.md) |
| **Found Footage** | Basic | Use found footage when the recording itself is part of the story: evidence, a recovered diary, surveillance, or a final document of danger. | [found-footage.md](techniques/genres/found-footage.md) |
| **Spaghetti Western** | Intermediate | Use the Spaghetti Western style for standoffs, revenge, outlaw myth, and confrontations where waiting carries as much force as violence. | [spaghetti-western.md](techniques/genres/spaghetti-western.md) |
| **Italian Neorealism** | Intermediate | Use Italian Neorealism for stories about work, poverty, family, survival, and ordinary moral choices shaped by material conditions. | [italian-neorealism.md](techniques/genres/italian-neorealism.md) |
| **Dogme 95** | Advanced | Use Dogme 95 principles when performance and human conflict should survive without cinematic decoration. | [dogme-95.md](techniques/genres/dogme-95.md) |
| **Mumblecore** | Basic | Use mumblecore for low-stakes situations carrying high emotional discomfort: uncertain relationships, stalled adulthood, shared apartments, and conversations where people interrupt, retreat, or say the wrong thing. | [mumblecore.md](techniques/genres/mumblecore.md) |
| **Giallo** | Advanced | Use giallo for horror-thriller scenes where color, suspense, and elaborate visual staging matter more than strict realism. | [giallo.md](techniques/genres/giallo.md) |
| **Mockumentary** | Basic | Use mockumentary when comedy depends on fictional characters treating absurd events with complete documentary seriousness. | [mockumentary.md](techniques/genres/mockumentary.md) |
| **Poetic Realism** | Advanced | Use poetic realism for melancholy love, working-class lives, doomed encounters, and ordinary places charged with longing. | [poetic-realism.md](techniques/genres/poetic-realism.md) |
| **Slow Cinema** | Advanced | Use slow cinema when duration itself reveals behavior, labor, landscape, grief, routine, or waiting. | [slow-cinema.md](techniques/genres/slow-cinema.md) |
| **Hyperlink Cinema** | Advanced | Use hyperlink cinema when several apparently separate stories can reveal a larger web of cause, coincidence, or shared consequence. | [hyperlink-cinema.md](techniques/genres/hyperlink-cinema.md) |
| **Tech Noir** | Intermediate | Use Tech Noir for detective stories, fugitives, artificial identities, surveillance, and moral compromise inside advanced technological worlds. | [noir-tech.md](techniques/genres/noir-tech.md) |
| **Wuxia** | Advanced | Use wuxia for martial conflict shaped by honor, loyalty, restraint, and impossible physical grace. | [wuxia.md](techniques/genres/wuxia.md) |
| **Acid Western** | Advanced | Use Acid Western style when frontier myths need to dissolve into hallucination, nihilism, absurdity, or metaphysical doubt. | [acid-western.md](techniques/genres/acid-western.md) |
| **Southern Gothic** | Intermediate | Use Southern Gothic for family secrets, moral corruption, inherited violence, religious unease, and beauty entangled with decay in the American South. | [southern-gothic.md](techniques/genres/southern-gothic.md) |
| **Vaporwave Aesthetic** | Basic | Use vaporwave when the image should feel nostalgic for a commercial future that never arrived. | [vaporwave.md](techniques/genres/vaporwave.md) |
| **Cosmic Horror** | Advanced | Use cosmic horror when fear should come from incomprehensible scale, unstable reality, or the discovery that human concerns mean nothing to the larger universe. | [cosmic-horror.md](techniques/genres/cosmic-horror.md) |
