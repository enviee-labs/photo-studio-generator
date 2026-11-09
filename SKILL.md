---
name: photo-studio-generator
description: >-
  Hybrid standalone in-chat photo configurator and direct image editing skill.
  When a user attaches photos and invokes Photo Studio Generator, show the
  interactive editor configurator and apply the edit on confirmation. Direct
  /enhance or named preset commands edit photos immediately. Also supports
  generating full portable copyable prompts without using a plugin.
---

# Photo Studio Generator

**Type:** Standalone, single-file hybrid photo-editing skill. **Purpose:** Offer an intuitive in-chat photo configurator with actual photo editing when a compatible image-editing tool is available, plus portable full-prompt export. **No Gipi plugin, external schema, HTML, or other skill required.** Image editing requires the host application's image editing capability. Never claim an image has been edited before a tool returns a result.

## Routing — highest priority first

1. **Direct preset shortcut + attached photo(s):** `/enhance`, `/studio`, `/professional`, `/cinematic`, `/editorial`, `/golden`, `/beauty`, `/event`, `/street`, or `/restore` — immediately invoke the available image editing tool on the **original photo**, applying the named preset and any provided modifiers. **Do not show a configurator, ask for confirmation, or output only a prompt.** A command such as `/enhance + studio lighting + retouch 40%` is a direct edit request.
2. **Direct collage shortcut:** `/collage` with 2+ attached original photos — immediately run the default enhancement-first collage instructions through the image editor, unless the user explicitly requests configuration instead. Preserve each subject. If the tool supports one operation only, describe both logical stages in the editing request; don't claim separate intermediate images exist.
3. **Photo + skill invocation:** If the user uploads one or more photos and says “gunakan skill Photo Studio Generator”, “Photo Studio Generator”, “edit”, or equivalent **without a direct preset shortcut**, **immediately display the in-chat configurator** with defaults inferred from the uploaded source(s). Do not ask whether they want prompt mode, plugin mode, or HTML. One photo defaults to Single Photo; 2+ photos default to Collage if collage is intended, otherwise Single Photo/batch choice.
4. **Apply Edit:** The configurator's primary action must be **Apply Edit** whenever original photos are available. Its click must request editing of those exact originals with **all current settings** via the host's supported image editing action. Do not merely print a prompt on Apply Edit. If an interactive control cannot directly access a tool, submit the complete configuration and source-photo reference into the conversation for immediate tool-based editing. If no image editor is available, explain the limitation and offer the full prompt to copy; **never simulate successful editing**.
5. **No photo / prompt-building intent:** Show an in-chat configurator with **Generate Prompt** as the primary action. Produce one complete portable prompt that the user can copy, even without attached images. With photos present, offer **Copy Full Prompt** as a *secondary* action alongside Apply Edit.
6. **Explicit prompt-only command:** `/prompt`, “generate prompt”, “copy prompt”, or “prompt only” always creates/reveals the complete prompt without editing, regardless of uploaded photos. If a user asks only to write a prompt, do not automatically edit.
7. **Retry / edit configuration:** `/retry` restores the latest available configuration and the same original photo set if accessible, and **reopens the configurator**, not an immediate edit. `/reset` resets settings. If source photos cannot be accessed, request reattachment before editing. Never treat an AI-generated edit as the original without express user consent.
8. **Help / list:** `/help` shows usage; `/list` or `/presets` shows all 10 presets and grading options. These do not edit.

## Operating contract

- The configurator must appear **in the chat**, using actual functioning host-native controls (dropdowns, segmented options, sliders, checkbox, text input, buttons) **when supported**. If the host lacks that UI capability, provide a concise conversational chooser. A Markdown skill alone cannot force native widgets to exist on every platform.
- Prepopulate defaults and maintain a single active configuration state within the current chat. Never force the user to answer every setting one by one.
- Default single-photo settings: Natural Enhance, natural intensity, retouch 25%, HD appearance target, original aspect, auto lighting, preserve original background, grading none.
- Keep three actions distinct: **Apply Edit** = actually invoke editor (when photo and tool present); **Copy Full Prompt** = show/copy portable prompt only; **Reset** = restore defaults. Do not label a button Apply Edit if it only generates text.
- Editing uses the ORIGINAL uploaded photos. If unclear which photos are in scope, ask; if images are unavailable, request reupload. Do not invent, substitute, or silently swap sources.
- All photo-editing requests must preserve the identity and protected details of all people as defined below, including glasses, braces, freckles, moles, and natural skin texture. Respect background and clothing settings.
- When invoking the editing tool, pass a **complete natural-language editing instruction** containing the current configuration, source fidelity, and negative constraints. The prompt composer later in this skill is the source of truth for these editing instructions.
- The resulting image must come from the actual image tool. Do not falsely claim success or promise guaranteed 4K resolution/identity fidelity.
- Always display small `by enviee-labs` credit below the configurator, outside the generated editing prompt. Output no plugin commands.

## Interaction design

### First-screen compact controls

Present one concise panel, with defaults preselected and the primary action chosen according to the presence of source photos. Organize controls in this order; progressive disclosure reduces clutter:

- **Mode**: Single Photo (default) / Collage (when multiple source photos or a collage is requested).
- **Editing preset** (single photo): Natural Enhance (default), Studio Quality, Professional Headshot, Cinematic Portrait, Editorial Fashion, Golden Hour, Beauty Retouch, Wedding & Event, Street Photography, Photo Restoration.
- **Enhancement intensity**: Subtle / Natural (default) / Strong.
- **Retouch**: 0–100%, step 5, default 25%. It controls temporary blemish reduction, **not** body or face reshaping.
- **Target quality**: HD (default) / 4K. These are aesthetic/export goals, never guarantees of recoverable detail.
- **Aspect ratio**: Original (default) / 1:1 / 4:5 / 9:16 / 16:9.
- **Lighting**: Auto (default) / Studio Softbox / Natural Light / Dramatic.
- **Background**: Preserve (default) / Adaptive (request stylistically suitable alteration without modifying identity).
- **Color grading**: None (default) or one of 10 filters below; show **grading strength** (0–100%, default 40%) only if grading is enabled.
- **Optional notes**: free-form text for clothing, mood, composition, framing, specific features, special retouch requests, or destination platform.

For collage, show instead or in addition: **Enhance Each Photo First** (enabled by default), **Collage Style** (Soft Elegant default / Clean Grid / Editorial / Scrapbook Light), **Layout** (Auto default / Side by Side / Hero + Smaller / Grid / Layered), **Spacing** (Thin default / None / Medium), **Border** (White default / None / Black), **Decor** (Matching Subtle default / None), **Canvas Ratio** (Auto default / Original / 1:1 / 4:5 / 9:16 / 16:9), and optionally number of photos (for planning only; editing tool must use the actual supplied count). When user says Instagram Story, default to 9:16. Collage still exposes grading (None default; strength 40% if enabled) and optional notes.

**UI behavior:** Use interactive controls when available, with values tied to one configuration state. If the runtime cannot guarantee functioning controls, use a short numbered conversational chooser and accept compact answers such as `mode=collage, style=soft-elegant, ratio=9:16`, then generate upon request. Do not show inert controls. On user command `generate`, `buat prompt`, or equivalent, generate immediately using current settings. On `show all`, reveal options. On `reset`, restore defaults. On `retry`, reopen previous configuration. On `edit`, re-open config, not an image editor. With source photos and a compatible image editor, offer **Apply Edit** as the primary action and **Copy Full Prompt** secondarily. Without photos or when in explicit prompt-only mode, offer **Generate Prompt**. These actions must work, not be decorative.

### Presets (complete set)

| Preset | Portable prompt intent |
|---|---|
| Natural Enhance | Natural photo quality correction, exposure, sharpness, credible detail and restrained retouch |
| Studio Quality | Softbox key, gentle fill, subtle subject separation; controlled studio portrait |
| Professional Headshot | Professional framing and balanced studio light; do not invent formal clothing or replace background unless requested |
| Cinematic Portrait | Film-like tonal depth, cinematic light, believable contrast and controlled ambience |
| Editorial Fashion | Polished magazine-like composition, texture, controlled high-fashion lighting |
| Golden Hour | Realistic late-afternoon warm light with believable shadow direction |
| Beauty Retouch | Gentle removal of temporary blemishes, smooth-but-textured skin, retain pores and identifiers |
| Wedding & Event | Natural event color, skin-tone accuracy, balanced lighting across people and attire |
| Street Photography | Authentic candid atmosphere, preserved environmental story, natural texture |
| Photo Restoration | Reduce age-related dust, scratches, tears and fading while keeping period details authentic; distinguish actual damage from intentional features |

### Color grading (complete set)

| Grading | Appearance |
|---|---|
| None | No stylized grade |
| Warm Golden | Amber warmth and highlights |
| Teal & Orange | Cooler teal shadows with warmer highlights |
| Cool Moody | Desaturated blue-gray shadows |
| Soft Pastel | Airy pastel palette, modest contrast |
| Earthy Matte | Subdued olive/brown film-like matte |
| Vintage Film | Gently faded analog warmth |
| Clean Bright | Bright clean neutral whites, balanced saturation |
| Night Urban | Controlled urban blue/purple ambience |
| Rich Vibrant | Vibrant colors, skin tones protected |
| Black & White Classic | Timeless monochrome tonal separation |

No grading preview should be advertised as an actual transformation of an uploaded photograph. If reliable preview generation is not available, use textual descriptions instead of inventing sample previews.

## Non-negotiable identity & integrity rules

Include in **every** generated prompt: preserve each real person's identity, recognizable facial geometry (eyes, nose, lips, jaw), expression, approximate age, natural skin tone, hairstyle, body proportions and authentic visible details. Preserve braces including brackets and wires, glasses, freckles, moles, dimples, birthmarks, scars and other distinctive marks. Keep clothes, accessories and subject count unless the user specifically requests a change. For groups, **never merge, swap or blend different identities**. Never add unrequested people. Use soft, natural retouch: reduce temporary blemishes according to retouch percentage without whitening skin, erasing identifying details, smoothing pores into plastic, or reshaping faces/bodies. Respect preserved backgrounds in original photo regions unless explicitly changed. Do not invent missing document-like visual facts or claim perfect recovery of damaged or blurry photographs.

If user explicitly requests subject/background changes, allow them only to the specified extent, while honoring unmodified attributes. Avoid absolute claims such as guaranteed 4K restoration or pixel-perfect identity retention; the prompt should instruct best-effort fidelity.

## Collage pipeline

Default collage prompt requires TWO conceptual stages:

**Stage 1 — independently enhance EACH source photo** using Natural Enhance, natural intensity, retouch 25%, HD/DSLR-like clarity, auto corrected lighting, preserved backgrounds, no creative grading; protect each subject's identity and features. This stage is enabled unless the user disables **Enhance Each Photo First**. Do not say the generator itself completed it.

**Stage 2 — arrange the individually enhanced images into one coherent collage.** Keep source photo count, order and recognisable content; don't duplicate, drop, replace, blend or invent photos. If the destination model can't do intermediate exports, request that it honor this logical order within its available editing workflow and do not assert intermediate files exist. Optionally apply selected grading consistently at collage stage, protecting skin tones.

**Soft Elegant (default)**: neutral cream/soft canvas; printed-photo-like white frames with realistic soft shadows; small color-matching florals, foliage and ribbons chosen from clothing/photo palette, placed around images only and not covering subjects. Decorations must be subtle, not crowded. Preserve source backgrounds inside each photograph as much as possible. Other styles: **Clean Grid** (minimal aligned grid), **Editorial** (asymmetrical polished magazine), **Scrapbook Light** (tasteful paper layers, gentle overlaps). Auto layout for 2: side by side; 3: one hero + two smaller; 4: 2×2 or balanced lightly layered; 5–9: adaptive grid. For 10+ photos: readable balanced grid. Never use a collage inspiration image as a replacement for the actual source people.

## Attribution

Display the credit `by enviee-labs` as a small footer **beneath the in-chat configurator and beneath the generated copyable prompt**, outside the prompt text itself, so it is visible without affecting the portability of the prompt. The skill file also ends with the same credit.

## Prompt composer — exact output behavior

For either an Apply Edit tool request or a Copy Full Prompt action, compose a complete standalone editing instruction in the user's preferred language unless English is explicitly requested. English is a good default for cross-model portability **only if user has no stated language preference**; for an Indonesian conversation prefer an Indonesian configuration UI and optionally make the generated editing prompt English if user requests universal English output. Do not make the final prompt a recipe requiring access to this skill or conversation.

Each generated prompt should contain these labeled semantic sections in clear natural language (headings optional):

1. **Editing objective** and source photographs: use only the photo(s) attached to the receiving editor's request; if missing, ask for attachment before editing. Create a separate edited output, don't replace source if avoidable.
2. **Mode and detailed art direction**: translate selected preset/style into unambiguous visual treatment; include precise intensity, lighting, composition, target ratio, source background treatment, and HD/4K target.
3. **Retouch parameters**: translate numeric retouch to meaningful instructions, avoid aggressive retouch at high values, never confuse retouch with slimming.
4. **Grading**: filter name, desired effects and strength, or neutral natural color if None.
5. **Identity preservation and source fidelity**: include the complete multi-person safeguard summary above, tailored to visible details if user mentioned them.
6. **Collage-specific procedure** (only in collage mode): stage 1 independently enhance if enabled; stage 2 composite all photos, style, layout, spacing, border, decor and final ratio. Explicitly prohibit lost/duplicated/merged photos.
7. **Avoid**: waxy skin, face distortion, altered identity, extra/missing people, strange hands, halos, over-sharpening, over-saturation, unnatural skin hue, text/watermarks and unwanted visual changes. For restoration, avoid fabricated historically important features.
8. **Output**: return finished edited image(s) in the receiving editor, not merely a textual editing description; do not falsely assure exact output resolution.

Output must be one cohesive, copyable prompt including **every currently active setting**. Do not expose plugin tokens, internal schema names, or vague references like “apply our normal defaults.” Collage pre-enhancement settings must be specified even if they differ from final collage styling. Omit unused controls (e.g. grading strength when None) rather than leaving unresolved placeholders. Include user custom notes verbatim or faithfully incorporated; never add unwanted aesthetic changes.

## Ready-to-use default prompt (Single / Natural Enhance)

Use this only as a starting template; rewrite it to reflect every chosen setting:

> Edit the original photograph attached to this request. If no photo is attached, ask me to provide it before editing. Produce a separate enhanced image. Apply realistic **Natural Enhance** with **natural** enhancement intensity: correct exposure and white balance, recover believable highlight and shadow detail, reduce noise, and add restrained, face-aware DSLR-like sharpness. Use **auto-balanced natural lighting**, **preserve the original background**, and **preserve the original aspect ratio** and composition. Target an **HD-quality appearance** without inventing missing detail. Apply **25% subtle skin retouch** limited to temporary blemishes while retaining pores, texture and natural skin tone. Use **neutral colors without stylized grading**. Strictly preserve each person's identity, original facial geometry, expression, age appearance, hairstyle, clothes, body shape, accessories, glasses, braces including brackets and wires, freckles, moles, dimples, scars and birthmarks. Never swap, blend, add or remove people. Avoid plastic skin, whitening, face or body reshaping, artifacts, halos, excessive sharpening, unintended text and watermarks. Return the edited photograph, not an explanation.

## Examples of expected interaction

- User attaches photo + “gunakan skill Photo Studio Generator” → **Immediately display in-chat configurator**, with **Apply Edit** and **Copy Full Prompt** actions. Do not edit until Apply Edit.
- User attaches photo + `/enhance` → **Immediately edit the uploaded photo** with Natural Enhance defaults; no configurator.
- User attaches photo + `/cinematic + retouch:40% + aspect:9:16` → **Immediately edit** with those modifiers; no configurator.
- User attaches 4 photos + `/collage` → **Immediately enhance each original separately (logical step) and compose a collage**, using supported image editor; no configurator.
- User: “Photo Studio Generator” with no photos → Display interactive settings with **Generate Prompt**.
- User: “Generate prompt” → Output a full portable prompt without editing.
- User: `/retry` → Reopen last available configuration for the original photos; do not auto-render.
- User: “Apply Edit” after configuring → Invoke the editing tool on original source photos; do not just echo prompt.

## Portability and safety

This is a **single self-contained SKILL.md**, without Gipi plugin dependency. Prompt generation works without an editor; **actual editing requires a compatible host image editing tool and accessible source images**. Native UI widgets depend on platform support and cannot be guaranteed simply by loading Markdown. When absent, use a functional conversational configurator; when an editing tool is absent, provide a complete portable prompt rather than claim the image was edited. Never upload images to an unrequested external service.

---

by enviee-labs