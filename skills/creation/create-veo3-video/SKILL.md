---
name: create-veo3-video
description: Create AI videos with Nano Banana 3 Pro (image editing) + Veo 3.1 (video with audio)
argument-hint: "[--topic TOPIC] [--scenes N] [--character NAME] [--auto]"
metadata:
  tags: veo3, nano-banana, video, ai, image-to-video, animation, google, voiceover
when:
  patterns:
    - create veo3 video
    - nano banana video
    - ai video
    - generate video with veo
    - veo3 video
  mentions:
    - veo3
    - nano banana
    - image to video
    - veo
---

# Create Veo3 Video Skill

Automate the full AI video generation pipeline:
1. **Prompts** - Generate scene descriptions with voiceover using Gemini
2. **Images** - Edit base character with Nano Banana 3 Pro
3. **Video + Audio** - Veo 3.1 generates video WITH audio from voiceover prompt

## When to Use

Use this skill when:
- Creating AI-generated videos from a base character image
- Need image-to-image editing to create character poses
- Want voiceover narration (Veo3 generates audio from prompt)
- Creating short-form vertical videos (Reels, Shorts, TikTok)

## Workflow

### Step 1: Scene Mode Selection (Required)

**IMPORTANT:** Always ask the user about scene mode first using AskUserQuestion:

```
Question: "How many scenes do you want to create?"
Header: "Scene Mode"
Options:
  - Single Scene: "Create one focused scene with action and voiceover"
  - Multiple Scenes: "Create a sequence of connected scenes for a longer video"
```

### Step 2: Gather Requirements

**For Single Scene:**
- Scene description (action and setting)
- Animation style (gentle, dynamic, subtle)
- Voiceover text (Veo3 will generate audio)
- Duration (5-8 seconds)

**For Multiple Scenes:**
- Number of scenes (2-10 recommended)
- Video concept/theme
- Content for each scene
- Voiceover for each scene

### Step 3: Execute Pipeline

```
[Generate Prompts] → [Edit Images] → [Generate Videos with Audio] → [Output]
```

## Prompt Structure (Critical)

**Veo3 generates video WITH audio from this JSON structure:**

```json
{
  "video_concept": "Korean School Expressions - 5 Essential Phrases",
  "character_description": "Base character image provided by user",
  "setting_description": "Bright classroom with wooden desks, green chalkboard, natural lighting",
  "style": "realistic",
  "scene": {
    "scene_id": 1,
    "content": {
      "korean": "감사합니다",
      "english": "Thank you",
      "romanization": "Gamsahamnida"
    },
    "image_scene_description": "Standing with hands neatly at sides, beginning of respectful deep bow from waist at 45 degrees, grateful happy expression with warm closed-lip smile. Classroom background with golden afternoon sunlight streaming through windows, chalkboard visible. Warm lighting. Medium shot, eye level.",
    "veo3_action": "Performs deep respectful bow from waist, hands remain neatly at sides. Rises slowly with bright grateful smile and gentle nod.",
    "animation": {
      "type": "bowing",
      "style": "gentle",
      "duration": 9
    },
    "voiceover": "감사합니다! This means 'Thank you!' Always say this to show appreciation to your teacher and classmates."
  }
}
```

### Key Fields

| Field | Purpose |
|-------|---------|
| `image_scene_description` | Used by Nano Banana to edit base image (pose, setting, NO character style) |
| `veo3_action` | Animation description for Veo3 |
| `voiceover` | **Veo3 generates audio from this text** |

## Arguments

```
Usage: create veo3 video [--topic TOPIC] [--scenes N] [--character NAME] [--auto]

Options:
  --topic TOPIC       Topic name for output folder (required)
  --scenes N          Number of scenes to generate (default: 3)
  --character NAME    Base character from base-characters/ folder (default: female-1)
  --auto              Auto-generate prompts using AI
  --list-characters   Show available base characters
```

## Output Structure

```
public/images/{topic}/
├── scene_01.png         # Edited base images
├── scene_02.png
└── scene_03.png

out/{topic}/
├── scene_01.mp4         # Videos WITH audio (from voiceover)
├── scene_02.mp4
├── scene_03.mp4
└── prompts.json         # Saved prompts for reference
```

## Available Base Characters

Characters are stored in `public/images/base-characters/`:
- `female-1` - Korean woman, short brown hair, lavender sweater

## CLI Usage

```bash
# Auto mode - AI generates Korean greeting scenes
npm run generate:veo3 -- --topic "korean-greetings" --scenes 3 --auto

# List available characters
npm run generate:veo3 -- --list-characters
```

## Dependencies

- `@google/genai` - Nano Banana and Veo3 (includes audio generation)

## Environment Variables

Required in `.env`:
```
GEMINI_API_KEY=your_gemini_api_key
```

## Detailed Documentation

- [rules/workflow.md](rules/workflow.md) - Complete workflow
- [rules/prompt-generation.md](rules/prompt-generation.md) - Uses create-veo3-prompt patterns
- [rules/api-integration.md](rules/api-integration.md) - API usage guide

## Related Skills

- [create-veo3-prompt](../create-veo3-prompt/SKILL.md) - Generate Veo3 prompts only (for prompt structure reference)
- [create-remotion-video](../create-remotion-video/SKILL.md) - Korean learning videos with Remotion
