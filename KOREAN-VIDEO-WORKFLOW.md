# Korean Learning Video Production Workflow

Complete guide for creating Korean language learning videos using the integrated skill system.

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────────────┐
│                    CONTENT GENERATION LAYER                         │
│                                                                     │
│     ┌─────────────────────────────────────────────────────────┐    │
│     │         generate-korean-video-topic                      │    │
│     │         ─────────────────────────────                    │    │
│     │  • CEFR-aligned content (A1, A2, B1, B2)                │    │
│     │  • Pedagogy: grammar, formality, cultural notes          │    │
│     │  • Style-neutral visual hints                            │    │
│     │  • Romanization (Revised Romanization of Korean)         │    │
│     └──────────────────────┬──────────────────────────────────┘    │
│                            │                                        │
│                            ▼                                        │
│              public/topics/{slug}/topic.json                        │
│                            │                                        │
└────────────────────────────┼────────────────────────────────────────┘
                             │
           ┌─────────────────┴─────────────────┐
           │                                   │
           ▼                                   ▼
┌─────────────────────────────┐   ┌─────────────────────────────┐
│    VIDEO PRODUCTION A       │   │    VIDEO PRODUCTION B       │
│                             │   │                             │
│   create-remotion-video     │   │   create-veo3-prompt        │
│   ─────────────────────     │   │   ────────────────────      │
│                             │   │                             │
│   Style: Disney Pixar       │   │   Style: Realistic          │
│   Output: MP4 via Remotion  │   │   Output: Scene JSONs       │
│   Images: Gemini AI         │   │   Animation: Google Veo3    │
│   Audio: ElevenLabs TTS     │   │   Base Image: User provided │
│                             │   │                             │
│   --from-topic {slug}       │   │   --from-topic {slug}       │
└─────────────────────────────┘   └─────────────────────────────┘
```

## Workflow Steps

### Step 1: Generate Topic Content

Use `generate-korean-video-topic` to create curriculum-aligned content.

```bash
# Basic vocabulary topic
generate korean topic korean-animals --level A1 --category vocabulary

# Formality pairs (반말/존댓말)
generate korean topic korean-greetings --level A1 --category polite

# Situational phrases
generate korean topic korean-restaurant --level A2 --category situation

# Common sentences
generate korean topic daily-actions --level A2 --category sentence
```

**Output:** `public/topics/{slug}/topic.json`

### Step 2: Choose Production Path

#### Path A: Remotion (Cartoon Style)

Best for:
- Children's content
- Social media reels
- Vocabulary flashcard videos
- Animated educational content

```bash
create video --from-topic korean-greetings
```

**Process:**
1. Loads topic.json
2. Adapts poses for Disney Pixar style
3. Generates base character with Gemini
4. Edits base for each scene (parallel)
5. Removes backgrounds with rembg
6. Generates TTS audio with ElevenLabs
7. Creates Remotion composition
8. Renders to MP4

**Output:** `out/{topic-slug}.mp4`

#### Path B: Veo3 (Realistic Style)

Best for:
- Realistic character videos
- Professional educational content
- Adult learner content
- AI-animated realistic scenes

```bash
create veo3 prompt --from-topic korean-greetings
```

**Process:**
1. Loads topic.json
2. Adapts poses for realistic style (adds camera/lighting)
3. Generates scene JSONs with Veo3 specifications
4. Creates prompts.json for tracking
5. User edits base image per scene
6. User runs Veo3 image-to-video

**Output:** `out/{topic-slug}/scene_*.json` + `prompts.json`

## Topic Library Schema

### topic.json Structure

```json
{
  "metadata": {
    "topic_slug": "korean-greetings",
    "topic_name": { "english": "...", "korean": "..." },
    "level": "A1",
    "category": "polite",
    "learning_objectives": ["..."],
    "total_scenes": 10
  },
  "intro": {
    "korean": "...",
    "english": "...",
    "voiceover": "..."
  },
  "outro": { ... },
  "scenes": [
    {
      "scene_id": 1,
      "content": {
        "korean": "안녕",
        "english": "Hello (casual)",
        "romanization": "annyeong"
      },
      "pedagogy": {
        "level": "A1",
        "formality": "informal",
        "grammar_point": "Basic greeting",
        "usage_context": "Use with friends",
        "cultural_note": "No bow required"
      },
      "visual": {
        "pose_suggestion": "waving hand casually",
        "expression": "bright smile",
        "setting_hint": "school hallway"
      },
      "voiceover": {
        "korean_audio": "안녕!",
        "explanation": "This means Hello in casual form."
      }
    }
  ]
}
```

## CEFR Level Guide

| Level | Target Audience | Content Type |
|-------|-----------------|--------------|
| A1 | Complete beginners | Single words, basic phrases, high-frequency vocabulary |
| A2 | Elementary learners | Simple sentences, practical situations |
| B1 | Intermediate learners | Complex sentences, opinions, feelings |
| B2 | Upper-intermediate | Abstract topics, nuanced expression |

## Category Types

| Category | Description | Example Topics |
|----------|-------------|----------------|
| `vocabulary` | Single words | Animals, Colors, Numbers, Body Parts |
| `polite` | Formality pairs (반말/존댓말) | Greetings, Thanks, Goodbyes |
| `situation` | Context-specific phrases | Restaurant, Hospital, Airport |
| `sentence` | Complete sentences | Daily Actions, Feelings |
| `grammar` | Grammar pattern focus | Verb Conjugation, Particles |

## Style Adaptation

### Visual Hints → Remotion (Cartoon)

```
Input:  "waving hand casually, bright smile"
Output: "waving hand casually, bright smile, cute expressive eyes,
         Disney Pixar 3D animation style"
```

### Visual Hints → Veo3 (Realistic)

```
Input:  "waving hand casually, bright smile, school hallway"
Output: "Waving hand casually at shoulder height, bright friendly smile.
         Modern school hallway with lockers and students in soft focus.
         Soft natural lighting from left side. Medium shot, eye level."
```

## Directory Structure

```
remotion/
├── public/
│   ├── topics/                    # Topic library
│   │   ├── korean-greetings/
│   │   │   └── topic.json
│   │   ├── korean-restaurant/
│   │   │   └── topic.json
│   │   └── ...
│   ├── images/                    # Generated images
│   │   └── {topic-slug}/
│   │       ├── base_001.png
│   │       ├── 01_item.png
│   │       └── ...
│   └── audio/                     # Generated audio
│       └── {topic-slug}/
│           ├── intro.mp3
│           ├── 01_item.mp3
│           └── outro.mp3
├── src/
│   ├── {TopicName}Video.tsx       # Remotion compositions
│   └── Root.tsx                   # Composition registry
├── out/                           # Rendered videos & Veo3 outputs
│   ├── {topic-slug}.mp4
│   └── {topic-slug}/
│       ├── scene_01.json
│       └── prompts.json
└── .claude/
    └── content/
        └── skills/
            └── creation/
                ├── generate-korean-video-topic/
                ├── create-remotion-video/
                └── create-veo3-prompt/
```

## Quick Reference Commands

### Generate Topic Library Content

```bash
# Beginner vocabulary
generate korean topic korean-colors --level A1 --category vocabulary --scenes 10

# Polite expressions
generate korean topic korean-thanks --level A1 --category polite --scenes 8

# Situational phrases
generate korean topic korean-cafe --level A2 --category situation --scenes 12
```

### Create Videos

```bash
# Cartoon style (Remotion)
create video --from-topic korean-greetings

# Realistic style (Veo3)
create veo3 prompt --from-topic korean-greetings
```

### Manual Mode (without topic library)

```bash
# Remotion - auto generate topic
create video --type vocabulary --auto

# Remotion - manual input
create video about Korean animals

# Veo3 - manual input
create veo3 prompt "Korean school expressions"
```

## Best Practices

### Content Quality
1. Always use CEFR-appropriate vocabulary
2. Include both formal and informal forms for key expressions
3. Add cultural context for every expression
4. Verify romanization follows Revised Romanization standard

### Production Quality
1. Use topic library (`--from-topic`) for consistency
2. Remove image backgrounds with rembg before compositing
3. Preview video before final render
4. Test audio sync with Korean speech patterns

### Pedagogical Quality
1. Progress from simple to complex within topics
2. Pair casual/formal forms in polite category
3. Include usage context for every phrase
4. Add common learner mistakes to prevent errors

## Related Skills

| Skill | Purpose |
|-------|---------|
| [generate-korean-video-topic](skills/creation/generate-korean-video-topic/SKILL.md) | Generate topic library content |
| [create-remotion-video](skills/creation/create-remotion-video/SKILL.md) | Create cartoon-style videos |
| [create-veo3-prompt](skills/creation/create-veo3-prompt/SKILL.md) | Create realistic-style video prompts |
