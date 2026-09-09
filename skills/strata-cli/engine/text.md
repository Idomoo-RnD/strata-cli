# Text layers

Type: boxes and alignment, fonts as file paths, tracking and leading, per-character animators, and right-to-left scripts.

Part of the scene-format reference — the index, and every other part, is in [format.md](../format.md).

## Text

```json
{ "type": "text", "text": "Hello", "font": "./arial.ttf", "size": 96,
  "color": "#ffffff", "box": [0,0,1280,200], "align": "center middle",
  "tracking": 0, "leading": 1.2, "breakline": false, "shrink": true, "min_size": 0,
  "rtl": false, "ellipsis": "…" }
```

- `font` (required): path to .ttf/.otf — deduped into the asset table.
- ⚠️ **The font MUST contain a glyph for every character in `text` (and every `styles` span).** The IDM only embeds the glyphs the font actually has — a character the font is missing renders as a blank/tofu box or **crashes the cloud render (error 3000 — a generic code, [traps.md](../traps.md#error-3000-is-a-generic-exporter-code))**. `validate`/`compile` now **auto-check coverage** and ⚠ name the exact missing characters (incl. emoji), so I fix them before rendering. **Verify glyph coverage before the final compile**, especially for anything beyond plain A–Z/0–9: accented/non-Latin letters (é ñ ü 你好 العربية), currency (€ £ ₪ ₹), punctuation people paste in (curly quotes “ ” ‘ ’, en/em dashes – —, ellipsis …), symbols (™ © ® • → ✓ ★), and emoji. Many basic fonts (Arial, and even the bundled DejaVuSans for non-Latin scripts) lack these. Options, best first: (1) pick/generate a font that covers the script — e.g. a Noto family for the target language; (2) for a styled span, point that span's `font` at a font that has the glyph; (3) substitute an ASCII equivalent the font has (`->` for →, straight `"` for “”, `...` for …). When in doubt, run the glyph check below.
- **Check coverage with the CLI** — no snippet needed:
  ```bash
  strata glyphs ./font.ttf "Your exact text — €49, “smart”, → ✓"   # one font vs some copy
  strata glyphs scene.json                                          # every text layer vs its own font
  ```
  `✅ covers all N character(s)` → safe. Otherwise it names each missing character with its
  codepoint and **exits 1**, so it can gate a build. Use it *before* authoring, to pick a font
  that can carry the copy; `validate`/`compile` then re-check every layer automatically, so a
  missing glyph cannot reach a render unnoticed.
- `align`: words from `left center right` + `top middle baseline bottom`, e.g. `"center middle"`, or `{"h": "center", "v": "top"}`.

> ### ⚠️ Vertical text sits at the BOTTOM of its box — `"top"` does nothing (VERIFIED)
> Rendered test, box `y 80 → 340` (260px tall), `size: 60`:
>
> | `align` | where the glyphs landed |
> |---|---|
> | `left top` | **y 298–339** — 1px off the box bottom |
> | `left bottom` | y 298–339 — **identical to `top`** |
> | `left middle` | y 190–231 — correctly centred (110/109px gaps) |
>
> **`middle` is the only vertical value that repositions anything.** A tall box with
> `"… top"` silently drops the text to the bottom, so:
> - **Anchor vertical maths on `box_y + box_h`, never `box_y + size`.** Stacking rows by
>   adding the font size to the box top puts every row in the wrong place — the classic
>   symptom is rows that look right in one layout and collapse in another.
> - **Size the box to the copy** (~`size × 1.3` per line) and position it by its **bottom**
>   edge; or use `"middle"` with the box centred on where the text should be.
> - A box exactly as tall as the font size **overflows ~22% below** (descenders): `size: 80`
>   in an 80px box drew to 18px past the bottom edge. Give a single line ~`1.3 × size`.
>   `validate` warns on this (below), so it is checked rather than remembered — the number is
>   here for when you need to size a box, not as something to keep in mind.
> - **`strata preview` cannot show this** — it draws layer *boxes*, not glyphs. Confirm text
>   placement with `strata snapshot` (poster only, cheap) before a full render.
>
> `validate` now warns when a text layer combines a `top` alignment with a box much taller
> than its type.
- **Rich spans (typography)** — `"styles"` is a list of per-character-range overrides, each `{ "start": <char index>, "length": <chars>, ... }`. **Confirmed to render:** `color` (hex), `size`, `tracking`, `leading`, `shift`, and a per-span `font` (`font` optional — defaults to the layer font). Example:
  ```json
  "text": "Rich VASCO Text",
  "styles": [
    { "start": 0,  "length": 5, "color": "#ff5a5f", "font": "./Bold.ttf" },
    { "start": 5,  "length": 6, "color": "#ffd166", "tracking": 6 },
    { "start": 11, "length": 4, "color": "#4cc9f0" }
  ]
  ```
  - ⚠️ **Spans MUST cover every character contiguously — including the spaces.** The renderer drops any character not covered by a span, so a gap (e.g. a space left out between two spans) **disappears** and words jam together ("Rich VASCO" → "RichVASCO"). Always extend each span to include its trailing space, or chain spans edge-to-edge so `start[n] = start[n-1] + length[n-1]` with no holes.
  - **Bold / italic:** point the span's `font` at a real **bold/italic font file** (e.g. `./Inter-Bold.ttf`). The boolean `bold`/`italic` flags do **not** synthesize a weight/slant in the renderer — they are no-ops without a variant font.
  - **`underline` / `strikethrough` / `highlight` do NOT render** in the current engine — don't rely on them. For an underline, draw a thin `solid` bar under the text; for highlight, place a `solid` (or a rounded image) behind the text layer. **And ask whether the title needs one at all** — a rule under a lone heading separates nothing and is one of the commonest agent tells; prefer weight, size or colour ([anti-slop.md](../craft/anti-slop.md)). Draw it when the brief asks.
  - **Non-ASCII spans — handled automatically.** `start`/`length` are authored in **characters**; the exporter indexes by **UTF-8 byte**, so multi-byte chars (`×`, `€`, `–`, accents, CJK, emoji) used to crash export. The compiler now converts span offsets to byte offsets at compile time, so styled non-ASCII text renders correctly — no workaround needed.
- **Per-character animators** (After-Effects-style): `"animators": [...]` — raw VASCO `IdmTextAnimator` objects, but `color` accepts hex and any object may carry `animate`. Example, words rising in one by one:

```json
"animators": [{
  "opacity": 0, "position": [0, 40, 0],
  "ranges": [{ "based_on": "words", "shape": "square", "end": 1,
    "animate": { "start": [{"t":0,"v":0},{"t":2,"v":1,"ease":"outQuad"}] } }]
}]
```

⛔ **A reveal pins `end` at 1 and sweeps `start`; a window that moves (`start` 0→1 *and* `end` 0.25→1.25) is not a reveal.** The offsets (`opacity: 0`, the drop) apply only to the *selected* units, and everything outside the selection sits at its base state — visible. With a moving window the whole word is on screen from the first frame and each unit goes dark and pops back as the window passes over it. *Measured* on a render of the two side by side, same 0.29→0.90 s timing: the window version shows "OTCH" before its first keyframe and knocks letters out one at a time; the pinned version shows nothing, then N, O, T, C, H in order. The state before the first keyframe is the first keyframe's value (the tween holds it), so with `end: 1` a late first keyframe is simply "all hidden" — with a window it is "one unit hidden, the rest showing". A moving window is right only for an effect that passes *through* the string (a wave, a shimmer) where the base state is the resting look ([recipes.md](../recipes.md), *Wave*). `validate` warns on a hiding animator whose `start` and `end` both move.

Animator offsets (`opacity`, `position`, `scale`, `rotation`, `color`, `tracking`, `skew`, **`character_offset`** — shift digits/letters by N, wraps mod 10 for digits, negative = glyph vanishes; **`character_value`** — replace with a codepoint, needs `character_range: full_unicode`; both verified, see recipes.md "Count-up") apply to the characters selected by `ranges`; animate the range `start`/`end`/`offset` to sweep the selection. Range options: `based_on` (`characters` `characters_excluding_spaces` `words` `lines`), `mode`, `shape` (`square ramp_up ramp_down triangle round smooth`), `units`, `randomize_order`.

⚠️ **`shape` and the string edges:** `square` has hard edges; the others (`ramp_up`/`ramp_down`/`triangle`/`round`/`smooth`) **taper** the selection. A tapered shape whose window edge lands at `0` or `1` leaves the first/last unit **partially** selected — so a "hidden at t=0, reveal in order" reveal (opacity 0, `start` 0→1, `end` pinned at 1) breaks with `smooth`/`round`/`triangle`: the last unit does visible→gone→visible and the first never animates in (widening the window doesn't fix it; the taper scales with width). **Use `square` for ordered reveals; keep tapered shapes for continuous effects with the window kept off the string ends** (see recipes.md §1).

### Right-to-left scripts (Hebrew, Arabic) — they already work; the flag does not

**Hebrew and Arabic render correctly with NO special setting.** *Measured by render:* `אבג`
came back with alef **rightmost**; `מחיר 250 שח` put `מחיר` rightmost with `250` still reading
left-to-right inside it — correct bidirectional layout, done by the engine automatically.

⚠️ **`rtl: true` is a no-op in every case tested.** Same string with the flag on and off gave
**pixel-identical** output (identical ink spans, 0.4–0.5% column-profile delta = JPEG noise)
for a single Hebrew line, a mixed Hebrew+Latin line, and a long auto-fitted string. It is in
the schema and it compiles, so it is harmless — but **it fixes nothing.** If RTL copy looks
wrong, the cause is one of the things below, never a missing flag.

What actually matters for an RTL scene:

- **Glyph coverage first.** The usual failure is tofu boxes or a cloud-render crash because the
  font has no Hebrew/Arabic glyphs — not direction. `strata glyphs ./font.ttf "<the copy>"`
  before rendering; a Noto family covers what Arial may not (Arial does carry Hebrew).
- **Alignment is yours to set.** The engine lays the script out correctly but does not move the
  text block — RTL copy normally wants `align: "right …"`, and a right-aligned column of copy
  wants its box's **right** edge on the layout grid line, not its left.
- **Numbers, prices and Latin brand names stay LTR inside RTL copy** — that is correct, not a
  bug. Do not "fix" it by reversing the string in the source; a reversed source renders
  reversed.
- **Never pre-reverse text to make it look right.** If something is backwards, the string
  itself is backwards. Store logical order and let the engine lay it out.
- **Punctuation at a line's end** (`!`, `?`, `.`) sits on the **left** in RTL. Expected.

**For personalized RTL templates**, see [personalization.md](personalization.md): a Hebrew name
substituted into a template built with English samples changes the visual weight and the side
the line grows from, so prove the layout against a long RTL value, not just the English one.
