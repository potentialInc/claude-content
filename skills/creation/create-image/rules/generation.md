---
name: image-generation
description: Image generation features using Gemini AI
metadata:
  tags: gemini, generation, prompt, batch, multiple
---

# Image Generation

Generate images from text prompts using the Gemini AI model.

## Model Configuration

- **Model**: `gemini-3-pro-image-preview`
- **Response Type**: Image (PNG/JPEG)
- **Max Images per Request**: Typically 1-4 depending on API limits

## Single Image Generation

Generate a single image from a text prompt:

```typescript
import { GoogleGenerativeAI } from "@google/generative-ai";
import * as fs from "fs";
import * as path from "path";

const MODEL_ID = "gemini-3-pro-image-preview";

async function generateImage(prompt: string, outputPath: string) {
  const apiKey = process.env.GEMINI_API_KEY;
  if (!apiKey) {
    throw new Error("GEMINI_API_KEY environment variable is not set");
  }

  const genAI = new GoogleGenerativeAI(apiKey);
  const model = genAI.getGenerativeModel({ model: MODEL_ID });

  const result = await model.generateContent({
    contents: [{ role: "user", parts: [{ text: prompt }] }],
    generationConfig: {
      responseModalities: ["image"],
    },
  });

  const response = result.response;
  const imagePart = response.candidates?.[0]?.content?.parts?.find(
    (part: any) => part.inlineData
  );

  if (!imagePart?.inlineData) {
    throw new Error("No image generated");
  }

  const imageData = Buffer.from(imagePart.inlineData.data, "base64");
  fs.writeFileSync(outputPath, imageData);

  return outputPath;
}

// Usage
generateImage(
  "A serene mountain landscape at dawn",
  "public/images/generated/landscape.png"
);
```

## Multiple Image Generation

Generate multiple variations in batch:

```typescript
async function generateMultipleImages(
  prompt: string,
  count: number,
  prefix: string,
  outputDir: string
) {
  const apiKey = process.env.GEMINI_API_KEY;
  if (!apiKey) {
    throw new Error("GEMINI_API_KEY environment variable is not set");
  }

  const genAI = new GoogleGenerativeAI(apiKey);
  const model = genAI.getGenerativeModel({ model: "gemini-3-pro-image-preview" });

  // Ensure output directory exists
  if (!fs.existsSync(outputDir)) {
    fs.mkdirSync(outputDir, { recursive: true });
  }

  const generatedFiles: string[] = [];

  for (let i = 1; i <= count; i++) {
    console.log(`Generating image ${i}/${count}...`);

    try {
      // Add variation prompt to get different results
      const variationPrompt = `${prompt}. Variation ${i} of ${count}, unique interpretation.`;

      const result = await model.generateContent({
        contents: [{ role: "user", parts: [{ text: variationPrompt }] }],
        generationConfig: {
          responseModalities: ["image"],
        },
      });

      const response = result.response;
      const imagePart = response.candidates?.[0]?.content?.parts?.find(
        (part: any) => part.inlineData
      );

      if (imagePart?.inlineData) {
        const filename = `${prefix}_${String(i).padStart(3, "0")}.png`;
        const filepath = path.join(outputDir, filename);
        const imageData = Buffer.from(imagePart.inlineData.data, "base64");
        fs.writeFileSync(filepath, imageData);
        generatedFiles.push(filepath);
        console.log(`✓ Saved: ${filename}`);
      }

      // Small delay to avoid rate limiting
      if (i < count) {
        await new Promise((resolve) => setTimeout(resolve, 1000));
      }
    } catch (error) {
      console.error(`✗ Error generating image ${i}:`, error);
    }
  }

  return generatedFiles;
}

// Usage
generateMultipleImages(
  "A cute robot mascot",
  4,
  "robot",
  "public/images/generated"
);
```

## Prompt Best Practices

### Be Specific and Descriptive

```typescript
// Good prompts
const goodPrompts = [
  "A minimalist logo of a fox, flat design, orange and white colors, on white background",
  "Professional product photo of wireless earbuds, studio lighting, white background, high detail",
  "Watercolor painting of a cozy coffee shop interior, warm lighting, vintage style",
];

// Avoid vague prompts
const vaguePrompts = [
  "a picture",
  "something nice",
  "an image",
];
```

### Transparent Background Prompts

For images requiring transparency, use explicit language:

```typescript
// Strong transparency prompts
const transparentPrompts = [
  // Most effective pattern
  "A cute character, isolated on pure transparent background, PNG with alpha channel, no background at all, floating in empty space, absolutely no ground or shadows",

  // For logos
  "A fox logo icon, isolated subject only, no background, transparent PNG, clean cutout with no environment",

  // For characters
  "A cartoon mascot, full body, isolated on transparent background, no floor no shadows no environment, subject floating in empty space",
];
```

**⚠️ Note**: Even with strong prompts, Gemini may add subtle backgrounds. For guaranteed transparency, post-process with `rembg`:

```bash
pip install rembg
rembg i input.png output.png
```

### Aspect Ratio / Orientation

Control image dimensions with aspect ratio modifiers:

```typescript
const aspectModifiers = {
  portrait: "portrait orientation, vertical image, taller than wide, 9:16 aspect ratio",
  landscape: "landscape orientation, horizontal image, wider than tall, 16:9 aspect ratio",
  square: "square image, 1:1 aspect ratio, equal width and height",
};

// For character images in vertical videos (Reels/TikTok)
const portraitCharacterPrompt = `
  A cute character, full body standing pose,
  portrait orientation, vertical image, taller than wide,
  9:16 aspect ratio, character fills the vertical frame
`;
```

**Best practices for portrait images:**
- Include "full body" for characters to fill vertical space
- Use "standing pose" or "full length view"
- Add "vertical composition, character centered"
- For video overlays: "character should be tall and narrow"

### Style Modifiers

Add style modifiers to guide the output:

```typescript
const styleModifiers = {
  photorealistic: "photorealistic, 8K, detailed, professional photography",
  illustration: "digital illustration, clean lines, vibrant colors",
  minimalist: "minimalist, simple shapes, flat design, limited color palette",
  vintage: "vintage style, retro, aged appearance, nostalgic",
  anime: "anime style, Japanese animation, cel shaded",
  sketch: "pencil sketch, hand-drawn, graphite, artistic",
};

function addStyleToPrompt(prompt: string, style: keyof typeof styleModifiers) {
  return `${prompt}, ${styleModifiers[style]}`;
}
```

### Composition Tips

```typescript
const compositionTips = {
  centered: "centered composition, subject in middle",
  ruleOfThirds: "rule of thirds composition",
  closeup: "close-up shot, detailed view",
  wideAngle: "wide angle shot, panoramic view",
  topDown: "top-down view, bird's eye perspective",
  sideView: "side view, profile angle",
};
```

## Output File Handling

### Automatic Naming

```typescript
function generateFilename(prefix: string, index: number, format: string) {
  const timestamp = new Date().toISOString().replace(/[:.]/g, "-");
  return `${prefix}_${timestamp}_${String(index).padStart(3, "0")}.${format}`;
}
```

### Directory Structure

```typescript
const outputDirectories = {
  generated: "public/images/generated/",
  edited: "public/images/edited/",
  temp: "public/images/temp/",
};

function ensureDirectories() {
  for (const dir of Object.values(outputDirectories)) {
    if (!fs.existsSync(dir)) {
      fs.mkdirSync(dir, { recursive: true });
    }
  }
}
```

## Error Handling

```typescript
async function safeGenerateImage(prompt: string, outputPath: string) {
  try {
    const result = await generateImage(prompt, outputPath);
    return { success: true, path: result };
  } catch (error: any) {
    if (error.message?.includes("SAFETY")) {
      return { success: false, error: "Content blocked by safety filters" };
    }
    if (error.message?.includes("RATE_LIMIT")) {
      return { success: false, error: "Rate limit exceeded. Try again later." };
    }
    if (error.message?.includes("INVALID_API_KEY")) {
      return { success: false, error: "Invalid API key" };
    }
    return { success: false, error: error.message };
  }
}
```

## Rate Limiting

Implement rate limiting for batch operations:

```typescript
class RateLimiter {
  private lastRequestTime = 0;
  private minInterval: number;

  constructor(requestsPerMinute: number) {
    this.minInterval = (60 * 1000) / requestsPerMinute;
  }

  async wait() {
    const now = Date.now();
    const timeSinceLastRequest = now - this.lastRequestTime;
    if (timeSinceLastRequest < this.minInterval) {
      await new Promise((resolve) =>
        setTimeout(resolve, this.minInterval - timeSinceLastRequest)
      );
    }
    this.lastRequestTime = Date.now();
  }
}

// Usage with 10 requests per minute
const rateLimiter = new RateLimiter(10);

for (const prompt of prompts) {
  await rateLimiter.wait();
  await generateImage(prompt, outputPath);
}
```
