---
name: image-editing
description: Image editing capabilities using Gemini AI
metadata:
  tags: gemini, editing, inpainting, transformation, mask
---

# Image Editing

Edit and transform existing images using the Gemini AI model.

## Basic Image Editing

Edit an image with a text prompt:

```typescript
import { GoogleGenerativeAI } from "@google/generative-ai";
import * as fs from "fs";
import * as path from "path";

const MODEL_ID = "gemini-3-pro-image-preview";

async function editImage(
  inputPath: string,
  prompt: string,
  outputPath: string
) {
  const apiKey = process.env.GEMINI_API_KEY;
  if (!apiKey) {
    throw new Error("GEMINI_API_KEY environment variable is not set");
  }

  // Read the input image
  const imageBuffer = fs.readFileSync(inputPath);
  const base64Image = imageBuffer.toString("base64");
  const mimeType = inputPath.endsWith(".png") ? "image/png" : "image/jpeg";

  const genAI = new GoogleGenerativeAI(apiKey);
  const model = genAI.getGenerativeModel({ model: MODEL_ID });

  const result = await model.generateContent({
    contents: [
      {
        role: "user",
        parts: [
          {
            inlineData: {
              mimeType,
              data: base64Image,
            },
          },
          {
            text: `Edit this image: ${prompt}`,
          },
        ],
      },
    ],
    generationConfig: {
      responseModalities: ["image"],
    },
  });

  const response = result.response;
  const imagePart = response.candidates?.[0]?.content?.parts?.find(
    (part: any) => part.inlineData
  );

  if (!imagePart?.inlineData) {
    throw new Error("No edited image generated");
  }

  const imageData = Buffer.from(imagePart.inlineData.data, "base64");
  fs.writeFileSync(outputPath, imageData);

  return outputPath;
}

// Usage
editImage(
  "public/images/photo.png",
  "Add a rainbow in the sky",
  "public/images/edited/photo_rainbow.png"
);
```

## Image-to-Image Transformation

Transform an image while preserving its structure:

```typescript
async function transformImage(
  inputPath: string,
  stylePrompt: string,
  outputPath: string
) {
  const apiKey = process.env.GEMINI_API_KEY;
  if (!apiKey) {
    throw new Error("GEMINI_API_KEY environment variable is not set");
  }

  const imageBuffer = fs.readFileSync(inputPath);
  const base64Image = imageBuffer.toString("base64");
  const mimeType = inputPath.endsWith(".png") ? "image/png" : "image/jpeg";

  const genAI = new GoogleGenerativeAI(apiKey);
  const model = genAI.getGenerativeModel({ model: MODEL_ID });

  const result = await model.generateContent({
    contents: [
      {
        role: "user",
        parts: [
          {
            inlineData: {
              mimeType,
              data: base64Image,
            },
          },
          {
            text: `Transform this image to the following style while preserving its composition and content: ${stylePrompt}`,
          },
        ],
      },
    ],
    generationConfig: {
      responseModalities: ["image"],
    },
  });

  const response = result.response;
  const imagePart = response.candidates?.[0]?.content?.parts?.find(
    (part: any) => part.inlineData
  );

  if (!imagePart?.inlineData) {
    throw new Error("No transformed image generated");
  }

  const imageData = Buffer.from(imagePart.inlineData.data, "base64");
  fs.writeFileSync(outputPath, imageData);

  return outputPath;
}

// Style transformation examples
const styleTransforms = {
  watercolor: "watercolor painting style, soft edges, flowing colors",
  oilPainting: "oil painting style, thick brushstrokes, rich colors",
  sketch: "pencil sketch, hand-drawn lines, graphite shading",
  anime: "anime illustration style, cel shaded, vibrant colors",
  vintage: "vintage photograph style, sepia tones, film grain",
  neon: "cyberpunk neon style, glowing edges, dark background",
};

// Usage
transformImage(
  "public/images/portrait.png",
  styleTransforms.watercolor,
  "public/images/edited/portrait_watercolor.png"
);
```

## Mask-Based Editing

Edit specific regions using a mask:

```typescript
async function editWithMask(
  inputPath: string,
  maskPath: string,
  prompt: string,
  outputPath: string
) {
  const apiKey = process.env.GEMINI_API_KEY;
  if (!apiKey) {
    throw new Error("GEMINI_API_KEY environment variable is not set");
  }

  // Read both images
  const imageBuffer = fs.readFileSync(inputPath);
  const maskBuffer = fs.readFileSync(maskPath);
  const base64Image = imageBuffer.toString("base64");
  const base64Mask = maskBuffer.toString("base64");

  const genAI = new GoogleGenerativeAI(apiKey);
  const model = genAI.getGenerativeModel({ model: MODEL_ID });

  const result = await model.generateContent({
    contents: [
      {
        role: "user",
        parts: [
          {
            inlineData: {
              mimeType: "image/png",
              data: base64Image,
            },
          },
          {
            inlineData: {
              mimeType: "image/png",
              data: base64Mask,
            },
          },
          {
            text: `Edit only the masked (white) region of this image: ${prompt}. The second image is the mask where white areas should be edited.`,
          },
        ],
      },
    ],
    generationConfig: {
      responseModalities: ["image"],
    },
  });

  const response = result.response;
  const imagePart = response.candidates?.[0]?.content?.parts?.find(
    (part: any) => part.inlineData
  );

  if (!imagePart?.inlineData) {
    throw new Error("No edited image generated");
  }

  const imageData = Buffer.from(imagePart.inlineData.data, "base64");
  fs.writeFileSync(outputPath, imageData);

  return outputPath;
}
```

## Common Editing Operations

### Object Removal

```typescript
async function removeObject(
  inputPath: string,
  objectDescription: string,
  outputPath: string
) {
  return editImage(
    inputPath,
    `Remove the ${objectDescription} from this image and fill the area naturally`,
    outputPath
  );
}

// Usage
removeObject(
  "public/images/photo.png",
  "person in the background",
  "public/images/edited/photo_cleaned.png"
);
```

### Object Addition

```typescript
async function addObject(
  inputPath: string,
  objectDescription: string,
  location: string,
  outputPath: string
) {
  return editImage(
    inputPath,
    `Add ${objectDescription} ${location} in this image`,
    outputPath
  );
}

// Usage
addObject(
  "public/images/room.png",
  "a potted plant",
  "in the corner",
  "public/images/edited/room_plant.png"
);
```

### Background Replacement

```typescript
async function replaceBackground(
  inputPath: string,
  newBackground: string,
  outputPath: string
) {
  return editImage(
    inputPath,
    `Replace the background with ${newBackground}, keep the main subject intact`,
    outputPath
  );
}

// Usage
replaceBackground(
  "public/images/portrait.png",
  "a professional studio backdrop with soft gradient lighting",
  "public/images/edited/portrait_studio.png"
);
```

### Color Adjustment

```typescript
async function adjustColors(
  inputPath: string,
  colorInstructions: string,
  outputPath: string
) {
  return editImage(
    inputPath,
    `Adjust the colors: ${colorInstructions}`,
    outputPath
  );
}

// Usage examples
adjustColors(
  "public/images/photo.png",
  "Make it warmer with golden tones",
  "public/images/edited/photo_warm.png"
);

adjustColors(
  "public/images/photo.png",
  "Convert to black and white with high contrast",
  "public/images/edited/photo_bw.png"
);
```

## Batch Editing

Edit multiple images with the same operation:

```typescript
async function batchEdit(
  inputPaths: string[],
  prompt: string,
  outputDir: string
) {
  const results: { input: string; output: string; success: boolean }[] = [];

  for (const inputPath of inputPaths) {
    const filename = path.basename(inputPath, path.extname(inputPath));
    const outputPath = path.join(outputDir, `${filename}_edited.png`);

    try {
      await editImage(inputPath, prompt, outputPath);
      results.push({ input: inputPath, output: outputPath, success: true });
      console.log(`✓ Edited: ${filename}`);
    } catch (error) {
      results.push({ input: inputPath, output: "", success: false });
      console.error(`✗ Failed: ${filename}`);
    }

    // Rate limiting
    await new Promise((resolve) => setTimeout(resolve, 1000));
  }

  return results;
}

// Usage
batchEdit(
  ["image1.png", "image2.png", "image3.png"],
  "Enhance the lighting and colors",
  "public/images/edited"
);
```

## Error Handling for Editing

```typescript
async function safeEditImage(
  inputPath: string,
  prompt: string,
  outputPath: string
) {
  // Validate input file exists
  if (!fs.existsSync(inputPath)) {
    return { success: false, error: `Input file not found: ${inputPath}` };
  }

  // Validate file format
  const ext = path.extname(inputPath).toLowerCase();
  if (![".png", ".jpg", ".jpeg", ".webp"].includes(ext)) {
    return { success: false, error: `Unsupported format: ${ext}` };
  }

  // Validate file size (max 20MB)
  const stats = fs.statSync(inputPath);
  if (stats.size > 20 * 1024 * 1024) {
    return { success: false, error: "File too large (max 20MB)" };
  }

  try {
    const result = await editImage(inputPath, prompt, outputPath);
    return { success: true, path: result };
  } catch (error: any) {
    return { success: false, error: error.message };
  }
}
```
