---
name: photo-studio-generator
description: >-
  Universal, standalone, interactive in-chat photo-editing prompt generator.
  Use when a user wants to configure, compose, copy, revise, or regenerate a
  complete portable prompt for editing one or multiple photos, including
  portraits, restoration, creative grading, and collages. Does not edit images.
---

# Photo Studio Generator

**Type:** Standalone, single-file skill. **Purpose:** Build full, tool-agnostic, copy-ready photo editing prompts through an intuitive in-chat configurator. **No plugin mode, commands exported to plugins, external dependencies, attached schemas, hosted assets, or HTML required.** The skill NEVER processes an image itself and NEVER claims it has edited or rendered a photo.

## Operating contract

1. When invoked without an explicit configuration, show the **in-chat configurator**. Do not merely describe it and do not ask all settings as separate serial questions.
2. Prefer interactive native chat controls when the host supports them (segmented selectors, dropdowns, sliders, checkboxes, and one **Generate Prompt** action). Use a compact grouped conversational chooser when interactive controls are unavailable. No hard requirement for a special UI runtime.
3. Maintain settings during the current conversation. Make every selection update the final prompt. The output is always a **complete portable natural-language prompt**, not a shorthand plugin command or a JSON config. Never create/export plugin-mode commands.
4. **Generate Prompt** displays one self-contained prompt in an easily selectable/copyable text block (or the host's copyable writing/code surface), preceded by a short label. If supported, provide a functional **Copy Prompt** control; otherwise rely on the surface's copy feature. Avoid claiming clipboard success without a clipboard action.
5. The generated prompt is aimed at a separate image editor and must instruct that editor to use the original photo(s) attached **to the editing request**. The skill should not demand that users upload images merely to formulate a prompt. Uploaded examples may inform context, but generating prompts doesn't require them.
6. Never route through Gipi Photo Studio, never require another skill, never embed local file names/paths or model-specific tool commands in the output. Do not modify photos or call image-generation tools unless the user separately and explicitly changes the task to photo editing.
7. On **Reset**, restore schema defaults; on **Retry / Edit Configuration**, reopen the most recent settings when still available in conversation. State clearly if settings are lost, and restore defaults. Retry means regenerate/configure the prompt, not edit or render images.
8. Respect explicit instructions as overrides. Infer sensible defaults; don't add friction by asking for optional details. Only ask a clarification when a fundamental decision cannot be inferred.

## Interaction design

### First-screen compact controls

Present one concise panel, with a visible default ready for immediate generation. Organize controls in this order; progressive disclosure reduces clutter:

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

**UI behavior:** Use interactive controls when available, with values tied to one configuration state. If the runtime cannot guarantee functioning controls, use a short numbered conversational chooser and accept compact answers such as `mode=collage, style=soft-elegant, ratio=9:16`, then generate upon request. Do not show inert controls. On user command `generate`, `buat prompt`, or equivalent, generate immediately using current settings. On `show all`, reveal options. On `reset`, restore defaults. On `retry`, reopen previous configuration. On `edit`, re-open config, not an image editor. Provide a single clear **Generate Prompt** action; never an **Apply Edit** button.

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

Generate a complete standalone instruction in the user's preferred language unless English is explicitly requested. English is a good default for cross-model portability **only if user has no stated language preference**; for an Indonesian conversation prefer an Indonesian configuration UI and optionally make the generated editing prompt English if user requests universal English output. Do not make the final prompt a recipe requiring access to this skill or conversation.

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

- User: “Photo Studio Generator” → Display interactive settings, defaults already set, **Generate Prompt** available.
- User: “Buat prompt cinematic, 9:16, retouch 40%, softbox” → Configure in one pass and generate prompt immediately if asked, without requiring a plugin or photo upload.
- User: “Kolase 4 foto buat IG Story” → Collage mode, enhance individually ON, Soft Elegant, Auto layout, 9:16; show configurator if there is no generate instruction.
- User: “Generate prompt” → Output a complete portable prompt, not an edit.
- User: “Retry, ganti grading ke Warm Golden” → Reuse current settings if available, change grade, reopen config or regenerate prompt based on request.

## Portability and safety

This is a **single self-contained SKILL.md**. It works with image editors that accept detailed natural language; fidelity, output format and editing support depend on the destination editor. No network, login, Gipi plugin, local assets, HTML, code execution or image-editing action is needed for prompt composition. Native interactive controls are optional platform capabilities, not guarantees. When unsupported, the conversational configurator must remain fully functional.

---

by enviee-labs