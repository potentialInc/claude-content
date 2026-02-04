---
name: workflow
description: Complete end-to-end workflow for creating Korean learning videos
metadata:
  tags: workflow, process, steps, video, generation
---

# Video Generation Workflow

Complete step-by-step process for creating Korean learning videos with consistent character images.

## Overview

```
0. Parse Args → 1. [Load Topic Library OR Generate/Parse Topic] → 2. Base Image → 3. Variation Images → 3.5. Remove Backgrounds → 4. TTS Audio → 5. Composition → 6. Render
```

## Step 0: Parse Arguments

Extract arguments from user request to determine mode and type.

### Argument Parsing
```typescript
interface VideoArgs {
  type: 'vocabulary' | 'polite' | 'situation' | 'sentence' | 'pronunciation' | 'other';
  auto: boolean;
  fromTopic?: string;  // Topic library slug
}

// Default values
const args: VideoArgs = {
  type: 'vocabulary',
  auto: false,
  fromTopic: undefined
};

// Parse from user request
// Examples:
// "create video --from-topic korean-greetings" → { fromTopic: 'korean-greetings' } (RECOMMENDED)
// "create video --type polite --auto" → { type: 'polite', auto: true }
// "create video --auto" → { type: 'vocabulary', auto: true }
// "create video about animals" → { type: 'vocabulary', auto: false }
```

### Mode Determination
- **Library Mode**: `--from-topic` flag present → Load from topic library (RECOMMENDED)
- **Auto Mode**: `--auto` flag present → AI generates topic
- **Manual Mode**: No flags → User provides topic and items

---

## Step 1: Generate or Parse Topic & Items

### 1a. Library Mode (--from-topic flag) - RECOMMENDED

When `--from-topic` is specified, load topic from library.

#### Loading Process
```typescript
const topicPath = `public/topics/${args.fromTopic}/topic.json`;
const topicData = JSON.parse(fs.readFileSync(topicPath, 'utf-8'));

// Extract config from topic library
const config = {
  topic: topicData.metadata.topic_name.english,
  topicKorean: topicData.metadata.topic_name.korean,
  outputDir: topicData.metadata.topic_slug,
  intro: topicData.intro.korean,
  outro: topicData.outro.korean,
  items: topicData.scenes.map((scene, i) => ({
    id: `${String(i + 1).padStart(2, '0')}_${scene.content.romanization.replace(/\s+/g, '_')}`,
    korean: scene.content.korean,
    english: scene.content.english,
    romanization: scene.content.romanization,
    // Adapt pose for Disney Pixar style
    pose: adaptPoseForCartoonStyle(scene.visual.pose_suggestion, scene.visual.expression)
  }))
};
```

#### Adapting Visual Hints for Cartoon Style
Topic library provides style-neutral poses. Adapt for Disney Pixar:

```typescript
function adaptPoseForCartoonStyle(pose: string, expression: string): string {
  // Add cartoon-style descriptors
  return `${pose}, ${expression}, cute expressive eyes, Disney Pixar 3D animation style`;
}

// Example:
// Input: "waving hand casually, relaxed stance" + "bright, friendly smile"
// Output: "waving hand casually, relaxed stance, bright, friendly smile, cute expressive eyes, Disney Pixar 3D animation style"
```

#### Using Voiceover Content
The topic library includes detailed voiceover:
- `scene.voiceover.korean_audio` → Use for TTS generation
- `scene.voiceover.explanation` → Can be used for English narration if needed

### 1b. Manual Mode (default)

When user requests a video without `--auto` or `--from-topic`, extract:
- Topic name (English & Korean)
- List of items to teach
- Character style (if specified)

#### Example Input
```
User: "Create a video about Korean greetings"
Items: 안녕, 안녕하세요, 고마워, 감사합니다, 잘 가
```

### 1c. Auto Mode (--auto flag)

When `--auto` is specified, AI generates the topic and items.

#### AI Generation Prompt
Based on the `--type` argument, use appropriate generation prompt:

```typescript
const GENERATION_PROMPTS = {
  vocabulary: `Generate a Korean vocabulary learning topic for children.
    Pick ONE random theme from: body parts, weather, food, household items, school supplies, transportation, fruits, vegetables, clothes, toys.
    Return JSON with exactly this structure:
    {
      "topic": "Korean [Theme]",
      "topicKorean": "[Theme in Korean]",
      "intro": "[Intro phrase in Korean, encouraging to learn]",
      "outro": "[Outro phrase in Korean, praising the learner]",
      "items": [10 items with { "korean", "english", "romanization", "pose" }]
    }`,

  polite: `Generate Korean polite expressions comparing casual (반말) vs formal (존댓말) speech.
    Include everyday phrases: greetings, thanks, apologies, requests, farewells.
    Return JSON with exactly this structure:
    {
      "topic": "Korean Polite Expressions",
      "topicKorean": "공손한 표현",
      "intro": "[Intro in Korean about learning polite speech]",
      "outro": "[Outro in Korean praising polite speech mastery]",
      "items": [10 items alternating casual/formal with { "korean", "english", "romanization", "pose" }]
    }`,

  situation: `Generate Korean phrases for a specific situation.
    Pick ONE random situation from: hospital, airport, hotel, cafe, convenience store, bank, subway, shopping mall.
    Return JSON with exactly this structure:
    {
      "topic": "Korean [Situation] Phrases",
      "topicKorean": "[Situation in Korean] 표현",
      "intro": "[Intro in Korean about the situation]",
      "outro": "[Outro in Korean praising practical skills]",
      "items": [10 practical phrases with { "korean", "english", "romanization", "pose" }]
    }`,

  sentence: `Generate common everyday Korean sentences for beginners.
    Pick ONE random theme from: eating & drinking, feelings & states, daily actions, basic requests, simple descriptions.
    Focus on simple, practical sentences used in daily life (not situation-specific).
    Use formal polite form (합니다/습니다) for consistency.
    Return JSON with exactly this structure:
    {
      "topic": "Korean Common Sentences",
      "topicKorean": "한국어 일상 문장",
      "intro": "[Intro in Korean about learning everyday sentences]",
      "outro": "[Outro in Korean praising sentence mastery]",
      "items": [10 common sentences with { "korean", "english", "romanization", "pose" }]
    }`,

  other: `Generate Korean items based on user's specified theme.
    [Requires user input for theme]`
};
```

#### AI Generation Process
1. Select generation prompt based on `--type`
2. Call AI to generate topic structure
3. Validate response has required fields
4. Continue to base image generation

#### Example Auto-Generated Output
```typescript
// AI generates for --type vocabulary --auto:
{
  topic: "Korean Body Parts",
  topicKorean: "한국어 신체 부위",
  intro: "신체 부위를 배워봐요!",
  outro: "잘했어요! 이제 신체 부위를 알아요!",
  items: [
    { korean: "머리", english: "Head", romanization: "meori", pose: "pointing at head with both hands" },
    { korean: "눈", english: "Eye", romanization: "nun", pose: "covering one eye playfully" },
    { korean: "코", english: "Nose", romanization: "ko", pose: "touching nose with finger" },
    { korean: "입", english: "Mouth", romanization: "ip", pose: "pointing at mouth, smiling" },
    { korean: "귀", english: "Ear", romanization: "gwi", pose: "cupping ear, listening pose" },
    { korean: "손", english: "Hand", romanization: "son", pose: "waving both hands" },
    { korean: "발", english: "Foot", romanization: "bal", pose: "lifting one foot, balancing" },
    { korean: "팔", english: "Arm", romanization: "pal", pose: "flexing both arms" },
    { korean: "다리", english: "Leg", romanization: "dari", pose: "marching pose" },
    { korean: "배", english: "Belly", romanization: "bae", pose: "patting belly happily" }
  ]
}
```

### Create Config Structure
```typescript
const config = {
  topic: "Korean Greetings",
  topicKorean: "한국어 인사",
  outputDir: "korean-greetings",
  character: {
    style: "Disney Pixar style",
    description: "Korean girl, age 8, short black hair with red ribbon, round cheerful face, yellow hanbok dress",
    background: "transparent"
  },
  items: [
    {
      id: "01_annyeong",
      korean: "안녕",
      english: "Hello (casual)",
      romanization: "annyeong",
      pose: "waving hand casually, bright friendly smile"
    },
    // ... more items
  ]
};
```

## Step 2: Generate Base Character Image

Generate ONE base image that establishes the character.

### Command
```bash
npx ts-node scripts/generate-image.ts \
  --prompt "A cute Disney Pixar style Korean girl character, age 8, with short black hair with red ribbon, round cheerful face, big expressive eyes, wearing a traditional yellow hanbok dress, waving hand casually saying hello with a bright friendly smile, full body character, Disney 3D animation style" \
  --aspect portrait \
  --style white-bg \
  --prefix "base" \
  --output "public/images/{topic-name}"
```

### Key Points
- Include FULL character description in prompt
- Always use `--aspect portrait` for vertical video formats
- Always use `--style white-bg` for clean white background
- Save to topic-specific directory

## Step 3: Generate Variation Images (Parallel)

Use Task agents to edit base image for each item IN PARALLEL.

### Launch Parallel Agents
```typescript
// Launch one agent per item
for (const item of items) {
  Task({
    subagent_type: "Bash",
    prompt: `Edit the base character image to create "${item.korean}" pose.

    Run:
    npx ts-node scripts/generate-image.ts \
      --edit \
      --input "public/images/{topic}/base_001.png" \
      --prompt "Change pose to: ${item.pose}, same character same outfit same style, transparent background" \
      --output "public/images/{topic}"

    Then rename output to "${item.id}.png"`
  });
}
```

### Why Parallel?
- 9 images take ~2 minutes sequentially
- 9 images take ~30 seconds in parallel
- Massive time savings

## Step 3.5: Remove Backgrounds (rembg)

AI-generated images often have slightly off-white backgrounds that don't match pure white video backgrounds. Use `rembg` to create transparent backgrounds for seamless blending.

### Prerequisites
```bash
# Install rembg with CPU support (one-time setup)
pip install "rembg[cpu]" filetype aiohttp
```

### Remove Backgrounds via Python
```python
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

    # Open image
    input_img = Image.open(img_path)

    # Remove background
    output_img = remove(input_img)

    # Save as PNG with transparency
    output_img.save(img_path, 'PNG')
    print(f'Done: {img_name}')

print('All images processed!')
"
```

### Why Remove Backgrounds?
- Gemini generates images with slightly off-white backgrounds (#f8f8f8 instead of #ffffff)
- This creates visible edges when placed on pure white video backgrounds
- Transparent backgrounds blend seamlessly with any video background color
- Characters appear cleaner and more professional

### Video Composition with Transparent Images
Use pure white background in PhraseScene/ItemScene:
```tsx
<AbsoluteFill
  style={{
    background: "#ffffff",
  }}
>
```

## Step 4: Generate TTS Audio

Create and run TTS generation script.

### Create TTS Script
```typescript
// scripts/generate-{topic}-tts.ts
import "dotenv/config";
import { ElevenLabsClient } from "elevenlabs";
import * as fs from "fs";
import * as path from "path";

const ITEMS = [
  { id: "01_annyeong", korean: "안녕" },
  // ... items from config
];

const EXTRA = [
  { id: "intro", text: "{intro text in Korean}" },
  { id: "outro", text: "{outro text in Korean}" },
];

async function generateTTS() {
  const client = new ElevenLabsClient({
    apiKey: process.env.ELEVENLABS_API_KEY
  });

  const outputDir = "public/audio/{topic}";
  // ... generate all audio files
}

generateTTS();
```

### Run TTS Generation
```bash
npx ts-node scripts/generate-{topic}-tts.ts
```

## Step 5: Create Remotion Composition

Create the video composition file.

### Create Composition File
Create `src/{TopicName}Video.tsx` following the template in [video-composition.md](video-composition.md).

### Register in Root.tsx
```tsx
import { {TopicName}Video } from "./{TopicName}";

// In RemotionRoot:
<Composition
  id="{TopicName}"
  component={{TopicName}Video}
  durationInFrames={calculated_duration}
  fps={30}
  width={1080}
  height={1920}
/>
```

### Duration Calculation
```typescript
const INTRO = 90;  // 3 seconds
const ITEM = 120;  // 4 seconds per item
const SUMMARY = 150;  // 5 seconds
const TRANSITION = 15;  // 0.5 seconds

const totalDuration = INTRO + (items.length * ITEM) + SUMMARY + ((items.length + 1) * TRANSITION);
```

## Step 6: Preview & Render

### Preview
```bash
npm run dev
# Open Remotion studio and select composition
```

### Render
```bash
npx remotion render {TopicName} out/{topic-name}.mp4
```

## Complete Example

```bash
# 1. Create output directories
mkdir -p public/images/korean-greetings public/audio/korean-greetings

# 2. Generate base image
npx ts-node scripts/generate-image.ts \
  --prompt "Disney Pixar Korean girl, yellow hanbok, waving" \
  --style transparent \
  --output public/images/korean-greetings

# 3. Generate variations (via parallel agents)
# ... 5 agents run simultaneously ...

# 4. Generate TTS
npx ts-node scripts/generate-greetings-tts.ts

# 5. Create composition
# ... create src/KoreanGreetingsVideo.tsx ...

# 6. Preview
npm run dev

# 7. Render
npx remotion render KoreanGreetings out/korean-greetings.mp4
```

## Best Practices

### Character Consistency
- Use the SAME detailed character description for all edits
- Only change the POSE in edit prompts
- Always include "same character same outfit same style"

### Parallel Execution
- Use Task tool with Bash subagent type
- Launch ALL variation agents in a single message
- Each agent handles one image edit + rename

### Error Handling
- Check base image exists before variations
- Verify all images generated before composition
- Preview before final render

---

## Pronunciation Type Workflow (Simplified)

**IMPORTANT**: The `--type pronunciation` workflow is SIMPLIFIED - it does NOT require image generation.

### Pronunciation Workflow Overview
```
0. Parse Args → 1. Select/Generate Letters → 4. TTS Audio → 5. Composition (PronunciationScene) → 6. Render
```

**Steps 2, 3, and 3.5 are SKIPPED** for pronunciation type because no character images are needed.

### Step 1 (Pronunciation): Select Letter Template

For `--type pronunciation`, select from pre-built templates:

```typescript
// Available pronunciation templates
const PRONUNCIATION_TEMPLATES = {
  'vowels': koreanVowelsTemplate,           // ㅏ, ㅓ, ㅗ, ㅜ, etc.
  'consonants': koreanConsonantsTemplate,   // ㄱ, ㄴ, ㄷ, ㄹ, etc.
  'syllable-vowels': koreanSyllableVowelsTemplate,     // 아, 어, 오, 우, etc.
  'syllable-consonants': koreanSyllableConsonantsTemplate,  // 가, 나, 다, 라, etc.
};

// Config structure for pronunciation (NO character/pose fields)
const config = {
  topic: "Korean Vowels",
  topicKorean: "한국어 모음",
  outputDir: "korean-vowels",
  intro: "한국어 모음을 배워봐요!",
  outro: "잘했어요! 이제 모음을 알아요!",
  items: [
    { id: "01_a", korean: "ㅏ", romanization: "a" },
    { id: "02_eo", korean: "ㅓ", romanization: "eo" },
    // ... more letters
  ]
};
```

### Step 4 (Pronunciation): Generate TTS Audio

Generate audio for each letter pronunciation:

```typescript
// For pronunciation, TTS reads just the letter sound
const ITEMS = [
  { id: "01_a", korean: "아" },  // TTS will say "아"
  { id: "02_eo", korean: "어" },
  // ...
];
```

### Step 5 (Pronunciation): Create PronunciationScene Composition

Use the `PronunciationScene` component instead of `ItemScene`:

```tsx
// See video-composition.md for full PronunciationScene template
// Key differences from ItemScene:
// - No character image
// - NO logo (clean, minimalist design)
// - Large centered Korean letter (~280px)
// - Pink romanization button at bottom (padding: 24px 80px, fontSize: 64px)
// - White background (#ffffff)
// - Summary: 3-column grid, 240px Korean letters, 50px romanization
```

### Pronunciation Complete Example

```bash
# 1. Create audio directory only (no images needed)
mkdir -p public/audio/korean-vowels

# 2. SKIP image generation (Steps 2, 3, 3.5)

# 3. Generate TTS for each letter
npx ts-node scripts/generate-vowels-tts.ts

# 4. Create composition with PronunciationScene
# ... create src/KoreanVowelsVideo.tsx ...

# 5. Preview
npm run dev

# 6. Render
npx remotion render KoreanVowels out/korean-vowels.mp4
```

### Auto Mode for Pronunciation

When `--type pronunciation --auto` is used:

```typescript
const PRONUNCIATION_GENERATION_PROMPT = `Generate a Korean letter pronunciation learning topic.
  Pick ONE random category from: basic vowels, compound vowels, basic consonants, aspirated consonants, syllable blocks with vowels, syllable blocks with consonants.
  Return JSON with exactly this structure:
  {
    "topic": "Korean [Category]",
    "topicKorean": "[Category in Korean]",
    "intro": "[Intro phrase in Korean]",
    "outro": "[Outro phrase in Korean]",
    "items": [10-14 items with { "korean", "romanization" }]
  }
  NOTE: No "english" or "pose" fields needed for pronunciation type.`;
```
