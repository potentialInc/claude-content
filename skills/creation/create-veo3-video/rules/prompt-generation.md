# Prompt Generation Rules

## Overview

This skill uses the existing `create-veo3-prompt` skill for generating scene prompts.

## Reference Skill

See [create-veo3-prompt](../../create-veo3-prompt/SKILL.md) for complete prompt generation documentation.

## Key Points

### Image Scene Description Format

From create-veo3-prompt rules:
- **DO include:** Pose, gestures, expression, setting, lighting, camera angle
- **DO NOT include:** Character appearance, age, hair, clothing, art style

### Scene JSON Structure

```json
{
  "scene_id": 1,
  "content": {
    "korean": "안녕하세요",
    "english": "Hello",
    "romanization": "Annyeonghaseyo"
  },
  "image_scene_description": "Standing with slight bow, warm smile. Office background. Medium shot.",
  "veo3_action": "Character completes bow, hand waves gently",
  "animation": { "type": "full_body", "style": "gentle", "duration": 8 },
  "voiceover": "안녕하세요! This means Hello in Korean."
}
```

## Integration

When using `--auto` mode, this skill invokes the create-veo3-prompt patterns to generate scene prompts before proceeding to image editing and video generation.
