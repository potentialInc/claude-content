# Veo3 Video Generation Workflow

## Complete 3-Step Pipeline

This workflow automates the full video generation process from prompt to final video.

## Step 0: Parse Arguments

Extract arguments from user request:

```typescript
interface Arguments {
  topic: string;           // Required - output folder name
  scenes: number;          // Default: 3
  character: string;       // Default: "female-1"
  auto: boolean;           // Default: false
  sceneJson?: string;      // Optional - path to manual scene JSON
}
```

### Argument Extraction Pattern

```
User: "create veo3 video --topic greetings --scenes 5 --auto"
Parsed: { topic: "greetings", scenes: 5, character: "female-1", auto: true }
```

---

## Step 1: Generate Scene Prompts

### Auto Mode (--auto flag)

Use Gemini to generate scene prompts based on topic:

```typescript
const PROMPT_GENERATION_TEMPLATE = `
Generate ${scenes} scene prompts for a video about "${topic}".

For each scene, provide:
1. content: Korean text with English translation and romanization
2. image_scene_description: Pose, expression, setting (NO character style)
3. veo3_action: How the still image should animate
4. animation: type, style, duration
5. voiceover: Narration text

IMPORTANT for image_scene_description:
- DO NOT include character appearance (age, hair, clothes)
- DO NOT include art style (Disney, Pixar, anime)
- ONLY include: pose, gesture, expression, setting, lighting, camera angle

Return JSON array with this structure:
[
  {
    "scene_id": 1,
    "content": {
      "korean": "한국어 텍스트",
      "english": "English translation",
      "romanization": "romanization"
    },
    "image_scene_description": "pose + expression + setting only",
    "veo3_action": "animation description",
    "animation": { "type": "type", "style": "style", "duration": 8 },
    "voiceover": "narration text"
  }
]
`;
```

### Manual Mode (--scene-json flag)

Load scenes from provided JSON file:

```typescript
const scenes = JSON.parse(fs.readFileSync(sceneJsonPath, 'utf-8'));
```

### Output: Scene Array

```json
{
  "topic": "korean-greetings",
  "character": "female-1",
  "scenes": [/* array of scene objects */]
}
```

---

## Step 2: Edit Base Images (Nano Banana 3 Pro)

### Load Base Character

```typescript
const baseImagePath = `public/images/base-characters/${character}.png`;
const baseImageData = fs.readFileSync(baseImagePath);
const baseImageBase64 = baseImageData.toString('base64');
```

### Image-to-Image Editing

For each scene, edit the base image to match the scene description:

```typescript
const editResponse = await ai.models.generateContent({
  model: "gemini-3-pro-image-preview",
  contents: [
    {
      role: "user",
      parts: [
        { inlineData: { mimeType: "image/png", data: baseImageBase64 } },
        { text: `Edit this image: ${scene.image_scene_description}` }
      ]
    }
  ],
  config: {
    responseModalities: ["IMAGE"],
    aspectRatio: "9:16"
  }
});
```

### Parallel Execution

Process multiple images in parallel for speed:

```typescript
const imagePromises = scenes.map((scene, index) =>
  editBaseImage(scene, baseImageBase64, index)
);
const editedImages = await Promise.all(imagePromises);
```

### Save Edited Images

```typescript
const outputDir = `public/images/${topic}`;
fs.mkdirSync(outputDir, { recursive: true });

for (let i = 0; i < editedImages.length; i++) {
  const imagePath = `${outputDir}/scene_${String(i + 1).padStart(2, '0')}.png`;
  fs.writeFileSync(imagePath, editedImages[i]);
}
```

---

## Step 3: Generate Videos (Veo 3.1)

### Image-to-Video Generation

For each edited image, generate a video:

```typescript
const videoOperation = await ai.models.generateVideos({
  model: "veo-3.1-generate-preview",
  prompt: scene.veo3_action,
  image: {
    imageBytes: editedImageBase64,
    mimeType: "image/png"
  },
  config: {
    aspectRatio: "9:16",
    resolution: "1080p",
    durationSeconds: scene.animation.duration
  }
});
```

### Poll for Completion

```typescript
while (!operation.done) {
  await new Promise(resolve => setTimeout(resolve, 10000));
  operation = await ai.operations.getVideosOperation({ operation });
}
```

### Sequential Execution

Process videos sequentially to avoid rate limits:

```typescript
for (let i = 0; i < scenes.length; i++) {
  console.log(`Generating video ${i + 1}/${scenes.length}...`);
  await generateVideo(scenes[i], editedImages[i], i);

  // Rate limiting delay between videos
  if (i < scenes.length - 1) {
    await new Promise(resolve => setTimeout(resolve, 5000));
  }
}
```

### Save Videos

```typescript
const outputDir = `out/${topic}`;
fs.mkdirSync(outputDir, { recursive: true });

const videoPath = `${outputDir}/scene_${String(index + 1).padStart(2, '0')}.mp4`;

// Download from URL or save bytes
if (video.uri) {
  const response = await fetch(`${video.uri}&key=${apiKey}`);
  fs.writeFileSync(videoPath, Buffer.from(await response.arrayBuffer()));
} else if (video.videoBytes) {
  fs.writeFileSync(videoPath, Buffer.from(video.videoBytes, 'base64'));
}
```

---

## Complete Workflow Execution

```typescript
async function generateVeo3Video(args: Arguments) {
  // Step 0: Parse and validate
  validateArguments(args);

  // Step 1: Generate or load scene prompts
  const scenes = args.auto
    ? await generateScenePrompts(args.topic, args.scenes)
    : loadScenesFromJson(args.sceneJson);

  // Save prompts for reference
  savePrompts(args.topic, scenes);

  // Step 2: Edit base images (parallel)
  const baseImage = loadBaseImage(args.character);
  const editedImages = await Promise.all(
    scenes.map((scene, i) => editBaseImage(scene, baseImage, i))
  );

  // Step 3: Generate videos (sequential)
  for (let i = 0; i < scenes.length; i++) {
    await generateVideo(scenes[i], editedImages[i], args.topic, i);
  }

  // Output summary
  printSummary(args.topic, scenes.length);
}
```

---

## Error Handling

### Image Generation Errors

```typescript
try {
  const editedImage = await editBaseImage(scene, baseImage, index);
} catch (error) {
  console.error(`Failed to edit image for scene ${index + 1}: ${error.message}`);
  // Continue with next scene or retry
}
```

### Video Generation Errors

```typescript
try {
  await generateVideo(scene, editedImage, topic, index);
} catch (error) {
  console.error(`Failed to generate video for scene ${index + 1}: ${error.message}`);
  errors.push({ scene: index + 1, error: error.message });
}
```

### Summary Report

```typescript
console.log("\n" + "=".repeat(50));
console.log("Generation Summary");
console.log("=".repeat(50));
console.log(`Topic: ${topic}`);
console.log(`Character: ${character}`);
console.log(`Scenes: ${scenes.length}`);
console.log(`Images: public/images/${topic}/`);
console.log(`Videos: out/${topic}/`);
if (errors.length > 0) {
  console.log(`Errors: ${errors.length}`);
  errors.forEach(e => console.log(`  - Scene ${e.scene}: ${e.error}`));
}
```

---

## Rate Limiting

| Operation | Delay |
|-----------|-------|
| Between image edits | None (parallel) |
| Between video generations | 5 seconds |
| Polling interval | 10 seconds |

---

## Output Files

After completion, the following files are created:

```
public/images/{topic}/
├── scene_01.png
├── scene_02.png
└── scene_03.png

out/{topic}/
├── scene_01.mp4
├── scene_02.mp4
├── scene_03.mp4
└── prompts.json
```
