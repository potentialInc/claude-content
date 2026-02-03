---
name: create-image
description: Generate and edit images using Gemini AI with various style options
metadata:
  tags: gemini, ai, image, generation, editing, style, background
when:
  patterns:
    - generate image
    - create image
    - make image
    - image generation
    - edit image
    - modify image
  mentions:
    - gemini
    - image
    - picture
    - transparent background
    - white background
---

# Create Image Skill

Generate and edit images using the Gemini AI API with support for multiple images, editing, and various style options.

## When to use

Use this skill when:
- Generating AI images from text prompts
- Creating multiple image variations at once
- Editing existing images with AI
- Applying specific background styles (white, transparent, custom colors)
- Creating product images with controlled backgrounds

## Quick Start

### Environment Setup

Ensure the `GEMINI_API_KEY` is set in your `.env` file:

```
GEMINI_API_KEY=your_api_key_here
```

### Generate a Single Image

```bash
npx ts-node scripts/generate-image.ts --prompt "A cute robot mascot"
```

### Generate Multiple Images

```bash
npx ts-node scripts/generate-image.ts --prompt "A cute robot mascot" --count 4
```

### Edit an Existing Image

```bash
npx ts-node scripts/generate-image.ts --edit --input public/images/robot.png --prompt "Make it wear a hat"
```

### Apply Background Styles

```bash
# White background
npx ts-node scripts/generate-image.ts --prompt "A red apple" --style white-bg

# Transparent background
npx ts-node scripts/generate-image.ts --prompt "A red apple" --style transparent

# Custom background color
npx ts-node scripts/generate-image.ts --prompt "A red apple" --style custom --bg-color "#f0f0f0"
```

### Aspect Ratio / Orientation

```bash
# Portrait (vertical, taller than wide) - ideal for Reels/TikTok
npx ts-node scripts/generate-image.ts --prompt "A cute character full body" --aspect portrait

# Landscape (horizontal, wider than tall)
npx ts-node scripts/generate-image.ts --prompt "A scenic mountain view" --aspect landscape

# Square (1:1)
npx ts-node scripts/generate-image.ts --prompt "A product photo" --aspect square
```

## Configuration

- **Model**: `gemini-3-pro-image-preview`
- **API Key**: `GEMINI_API_KEY` from `.env` file
- **Output Directory**: `public/images/generated/` (new images), `public/images/edited/` (edited images)

## Features

### 1. Multiple Image Generation

Generate multiple variations of an image in a single command:

```bash
npx ts-node scripts/generate-image.ts \
  --prompt "A futuristic cityscape at sunset" \
  --count 4 \
  --prefix "cityscape"
```

Output: `public/images/generated/cityscape_001.png`, `cityscape_002.png`, etc.

### 2. Image Editing

Edit existing images with text prompts:

```bash
npx ts-node scripts/generate-image.ts \
  --edit \
  --input public/images/original.png \
  --prompt "Add a rainbow in the background"
```

### 3. Background Styles

| Style | Flag | Description |
|-------|------|-------------|
| White | `--style white-bg` | Pure white (#FFFFFF) background |
| Transparent | `--style transparent` | Transparent/alpha background |
| Custom | `--style custom --bg-color "#HEX"` | Custom hex color background |
| None | (default) | AI-generated background |

### 4. Aspect Ratio

| Aspect | Flag | Description |
|--------|------|-------------|
| Portrait | `--aspect portrait` | Vertical 9:16, taller than wide (ideal for Reels/TikTok) |
| Landscape | `--aspect landscape` | Horizontal 16:9, wider than tall |
| Square | `--aspect square` | 1:1 equal dimensions |
| Auto | (default) | AI determines based on subject |

### 5. Output Formats

```bash
# PNG (default, supports transparency)
npx ts-node scripts/generate-image.ts --prompt "..." --format png

# JPEG (smaller file size)
npx ts-node scripts/generate-image.ts --prompt "..." --format jpeg
```

## Commands Reference

### Generate Image
```bash
npx ts-node scripts/generate-image.ts --prompt <text> [options]
```

Options:
- `--prompt <text>` - Text description of the image (required)
- `--count <n>` - Number of images to generate (default: 1, max: 8)
- `--prefix <name>` - Filename prefix (default: "image")
- `--style <type>` - Background style: `white-bg`, `transparent`, `custom`
- `--bg-color <hex>` - Background color for custom style
- `--aspect <type>` - Aspect ratio: `portrait`, `landscape`, `square` (default: auto)
- `--format <type>` - Output format: `png`, `jpeg` (default: png)
- `--output <dir>` - Output directory (default: public/images/generated/)

### Edit Image
```bash
npx ts-node scripts/generate-image.ts --edit --input <path> --prompt <text> [options]
```

Options:
- `--edit` - Enable edit mode
- `--input <path>` - Path to input image (required in edit mode)
- `--prompt <text>` - Editing instructions (required)
- `--mask <path>` - Optional mask image for targeted editing
- `--format <type>` - Output format: `png`, `jpeg` (default: png)
- `--output <dir>` - Output directory (default: public/images/edited/)

## Output Structure

```
public/images/
├── generated/           # New AI-generated images
│   ├── image_001.png
│   ├── cityscape_001.png
│   └── ...
└── edited/              # Edited images
    ├── original_edited_001.png
    └── ...
```

## Detailed Documentation

For detailed information, see the rules documentation:

- **[rules/generation.md](rules/generation.md)** - Image generation features and prompt tips
- **[rules/editing.md](rules/editing.md)** - Image editing capabilities and examples
- **[rules/styles.md](rules/styles.md)** - Background styles and presets

## Examples

### Example 1: Product Photo with White Background

```bash
npx ts-node scripts/generate-image.ts \
  --prompt "A sleek wireless headphone product photo, professional lighting" \
  --style white-bg \
  --prefix "headphone"
```

### Example 2: Logo with Transparent Background

```bash
npx ts-node scripts/generate-image.ts \
  --prompt "A minimalist fox logo, flat design" \
  --style transparent \
  --prefix "logo"
```

### Example 3: Multiple Variations

```bash
npx ts-node scripts/generate-image.ts \
  --prompt "An astronaut riding a horse on Mars" \
  --count 4 \
  --prefix "astronaut"
```

### Example 4: Edit an Image

```bash
npx ts-node scripts/generate-image.ts \
  --edit \
  --input public/images/photo.png \
  --prompt "Change the sky to a dramatic sunset"
```

### Example 5: Using with Claude Code

Simply ask Claude Code to generate images:

```
User: "Generate 3 variations of a cute cat mascot with white background"
```

Claude Code will:
1. Load this skill automatically
2. Run the generation with appropriate options
3. Save images to `public/images/generated/`
4. Show the generated file paths

## Troubleshooting

### API Key Not Found
```
Error: GEMINI_API_KEY environment variable is not set
```
**Solution**: Add `GEMINI_API_KEY=your_key` to your `.env` file.

### Invalid Model
```
Error: Model gemini-3-pro-image-preview not found
```
**Solution**: Ensure you have access to the image generation model.

### Generation Failed
```
Error: Content generation failed
```
**Solution**: Check your prompt for policy violations or try a different description.

### Transparent Background Not Working (Common Issue)

**Problem**: Gemini may generate images with visible backgrounds even when using `--style transparent`.

**Solutions**:

1. **Post-process with rembg** (Recommended for guaranteed transparency):
   ```bash
   # Install rembg
   pip install rembg

   # Remove background from image
   rembg i input.png output.png

   # Process entire folder
   rembg p input_folder/ output_folder/
   ```

2. **Use white background instead** (For light-themed designs):
   ```bash
   npx ts-node scripts/generate-image.ts --prompt "..." --style white-bg
   ```

3. **Enhance prompt for transparency**:
   ```bash
   npx ts-node scripts/generate-image.ts \
     --prompt "A cute character, isolated on pure transparent background, no background at all, floating in empty space, no ground no shadows" \
     --style transparent
   ```

4. **Workflow for guaranteed transparency**:
   ```bash
   # Step 1: Generate with transparent style
   npx ts-node scripts/generate-image.ts --prompt "..." --style transparent --output temp/

   # Step 2: Post-process with rembg
   rembg i temp/image_001.png public/images/final/image_001.png
   ```

## API Reference

The skill uses the Gemini API:

- **Model**: `gemini-3-pro-image-preview`
- **Package**: `@google/generative-ai`
- **Documentation**: https://ai.google.dev/docs

## Contributing

To extend this skill:

1. **Add new styles**: Update `rules/styles.md` with new presets
2. **Enhance editing**: Add new editing modes in `rules/editing.md`
3. **Improve prompts**: Add prompt templates in `rules/generation.md`
