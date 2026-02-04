# API Integration Guide

## Overview

This skill uses Google's GenAI SDK (`@google/genai`) for both image editing and video generation.

## Models

| Step | Model ID | Purpose |
|------|----------|---------|
| Image Editing | `gemini-3-pro-image-preview` | Nano Banana 3 Pro - Edit base image |
| Video Generation | `veo-3.1-generate-preview` | Veo 3.1 - Image to video |

## Setup

### Environment Variable

```bash
GEMINI_API_KEY=your_api_key_here
```

Get your API key from [Google AI Studio](https://aistudio.google.com/app/apikey).

### Required Package

```bash
npm install @google/genai
```

## Image-to-Image Editing (Nano Banana 3 Pro)

### API Call

```typescript
import { GoogleGenAI } from "@google/genai";

const ai = new GoogleGenAI({ apiKey: process.env.GEMINI_API_KEY });

const editResponse = await ai.models.generateContent({
  model: "gemini-3-pro-image-preview",
  contents: [
    {
      role: "user",
      parts: [
        {
          inlineData: {
            mimeType: "image/png",
            data: baseImageBase64  // Base64 encoded image
          }
        },
        {
          text: `Edit this image: ${sceneDescription}`
        }
      ]
    }
  ],
  config: {
    responseModalities: ["IMAGE"],
    aspectRatio: "9:16"
  }
});
```

### Response Handling

```typescript
const candidate = editResponse.candidates?.[0];
const imagePart = candidate?.content?.parts?.[0];

if (imagePart && "inlineData" in imagePart) {
  const imageData = imagePart.inlineData.data;
  const buffer = Buffer.from(imageData, "base64");
  fs.writeFileSync(outputPath, buffer);
}
```

## Image-to-Video Generation (Veo 3.1)

### API Call

```typescript
let operation = await ai.models.generateVideos({
  model: "veo-3.1-generate-preview",
  prompt: veo3Action,  // Animation description
  image: {
    imageBytes: editedImageBase64,
    mimeType: "image/png"
  },
  config: {
    aspectRatio: "9:16",
    resolution: "1080p",
    durationSeconds: 8
  }
});
```

### Polling for Completion

```typescript
while (!operation.done) {
  await new Promise(resolve => setTimeout(resolve, 10000));
  operation = await ai.operations.getVideosOperation({
    operation: operation
  });
}
```

### Response Handling

```typescript
const videoResponse = operation.response;
const generatedVideo = videoResponse?.generatedVideos?.[0]?.video;

if (generatedVideo?.uri) {
  // Download from URL
  const videoUrl = `${generatedVideo.uri}&key=${apiKey}`;
  const response = await fetch(videoUrl);
  const buffer = Buffer.from(await response.arrayBuffer());
  fs.writeFileSync(outputPath, buffer);
} else if (generatedVideo?.videoBytes) {
  // Direct bytes
  fs.writeFileSync(outputPath, Buffer.from(generatedVideo.videoBytes, "base64"));
}
```

## Configuration Options

### Image Editing

| Parameter | Values | Description |
|-----------|--------|-------------|
| `aspectRatio` | `"9:16"`, `"16:9"`, `"1:1"` | Output aspect ratio |
| `responseModalities` | `["IMAGE"]` | Must be IMAGE for editing |

### Video Generation

| Parameter | Values | Description |
|-----------|--------|-------------|
| `aspectRatio` | `"9:16"`, `"16:9"` | Video aspect ratio |
| `resolution` | `"720p"`, `"1080p"`, `"4k"` | Output resolution |
| `durationSeconds` | `5-15` | Video length (8 recommended) |

## Rate Limits

- Image editing: No explicit rate limit, but use parallel execution responsibly
- Video generation: Sequential processing with 5-second delay between requests
- Polling: 10-second intervals

## Error Handling

### Common Errors

| Error | Cause | Solution |
|-------|-------|----------|
| `INVALID_ARGUMENT` | Wrong parameter type | Check config values |
| `PERMISSION_DENIED` | Invalid API key | Verify GEMINI_API_KEY |
| `RESOURCE_EXHAUSTED` | Rate limit exceeded | Add delays between requests |
| `Operation name required` | Polling error | Pass operation object directly |

### Retry Strategy

```typescript
async function withRetry<T>(fn: () => Promise<T>, maxRetries = 3): Promise<T> {
  for (let i = 0; i < maxRetries; i++) {
    try {
      return await fn();
    } catch (error) {
      if (i === maxRetries - 1) throw error;
      await new Promise(r => setTimeout(r, 5000 * (i + 1)));
    }
  }
  throw new Error("Max retries exceeded");
}
```

## Script Reference

See `scripts/generate-veo3-video.ts` for complete implementation.
