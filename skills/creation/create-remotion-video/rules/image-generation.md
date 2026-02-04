---
name: image-generation
description: Image generation and character consistency guidelines
metadata:
  tags: gemini, image, generation, editing, consistency, character
---

# Image Generation Guidelines

Best practices for generating consistent character images using Gemini AI.

## Character Consistency Strategy

The key to consistent characters across multiple images:

1. **Generate ONE base image** with full character details
2. **Edit the base** for variations (don't generate new)
3. **Include consistency anchors** in all prompts

## Base Image Generation

### Full Character Prompt Template
```
A cute {style} {ethnicity} {gender} character, age {age}, with {hair description}, {face description}, {eye description}, wearing {clothing description}, {initial pose}, full body character, {art style}
```

### Example Base Prompt
```
A cute Disney Pixar style Korean girl character, age 8, with short black hair with red ribbon, round cheerful face, big expressive eyes, wearing a traditional yellow hanbok dress, waving hand casually with bright friendly smile, full body character standing pose, Disney 3D animation style
```

### Generation Command
```bash
npx ts-node scripts/generate-image.ts \
  --prompt "{full character prompt}" \
  --aspect portrait \
  --style white-bg \
  --prefix "base" \
  --output "public/images/{topic}"
```

**Important:** Use `--aspect portrait` to generate vertical/tall images that work well in vertical video formats (Reels, TikTok, Stories). This ensures the character fills the vertical space properly.

## Image Editing for Variations

### Edit Prompt Template
```
Change pose to: {new pose description}, same character same outfit same style, transparent background
```

### Example Edit Prompts
| Expression | Edit Prompt |
|------------|-------------|
| Hello (casual) | "Change pose to: waving hand casually, bright friendly smile, same character same outfit same style, transparent background" |
| Hello (formal) | "Change pose to: respectful bow with hands together, polite greeting, same character same outfit same style, transparent background" |
| Thank you | "Change pose to: thumbs up with happy smile, same character same outfit same style, transparent background" |
| Goodbye | "Change pose to: waving goodbye with one hand raised, same character same outfit same style, transparent background" |
| Good night | "Change pose to: sleepy yawning expression, eyes half closed, same character same outfit same style, transparent background" |

### Edit Command
```bash
npx ts-node scripts/generate-image.ts \
  --edit \
  --input "public/images/{topic}/base_001.png" \
  --prompt "{edit prompt}" \
  --output "public/images/{topic}"
```

## Parallel Image Generation

Launch multiple Task agents to generate all variations simultaneously.

### Single Agent Template
```
Edit the base character image at "public/images/{topic}/base_001.png" to create image for "{korean}" ({english}).

Run this command:
npx ts-node scripts/generate-image.ts \
  --edit \
  --input "public/images/{topic}/base_001.png" \
  --prompt "Change pose to: {pose}, same character same outfit same style, transparent background" \
  --output "public/images/{topic}"

Then rename the output file to "{id}.png" in the same directory.
```

### Example: Launch 5 Agents in Parallel
```typescript
// All agents launched in single message
Task({
  subagent_type: "Bash",
  prompt: "Edit base for 안녕 - waving pose...",
});
Task({
  subagent_type: "Bash",
  prompt: "Edit base for 안녕하세요 - bowing pose...",
});
Task({
  subagent_type: "Bash",
  prompt: "Edit base for 고마워 - thumbs up pose...",
});
// ... more agents
```

## Consistency Anchors

Always include these in edit prompts:
- "same character"
- "same outfit"
- "same style"
- "transparent background"

## Character Style Options

### Disney Pixar Style (Recommended)
- Round, expressive features
- Large eyes
- 3D rendering look
- Vibrant colors

```
Disney Pixar style, 3D animation, expressive features, vibrant colors
```

### Anime Style
- Large eyes
- Simplified features
- Cel-shaded look

```
anime style, cel shaded, Japanese animation aesthetic, expressive eyes
```

### Minimalist/Flat
- Simple shapes
- Limited colors
- 2D illustration

```
minimalist flat design, simple shapes, limited color palette, 2D illustration
```

## Background Options

### White (Recommended for Videos)
```bash
--style white-bg
```
Best for videos with white scene backgrounds. Gemini may not produce true transparency.

### Transparent (If Needed)
```bash
--style transparent
```
Note: Gemini often outputs JPEG with visible patterns instead of true PNG transparency.

### Custom Color
```bash
--style custom --bg-color "#f0f0f0"
```

## File Naming Convention

```
{sequence}_{item_id}.png

Examples:
01_annyeong_informal.png
02_annyeonghaseyo_formal.png
03_gomawo_informal.png
```

## Image Size Recommendations

For Remotion video compositions:
- **Recommended size**: 450-550px width/height
- Use `objectFit: "contain"` to maintain aspect ratio
- Character should be prominent and centered

```tsx
<Img
  src={getImagePath(expression.image)}
  style={{
    width: 500,
    height: 500,
    objectFit: "contain",
  }}
/>
```

## Troubleshooting

### Inconsistent Character
- Ensure base image is high quality
- Use exact same edit prompt structure
- Include all consistency anchors

### Background Not Transparent (Common Issue)

**Problem**: Gemini may generate images with a visible background even when requesting transparency.

**Solutions**:

1. **Use white background instead** (Recommended for light-themed videos):
   ```bash
   npx ts-node scripts/generate-image.ts \
     --prompt "..." \
     --style white-bg \
     --output "public/images/{topic}"
   ```

2. **Emphasize isolated subject in prompt**:
   ```
   isolated character on pure white background, no shadows, clean cutout,
   subject only, professional product photography style
   ```

3. **Post-processing with background removal**:
   - Use online tools: remove.bg, Canva background remover
   - Use Python with rembg library:
     ```bash
     pip install rembg
     rembg i input.png output.png
     ```

4. **Match video background color**:
   - If using light video background (#f8fafc), generate with white-bg
   - If using dark video background, consider dark gradient in image

### Background Not Pure White (Common Issue)

**Problem**: Even with `--style white-bg`, Gemini may generate backgrounds that are slightly off-white (e.g., #f8f8f8 instead of #ffffff), creating visible edges when placed on pure white video backgrounds.

**Solution (Recommended)**: Remove backgrounds using `rembg` to create transparent PNGs:

```bash
# Install rembg with dependencies (one-time setup)
pip install "rembg[cpu]" filetype aiohttp
```

```python
# Remove backgrounds via Python script
/Library/Frameworks/Python.framework/Versions/3.13/bin/python3 -c "
from rembg import remove
from PIL import Image
import os

img_dir = 'public/images/{topic}'
images = [
    '01_item.png', '02_item.png', '03_item.png',
    '04_item.png', '05_item.png', '06_item.png',
    '07_item.png', '08_item.png', '09_item.png', '10_item.png'
]

for img_name in images:
    img_path = os.path.join(img_dir, img_name)
    print(f'Processing {img_name}...')
    input_img = Image.open(img_path)
    output_img = remove(input_img)
    output_img.save(img_path, 'PNG')
    print(f'Done: {img_name}')

print('All images processed!')
"
```

Then use pure white background in the video composition:
```tsx
<AbsoluteFill style={{ background: "#ffffff" }}>
```

**Alternative Solution**: Use a light gradient in the video composition to mask slight color differences:

```tsx
// In PhraseScene/ItemScene component
<AbsoluteFill
  style={{
    // Use gradient instead of pure white to mask slight color differences
    background: "linear-gradient(to bottom, #fafafa, #f5f5f5)",
  }}
>
```

This light gradient (nearly white) masks any slight color differences in the AI-generated image backgrounds while still appearing white to viewers.

### Pose Not Changing
- Be more specific in pose description
- Include action words ("waving", "bowing", "pointing")
- Describe expression along with pose

### Image Quality Issues
- Regenerate if quality is poor
- Try slightly different prompt wording
- Ensure base image is high resolution
