# Index — which file owns what

A lookup for a question that arrives **mid-job**, when `strata route` has already run and you need
one specific answer. Every row is one line, so `grep -i "<your words>" INDEX.md` returns the whole
row and the filename with it.

**Owns** is the decision or fact that lives in that file and nowhere else — where a topic is
*settled*, not merely mentioned. Several files discuss alpha; one owns the choice between matting,
keying and same-clip occlusion. **Answers** is written in the words a question arrives in, which
are often not the words the file uses.

If two rows look plausible, the one whose **Owns** matches your question is the one to open.
*Measured:* ranking the reference files by how often a phrase appears finds the owning file first
only 3 times in 12 — several files discuss a topic, one settles it, and match count cannot tell
them apart; grepping this index finds it 14 of 14.

## Making the piece

| File | Owns | Answers |
|---|---|---|
| [craft.md](craft.md) | how a good video is made: shots, composition, motion principles, rhythm, polish | how long should a shot be · what makes this look cheap · shot sizes · where do I put things · does this move need a reason · pacing · read time · when to hold · light and depth · one atmospheric effect · flash frame · flash cut · overshoot |
| [workflow.md](workflow.md) | the seven steps end to end, and the Definition of Done | what do I do next · what order · when do I get sign-off · am I finished · what counts as done · unattended run · never end on a promise |
| [director.md](director.md) | the creative-direction pass for premium work | how do I make this AAA · thesis · three directions · styleframes · motion bible · what stays still · forbidden moves · expectedness · how do I not make the obvious thing |
| [anti-slop.md](anti-slop.md) | the defaults agent-made frames fall into, and the test that catches them | why does this look generic · looks AI-made · looks like a template · scrim · accent bar · eyebrow label · 01/02/03 · cream and terracotta · what should I remove |
| [layouts.md](layouts.md) | named frame layouts and the 12-column grid | where do I put the text · composition · hero-center · split screen · thirds · safe area · dead space · lower band · let the user lay it out |
| [video-layouts.md](video-layouts.md) | composing *with* footage rather than full-bleed clip plus caption | footage in a shape · text behind a person · type as a window · split screen · picture in picture · device frame · phone mockup · occlusion · subject passes in front of the type |
| [motion-design.md](motion-design.md) | the ten techniques with real keys, and the emotion/material tables | how should this move · easing · overshoot · stagger · anticipation · follow through · motion blur · what curve · make it feel heavy or playful · web animation tells |
| [editing-director.md](editing-director.md) | the editorial contract and why this cut, here, now | why this cut · pacing · information release · viewer promise · dominant mode · montage · does the edit have a shape |
| [blueprints.md](blueprints.md) | whole-video structures by type | what structure · beat sheet · shot count · how do I lay out 30 seconds · product launch · explainer · data story · logo reveal |
| [video-styles.md](video-styles.md) | 50 kinds of video with their shot grammar and tells — technique only, a brand overrides every style | make it like a TikTok · a sale ad · a luxury spot · what kind of video is this · which style · construction of a look |
| [reference-styles.md](reference-styles.md) | "make it feel like X" — ~25 films, directors and campaigns reduced to measurable signatures; a technique source, never a costume | feel like Apple · like Nike · in the style of a film or director · copy this campaign · what makes that look work |
| [motion-atlas.md](motion-atlas.md) | ~700 named motion concepts as vocabulary | what is this move called · name for this effect · give me options · a word for what I want (numbers here lose to motion-design.md) |

## Making assets

| File | Owns | Answers |
|---|---|---|
| [assets.md](assets.md) | generating media, the **drawn-or-generated** rule (a device, prop, icon or texture is a `generate image` asset; shapes are for simple geometry), and the **matte / chroma-key / same-clip** decision for cut-outs | draw it or generate it · a phone · a device · a prop · an icon · a product image · cut out a still · transparent overlay · remove the background · cut out a subject · green screen · alpha · knock out · `.jet` · which generate command · generate in waves · upload a file · make an image move |
| [video-generation.md](video-generation.md) | prompting `generate video`: five modes, the model table, clip length | how do I prompt a clip · text to video · image to video · first frame · last frame · the clip is too short · it froze · which model · how long should I ask for · frames and references · 24 fps · clip fps · what it actually does |
| [video-generation-advanced.md](video-generation-advanced.md) | references for consistency, camera plans, dialogue, sketch animatics | same character twice · same product every shot · copy a camera move · make them speak · lip sync · reference audio · an animatic to drive the camera |
| [video-prompts.md](video-prompts.md) | a worked, measured prompt for each kind of clip | give me a prompt · what do I write · product hero prompt · food · a person walking · b-roll · why did my clip come back wrong |
| [production-bible.md](production-bible.md) | consistency across clips: sheets, the identity block, verification | the character keeps changing · same face · same shoe · consistent cast · continuity across shots |
| [avatar.md](avatar.md) | building a talking presenter, and which route to take | talking head · presenter · spokesperson · avatar · someone speaking to camera · which is better, avatar or generated clip |
| [music.md](music.md) | the mix decision (LUFS target, loud-to-quiet range) and prompting `generate music` | how loud · loudness · LUFS · loud/quiet gap · the mix · make a track · BPM · the bed sounds generic · the music dies at the end · SFX · a whoosh · sound design levels · what it actually does |
| [captions.md](captions.md) | `strata captions` — speech timing from audio or video as `{t0,t1,text}` cues (segment-level, not per-word) | when are the words said · subtitles · caption timing · sync text to voice · did the clip say the line · transcribe |
| [generative-fx.md](generative-fx.md) | beat-sync, generator scripts, audio-reactive graphics | cut to the beat · sync to music · onsets · particles · confetti · light leaks · make it react to audio · draw a path |
| [intake.md](intake.md) | turning supplied material into a shot table and executing it | they sent a storyboard · a script · a PDF · a shot list · footage · what do I do with what they gave me |
| [video-editing.md](video-editing.md) | ffmpeg edits on finished footage | trim · cut · join · concat · reframe to vertical · speed up · slow motion · loop · strip audio · replace audio · the audio disappeared |
| [brand.md](brand.md) | capturing an identity as `.brand/brand.md` and authoring from it | on brand · our colours · our fonts · brand guidelines · like the last one · a series · style guide |
| [figma.md](figma.md) | importing a Figma design correctly | from Figma · the design file · coordinates are wrong · fonts won't compile · export the vectors |

## The engine

| File | Owns | Answers |
|---|---|---|
| [traps.md](traps.md) | every trap that yields a *wrong video* with a clean compile | it rendered wrong but compiled fine · letters show then animate one by one · letter-by-letter reveal · two voices · the audio vanished · an opaque rectangle · duplicate layer names · tofu · the shot froze · clip shorter than its slot · offset_frame · trim a clip inside the scene · 24 fps clips in a 25 fps scene · error 3000 · Scene exporter error |
| [format.md](format.md) | the scene format: every key the engine accepts; per-character animators — a reveal pins `end` at 1 | what keys can I use · syntax · text animator · ranges · reveal letter by letter · word by word · start and end of a range · is this a real property · 3D · camera · masks · track matte · layer styles · sub-comps · rich text · does this key exist · offset_frame · trim a clip inside the scene · clip shorter than its slot |
| [recipes.md](recipes.md) | engine-correct patterns to copy | how do I build X · kinetic text · a number counting up · count up · counter · a wipe · a reveal · textured type · depth of field · corner pin · a chart |
| [blocks.md](blocks.md) | reusable sub-comp blocks via `strata add` | lower third · stat card · end card · logo sting · device frame · quote card · search bar · a component I shouldn't build twice |
| [commands.md](commands.md) | the CLI: setup, every command in one line, libraries, flags, exit codes; every command answers `--help` | which command · what flags · how do I install · exit codes · what does this command do · libraries |
| [personalization.md](personalization.md) | one template → many videos: placeholder naming, `--emit-timeline` for the contract, `render --data rows.json` for the batch, charts per viewer | per viewer · each customer · their name · batch · rows · data-driven · swap media per person · charts per viewer |
| [review.md](review.md) | the critic pass on the rendered MP4, and the tool's false positives | is it good · is it done · how do I judge this · the review says freeze but it moves · the numbers look wrong · what counts as a must-fix |
| [tagging.md](tagging.md) | the `--tags` manifest for reusable scenes — two closed vocabularies; only when the user asks | tag a scene · make a template · catalog entry · what tags exist |

## Chapters — `motion/` is the animation textbook, `editorial/` the editing one

| File | Owns | Answers |
|---|---|---|
| [motion/01-foundations.md](motion/01-foundations.md) | principles, timing and spacing, easing families, weight | why does this feel wrong · timing · easing families · physics · squash and stretch · rhythm |
| [motion/02-choreography-space-camera.md](motion/02-choreography-space-camera.md) | staging, focal control, depth, camera language | where does the eye go · parallax · camera moves · push in · orbit · depth · many elements at once |
| [motion/03-typography.md](motion/03-typography.md) | type entrances and exits, numbers, multilingual and RTL | animate text · per-letter · kinetic type · counters · Hebrew · Arabic · RTL · line breaks |
| [motion/04-transitions.md](motion/04-transitions.md) | cuts, dissolves, wipes, motion-driven and textural transitions | how do I get from A to B · transition · wipe · whip pan · match cut · a transition system |
| [motion/05-shape-effects-texture.md](motion/05-shape-effects-texture.md) | shape and form, masks, optical effects, particles, materials | morph a shape · masks · glow · grain · particles · texture · what is it made of · the look |
| [motion/06-ui-data-brand.md](motion/06-ui-data-brand.md) | UI motion, data motion honesty, brand motion systems | animate an interface · a dashboard · a chart · micro-interaction · loading · a motion system for a brand |
| [motion/07-narrative-sound-emotion.md](motion/07-narrative-sound-emotion.md) | story structure, pacing, sound, emotion → motion | tell a story in 30 seconds · pacing · where does sound go · make it feel tense or joyful |
| [motion/08-styles-index.md](motion/08-styles-index.md) | the 77-entry style index | retro · glitch · isometric · claymation · what styles exist · name a look |
| [motion/09-production-qa.md](motion/09-production-qa.md) | formats, accessibility, the QA list, default numbers | what size · aspect ratio · flashing · accessibility · safe defaults · pre-delivery checks |
| [editorial/editing-grammar.md](editorial/editing-grammar.md) | the six control dimensions of pace, cut vocabulary, pacing diagnosis | why does this feel flat · cut rhythm · continuity · montage · J-cut · pacing problems |
| [editorial/short-form-performance.md](editorial/short-form-performance.md) | promos, ads, trailers, social — hooks, CTA dwell, failure modes | the hook · first three seconds · CTA · scroll-stopping · why do ads fail |
| [editorial/nonfiction-formats.md](editorial/nonfiction-formats.md) | explainers, testimonials, corporate, sports; factual integrity | an explainer · a testimonial · internal comms · a recap · real people · real data claims |
| [editorial/narrative-genres.md](editorial/narrative-genres.md) | genre as a modifier | thriller · comedy timing · documentary · trailer escalation · make it feel cinematic in a genre way |
| [editorial/workflow-and-qa.md](editorial/workflow-and-qa.md) | the editorial workflow, captions, flashing, rights, review passes | review a cut · caption rules · music rights · flashing limits · notes on someone else's edit |
| [editorial/research-sources.md](editorial/research-sources.md) | where the editorial rules come from | says who · is this sourced · what if two rules conflict (a measured engine fact wins) |
