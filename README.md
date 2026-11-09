# Photo Studio Generator

**A standalone, interactive AI photo editing skill with instant presets and a portable prompt generator.**  
by **enviee-labs**

Photo Studio Generator is a single-file hybrid skill. Upload photos and call the skill to open an in-chat configurator, or invoke a direct preset shortcut to immediately edit the photos when your chat environment has an image editing tool. It does not depend on the Gipi Photo Studio plugin.

## Features

- In-chat configurator for uploaded photos (native interactive controls when the host supports them; conversational fallback otherwise)
- **Apply Edit** for photos + a compatible editor, and **Copy Full Prompt** for portable use
- 10 photo editing presets and 10 optional color-grading styles
- Direct preset shortcuts that skip configuration
- Enhancement intensity, skin retouch, lighting, aspect ratio, and background controls
- Multi-photo collage, enhancing each original independently before arranging by default
- Multi-person identity and distinctive-feature protection
- Standalone `SKILL.md`, with no plugin integration required

## Quick start

1. [Open SKILL.md](SKILL.md), and install it in an AI environment that supports custom skills, or paste its full instructions into a chat.
2. **Interactive edit:** Upload a photo and say `Use Photo Studio Generator`. The assistant should show an in-chat configurator. Select **Apply Edit** to process the original photo when an editor is available.
3. **Immediate edit:** Upload a photo and say `/enhance`, `/studio`, `/professional`, `/cinematic`, `/editorial`, `/golden`, `/beauty`, `/event`, `/street`, or `/restore`. These shortcuts skip the configurator and request editing right away.
4. **Collage:** Upload two or more photos and say `/collage`.
5. **Prompt only:** Say `/prompt` or `Generate Prompt` to get a complete copyable prompt rather than edit the photo.
6. `/retry` reopens the most recent accessible configuration; it does not automatically rerender an image.

> **Platform limitations:** A Markdown skill by itself cannot install or force an interactive UI into every ChatGPT account. Native controls depend on the host's capabilities; otherwise the skill uses conversational configuration. Actual photo editing also requires an available image editing tool and accessible uploaded photos. Using GitHub to store the skill does not automatically activate it in a chat.

## Examples

See [Natural Enhance](examples/natural-enhance.md), [Studio Quality](examples/studio-quality.md), and [Collage](examples/collage.md).

## Attribution

**by enviee-labs**. Released under the [MIT License](LICENSE).

## Feedback

Issues and suggestions are welcome via GitHub Issues.