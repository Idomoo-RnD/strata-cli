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
| [design-contract.md](make/design-contract.md) | intent, locked/live holds, diagnostic confidence, approval and budget boundaries | do I need motion here · can this stay flat · no reference · uncalibrated · prototype before approval · final regression |
| [capabilities.md](engine/capabilities.md) | native vs sugar vs approximation vs external/unsupported | can Strata do this · 4K · true extrusion · depth of field · exact diagrams |
| [case-studies.md](craft/case-studies.md) | visual/mechanical comparison lessons, with proof levels | what does good spacing look like · why not add a push · good versus bad · transfer a lesson |
| [runnable-scenes.md](examples/runnable-scenes.md) | complete tested scene fixtures and setup | runnable JSON · copyable scene · typewriter example · grouped reveal · parallax fixture |
| [evidence-index.md](qa/evidence-index.md) | canonical rule IDs and reproduced/pending proof scope | says who · which test · historical render · what is unverified |
| [craft.md](craft.md) | how a good video is made: shots, composition, motion principles, rhythm, polish | how long should a shot be · what makes this look cheap · shot sizes · where do I put things · does this move need a reason · pacing · read time · when to hold · light and depth · one atmospheric effect · flash frame · flash cut · overshoot |
| [workflow.md](workflow.md) | the seven steps end to end, phase-specific reading, the Definition of Done, and the brief as the routing artifact (`strata brief init`, `strata prefs`, `strata recipe`) | what do I do next · what order · BRIEF.md · write the brief · what did the user already answer · remember my library · a series · start from the last piece · when do I get sign-off · am I finished · what counts as done · unattended run · two renders · never end on a promise |
| [director.md](direct/director.md) | the creative-direction pass for premium work | how do I make this AAA · thesis · three directions · styleframes · motion bible · what stays still · forbidden moves · expectedness · how do I not make the obvious thing |
| [anti-slop.md](craft/anti-slop.md) | the defaults agent-made frames fall into, and the test that catches them | why does this look generic · looks AI-made · looks like a template · scrim · accent bar · eyebrow label · 01/02/03 · cream and terracotta · what should I remove |
| [layouts.md](layouts.md) | named frame layouts and the 12-column grid | where do I put the text · composition · hero-center · split screen · thirds · safe area · dead space · lower band · let the user lay it out |
| [video-layouts.md](craft/video-layouts.md) | composing *with* footage rather than full-bleed clip plus caption | footage in a shape · text behind a person · type as a window · split screen · picture in picture · device frame · phone mockup · occlusion · subject passes in front of the type |
| [motion-design.md](craft/motion-design.md) | the ten techniques with real keys, and the emotion/material tables | how should this move · easing · overshoot · stagger · anticipation · follow through · motion blur · what curve · make it feel heavy or playful · web animation tells · which custom curve · spring or bezier · when to hesitate |
| [editing-director.md](direct/editing-director.md) | the editorial contract and why this cut, here, now | why this cut · pacing · information release · viewer promise · dominant mode · montage · does the edit have a shape |
| [blueprints.md](make/blueprints.md) | whole-video structures by type | what structure · beat sheet · shot count · how do I lay out 30 seconds · product launch · explainer · data story · logo reveal |
| [video-styles.md](craft/video-styles.md) | 50 kinds of video with their shot grammar and tells — technique only, a brand overrides every style | make it like a TikTok · a sale ad · a luxury spot · what kind of video is this · which style · construction of a look |
| [reference-styles.md](craft/reference-styles.md) | "make it feel like X" — ~25 films, directors and campaigns reduced to measurable signatures; a technique source, never a costume | feel like Apple · like Nike · in the style of a film or director · copy this campaign · what makes that look work |
| [motion-atlas.md](craft/motion-atlas.md) | ~700 named motion concepts as vocabulary | what is this move called · name for this effect · give me options · a word for what I want (numbers here lose to motion-design.md) |

## Routes — one page per kind of brief

| File | Owns | Answers |
|---|---|---|
| [routes/supplied-material.md](routes/supplied-material.md) | the landing page for supplied material: questions, range, reading order, done-when | a storyboard · a script · a shot list · a PDF · a deck · reference sheets · the client's footage · a voice sample · "here is" · "attached" |
| [routes/tv-ad.md](routes/tv-ad.md) | the landing page for a TV / broadcast ad, promo, launch: questions, range, reading order, done-when | TV · commercial · spot · advert · promo · launch · sale · hero film · campaign · landing page · keynote · booth · :30 |
| [routes/social.md](routes/social.md) | the landing page for social / vertical / UGC / trailer: questions, range, reading order, done-when | social · TikTok · reels · shorts · vertical · 9:16 · UGC · trailer · teaser · hook |
| [routes/explainer.md](routes/explainer.md) | the landing page for an explainer / testimonial / internal / recap: questions, range, reading order, done-when | explainer · tutorial · how-to · testimonial · case study · internal comms · onboarding · recap · highlights · demo · feature tour |
| [routes/personalized.md](routes/personalized.md) | the landing page for personalized / data-driven: questions, range, reading order, done-when | personalized · per viewer · each customer · their name · their stats · chart · graph · dashboard · KPI · batch · rows |
| [routes/presenter.md](routes/presenter.md) | the landing page for a presenter / dialogue: questions, range, reading order, done-when | presenter · spokesperson · talking head · host · avatar · dialogue · speaks · voice-over · narration · lip-sync |
| [routes/logo-ident.md](routes/logo-ident.md) | the landing page for a logo sting / ident / product hero: questions, range, reading order, done-when | logo · sting · ident · bumper · intro · opener · title sequence · product · packshot · unboxing · reveal |
| [routes/kinetic-type.md](routes/kinetic-type.md) | the landing page for kinetic type / captions: questions, range, reading order, done-when | kinetic · typographic · type-led · text-heavy · captions · subtitles · lyric · quote · headline |
| [routes/music-led.md](routes/music-led.md) | the landing page for music-led / beat-synced: questions, range, reading order, done-when | music · beat · BPM · track · song · audio-reactive · visualiser · rhythm · soundtrack |
| [routes/genre.md](routes/genre.md) | the landing page for a genre feel: questions, range, reading order, done-when | thriller · comedy · documentary · noir · horror · drama · epic · heist · western · sci-fi |
| [routes/feel-like.md](routes/feel-like.md) | the landing page for "make it feel like X": questions, range, reading order, done-when | feel like · looks like · in the style of · inspired by · a reference film or campaign · like Apple · like Nike · like Kubrick |
| [routes/premium.md](routes/premium.md) | the landing page for premium / cinematic: questions, range, reading order, done-when | premium · AAA · cinematic · broadcast quality · high-end · luxury · launch film · film-grade · flagship |
| [routes/generated-clips.md](routes/generated-clips.md) | the landing page for generated video clips: questions, range, reading order, done-when | generate · generated · AI footage · text-to-video · image-to-video · b-roll · footage of · a clip of |
| [routes/footage-edit.md](routes/footage-edit.md) | the landing page for an edit of supplied footage: questions, range, reading order, done-when | trim · cut down · join · concat · reframe · crop · speed ramp · slow-mo · mute · swap the audio · re-edit |
| [routes/brand-kit.md](routes/brand-kit.md) | the landing page for brand: questions, range, reading order, done-when | brand · branded · on-brand · style guide · brand book · like the last one · a series · our colours · our fonts |
| [routes/figma-handoff.md](routes/figma-handoff.md) | the landing page for Figma: questions, range, reading order, done-when | Figma |

## Making assets

| File | Owns | Answers |
|---|---|---|
| [assets.md](assets.md) | generating media, the **drawn-or-generated** decision (photographic objects vs precise graphic artwork), **editing an image that already exists** (`edit image`), and the **matte / chroma-key / same-clip** decision for cut-outs | draw it or generate it · a phone · a device · a prop · an icon · a product image · edit an image · change one thing in the image · remove an object · add an object · change the colour of · swap the text on a screen · almost right, fix it · cut out a still · transparent overlay · remove the background · cut out a subject · green screen · alpha · knock out · `.jet` · which generate command · generate in waves · upload a file · make an image move |
| [video-generation.md](video-generation.md) | prompting `generate video`: five modes, the model table, clip length | how do I prompt a clip · text to video · image to video · first frame · last frame · the clip is too short · it froze · which model · how long should I ask for · frames and references · 24 fps · clip fps · what it actually does |
| [video-generation-advanced.md](video-generation-advanced.md) | references for consistency, camera plans, dialogue, sketch animatics | same character twice · same product every shot · copy a camera move · make them speak · lip sync · reference audio · an animatic to drive the camera |
| [video-prompts.md](shoot/video-prompts.md) | a worked, measured prompt for each kind of clip | give me a prompt · what do I write · product hero prompt · food · a person walking · b-roll · why did my clip come back wrong |
| [production-bible.md](make/production-bible.md) | consistency across clips: sheets, the identity block, verification | the character keeps changing · same face · same shoe · consistent cast · continuity across shots |
| [avatar.md](shoot/avatar.md) | building a talking presenter, and which route to take | talking head · presenter · spokesperson · avatar · someone speaking to camera · which is better, avatar or generated clip |
| [music.md](shoot/music.md) | the mix decision (LUFS target, loud-to-quiet range) and prompting `generate music` | how loud · loudness · LUFS · loud/quiet gap · the mix · make a track · BPM · the bed sounds generic · the music dies at the end · SFX · a whoosh · sound design levels · what it actually does |
| [captions.md](shoot/captions.md) | `strata captions` — speech timing from audio or video as `{t0,t1,text}` cues (segment-level, not per-word) | when are the words said · subtitles · caption timing · sync text to voice · did the clip say the line · transcribe |
| [generative-fx.md](craft/generative-fx.md) | beat-sync, generator scripts, audio-reactive graphics | cut to the beat · sync to music · onsets · particles · confetti · light leaks · make it react to audio · draw a path · audio-reactive · spectrum analyser · bars that react to music · pulse on the beat · drive a property from audio |
| [intake.md](make/intake.md) | turning supplied material into a shot table and executing it | they sent a storyboard · a script · a PDF · a shot list · footage · what do I do with what they gave me |
| [video-editing.md](shoot/video-editing.md) | ffmpeg edits on finished footage | trim · cut · join · concat · reframe to vertical · speed up · slow motion · loop · strip audio · replace audio · the audio disappeared |
| [brand.md](brand/brand.md) | capturing an identity as `.brand/brand.md` and authoring from it | on brand · our colours · our fonts · brand guidelines · like the last one · a series · style guide |
| [figma.md](brand/figma.md) | importing a Figma design correctly | from Figma · the design file · coordinates are wrong · fonts won't compile · export the vectors |

## The engine

| File | Owns | Answers |
|---|---|---|
| [traps.md](traps.md) | every trap that yields a *wrong video* with a clean compile | it rendered wrong but compiled fine · letters show then animate one by one · letter-by-letter reveal · two voices · the audio vanished · an opaque rectangle · duplicate layer names · tofu · the shot froze · clip shorter than its slot · offset_frame · trim a clip inside the scene · 24 fps clips in a 25 fps scene · error 3000 · Scene exporter error |
| [format.md](format.md) | the scene format: every key the engine accepts; per-character animators — a reveal pins `end` at 1 | what keys can I use · syntax · text animator · ranges · reveal letter by letter · word by word · start and end of a range · is this a real property · 3D · camera · masks · track matte · layer styles · sub-comps · rich text · does this key exist · offset_frame · trim a clip inside the scene · clip shorter than its slot · draw-on · trim path · stroke a path · progress ring · underline that draws itself · repeat · repeater · radial array · tick marks · dot grid · SVG path in a mask · custom easing · my own curve · spring · bezier · overshoot curve · a curve that hesitates · named ease · motion token · audio-driven channel · react to the music |
| [recipes.md](recipes.md) | engine-correct patterns to copy | how do I build X · kinetic text · a number counting up · count up · counter · a wipe · a reveal · textured type · depth of field · corner pin · a chart · underline that writes itself · progress ring · tick scale · logo draw-on |
| [blocks.md](engine/blocks.md) | reusable sub-comp blocks via `strata add` | lower third · stat card · end card · logo sting · device frame · quote card · search bar · a component I shouldn't build twice |
| [commands.md](commands.md) | the CLI: setup, every command in one line (`brief`, `route`, `prefs`, `recipe` included), libraries, flags, exit codes; every command answers `--help` | which command · prefs · preferences · recipe · brief init · which library does render use · what flags · how do I install · exit codes · what does this command do · libraries |
| [personalization.md](engine/personalization.md) | one template → many videos: placeholder naming, `--emit-timeline` for the contract, `render --data rows.json` for the batch, charts per viewer | per viewer · each customer · their name · batch · rows · data-driven · swap media per person · charts per viewer |
| [review.md](qa/review.md) | the critic pass on the rendered MP4, and the tool's false positives | is it good · is it done · how do I judge this · the review says freeze but it moves · the numbers look wrong · what counts as a must-fix · filmstrip · frame by frame · did it actually move · judge the motion · zoom into a frame · contact sheet |
| [tagging.md](engine/tagging.md) | the `--tags` manifest for reusable scenes — two closed vocabularies; only when the user asks | tag a scene · make a template · catalog entry · what tags exist |

## Chapters — `motion/` is the animation textbook, `editorial/` the editing one

| [techniques.md](techniques.md) | 150 film techniques as prompts — camera angles and moves, lighting setups, composition, editing, story devices, promptable FX, genre looks; one page each with a template, when to use it, how to direct a generator toward it, the mistakes that break it | Dutch angle · Rembrandt lighting · match cut · noir · giallo · how do I prompt a low angle · which light for unease · a director's look · name this shot · what is a rack focus |

| File | Owns | Answers |
|---|---|---|
| [motion/01-foundations.md](craft/motion/01-foundations.md) | principles, timing and spacing, easing families, weight | why does this feel wrong · timing · easing families · physics · squash and stretch · rhythm |
| [motion/02-choreography-space-camera.md](craft/motion/02-choreography-space-camera.md) | staging, focal control, depth, camera language | where does the eye go · parallax · camera moves · push in · orbit · depth · many elements at once |
| [motion/03-typography.md](craft/motion/03-typography.md) | type entrances and exits, numbers, multilingual and RTL | animate text · per-letter · kinetic type · counters · Hebrew · Arabic · RTL · line breaks |
| [motion/04-transitions.md](craft/motion/04-transitions.md) | cuts, dissolves, wipes, motion-driven and textural transitions | how do I get from A to B · transition · wipe · whip pan · match cut · a transition system |
| [motion/05-shape-effects-texture.md](craft/motion/05-shape-effects-texture.md) | shape and form, masks, optical effects, particles, materials | morph a shape · masks · glow · grain · particles · texture · what is it made of · the look |
| [motion/06-ui-data-brand.md](craft/motion/06-ui-data-brand.md) | UI motion, data motion honesty, brand motion systems | animate an interface · a dashboard · a chart · micro-interaction · loading · a motion system for a brand |
| [motion/07-narrative-sound-emotion.md](craft/motion/07-narrative-sound-emotion.md) | story structure, pacing, sound, emotion → motion | tell a story in 30 seconds · pacing · where does sound go · make it feel tense or joyful |
| [motion/08-styles-index.md](craft/motion/08-styles-index.md) | the 77-entry style index | retro · glitch · isometric · claymation · what styles exist · name a look |
| [motion/09-production-qa.md](craft/motion/09-production-qa.md) | formats, accessibility, the QA list, default numbers | what size · aspect ratio · flashing · accessibility · safe defaults · pre-delivery checks |
| [editorial/editing-grammar.md](direct/editorial/editing-grammar.md) | the six control dimensions of pace, cut vocabulary, pacing diagnosis | why does this feel flat · cut rhythm · continuity · montage · J-cut · pacing problems |
| [editorial/short-form-performance.md](direct/editorial/short-form-performance.md) | promos, ads, trailers, social — hooks, CTA dwell, failure modes | the hook · first three seconds · CTA · scroll-stopping · why do ads fail |
| [editorial/nonfiction-formats.md](direct/editorial/nonfiction-formats.md) | explainers, testimonials, corporate, sports; factual integrity | an explainer · a testimonial · internal comms · a recap · real people · real data claims |
| [editorial/narrative-genres.md](direct/editorial/narrative-genres.md) | genre as a modifier | thriller · comedy timing · documentary · trailer escalation · make it feel cinematic in a genre way |
| [editorial/workflow-and-qa.md](direct/editorial/workflow-and-qa.md) | the editorial workflow, captions, flashing, rights, review passes | review a cut · caption rules · music rights · flashing limits · notes on someone else's edit |
| [editorial/research-sources.md](direct/editorial/research-sources.md) | where the editorial rules come from | says who · is this sourced · what if two rules conflict (a measured engine fact wins) |
