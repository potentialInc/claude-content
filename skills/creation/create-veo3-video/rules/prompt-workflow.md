# Veo3 Prompt Workflow

## Step-by-Step Workflow

### Step 0: Check for Library Mode (--from-topic)

If the user provides `--from-topic {slug}`, load content from the topic library.

#### Loading from Topic Library
```typescript
const topicPath = `public/topics/${slug}/topic.json`;
const topicData = JSON.parse(fs.readFileSync(topicPath, 'utf-8'));

// Topic library provides style-neutral content
// Adapt for Veo3's realistic style with camera/lighting details
```

#### Adapting Visual Hints for Veo3

Topic library provides neutral visual hints. Expand them for Veo3:

```typescript
function adaptForVeo3(scene) {
  const { pose_suggestion, expression, setting_hint } = scene.visual;

  // Expand setting hint to full environment
  const settingMap = {
    'school hallway': 'Modern school hallway with lockers, natural light from windows, students in soft focus background',
    'park': 'Green park with trees and benches, dappled sunlight through leaves',
    'restaurant': 'Korean restaurant interior with traditional decor, warm ambient lighting',
    // ... more mappings
  };

  const setting = settingMap[setting_hint] || setting_hint;

  // Add professional camera and lighting
  return {
    image_scene_description: `${pose_suggestion}, ${expression}. ${setting}. Soft natural lighting. Medium shot, eye level camera angle.`,
    veo3_action: generateActionFromPose(pose_suggestion),
    animation: inferAnimationType(pose_suggestion)
  };
}
```

#### Animation Type Inference

```typescript
function inferAnimationType(pose: string): AnimationType {
  if (pose.includes('bow')) return { type: 'bowing', style: 'gentle' };
  if (pose.includes('wave')) return { type: 'hand_movement', style: 'natural' };
  if (pose.includes('walk')) return { type: 'walking', style: 'natural' };
  if (pose.includes('point')) return { type: 'hand_movement', style: 'dynamic' };
  if (pose.includes('raise')) return { type: 'hand_movement', style: 'dynamic' };
  return { type: 'subtle_motion', style: 'gentle' };
}
```

#### Skip to Step 3

After loading and adapting from library, proceed directly to Step 3 (Generate Prompt Structure).

---

### Step 1: Scene Mode Selection (Manual Mode Only)

**ALWAYS** start by asking the user about scene mode using AskUserQuestion:

```
Question: "How many scenes do you want to create?"
Header: "Scene Mode"
Options:
  - Single Scene: "Create one focused scene with action, animation, and voiceover"
  - Multiple Scenes: "Create a sequence of connected scenes for a longer video"
```

### Step 2: Gather Requirements (Based on Selection)

#### For Single Scene:

Ask follow-up questions:
1. **Scene Description**: "What should happen in this scene? Describe the action and setting."
2. **Character/Subject**: "Who or what is the main subject? (e.g., person explaining, product, landscape)"
3. **Animation Style**: "What animation style do you prefer? (gentle, dynamic, subtle, dramatic)"
4. **Voiceover**: "What should the narration say?"
5. **Duration**: "How long should this scene be? (5-15 seconds recommended)"

#### For Multiple Scenes:

Ask follow-up questions:
1. **Number of Scenes**: "How many scenes do you need? (2-10 recommended)"
2. **Video Concept**: "What is the overall theme or purpose of this video?"
3. **Scene Breakdown**: "Briefly describe what should happen in each scene"
4. **Consistency Requirements**: "Describe the character/background that should stay consistent"
5. **Total Duration**: "What's your target total video length?"

### Step 3: Generate Prompt Structure

Based on gathered information, generate the JSON prompt structure.

#### Single Scene Template:

```json
{
  "scene_id": 1,
  "action": "[Detailed action description with setting and character position]",
  "animation": {
    "type": "[animation_type]",
    "style": "[style]",
    "duration": [seconds]
  },
  "voiceover": "[Narration text]"
}
```

#### Multiple Scenes Template:

```json
{
  "video_concept": "[Theme]",
  "consistency_notes": "[What must remain consistent across scenes]",
  "scenes": [
    // Array of scene objects
  ],
  "total_duration": "[X seconds]"
}
```

### Step 4: Generate prompts.json (Required)

**ALWAYS** create a `prompts.json` file to track all prompts used:

```json
{
  "topic": "[topic-slug]",
  "character": "[character-id]",
  "scenes": [
    {
      "scene_id": 1,
      "content": {
        "korean": "[Korean text]",
        "english": "[English translation]",
        "romanization": "[Romanization]"
      },
      "image_scene_description": "[Exact prompt used for image generation]",
      "veo3_action": "[Exact prompt used for Veo3 action]",
      "animation": {
        "type": "[type]",
        "style": "[style]",
        "duration": [seconds]
      },
      "voiceover": "[Voiceover text]"
    }
  ]
}
```

**Save Location:** `out/[topic-name]/prompts.json`

**Purpose:**
- Audit trail for reviewing which prompts were used
- Enables prompt iteration and debugging
- Reproducibility for regenerating content

### Step 5: Review and Refine

Present the generated prompt to the user and offer to:
- Adjust timing/duration
- Modify action descriptions
- Change animation styles
- Edit voiceover text
- Add or remove scenes (for multiple scenes)

## Animation Type Reference

| Type | Use Case |
|------|----------|
| `hand_movement` | Explaining, gesturing, pointing |
| `head_turn` | Reactions, looking at something |
| `walking` | Moving through space |
| `talking` | Conversation, narration focus |
| `zoom_in` | Emphasizing details |
| `zoom_out` | Revealing context |
| `pan` | Showing environment |
| `subtle_motion` | Ambient, background movement |
| `full_body` | Dynamic physical actions |

## Style Reference

| Style | Description | Best For |
|-------|-------------|----------|
| `gentle` | Smooth, calming | Explanations, tutorials |
| `dynamic` | Energetic, bold | Promotions, excitement |
| `subtle` | Minimal movement | Professional, corporate |
| `dramatic` | Intense, impactful | Reveals, climax moments |
| `natural` | Realistic motion | Documentary, testimonials |

## Duration Guidelines

- **Single Scene**: 5-15 seconds optimal
- **Multiple Scenes**:
  - Intro scene: 5-8 seconds
  - Content scenes: 8-12 seconds each
  - Closing scene: 5-10 seconds
- **Total Video**: 30-120 seconds recommended for engagement

## Voiceover Pacing

- Average speaking rate: ~150 words per minute
- 5-second scene: ~12-15 words
- 10-second scene: ~25 words
- 15-second scene: ~35-40 words

## Consistency Best Practices

For multiple scenes, include in action descriptions:
- "Consistency in face and background"
- "Same character, same setting"
- "Character maintains [specific attribute]"
- "Background remains [description]"
