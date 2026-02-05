---
name: generate-topic
description: Generate pedagogically-sound Korean learning topics for video production
argument-hint: "[topic-name] [--level A1|A2|B1|B2] [--category vocabulary|polite|situation|sentence|grammar] [--scenes 5-15]"
metadata:
  tags: korean, learning, topic, curriculum, cefr, pedagogy, content, library
when:
  patterns:
    - generate topic
    - generate korean topic
    - create topic
    - korean video topic
  mentions:
    - topic
    - vocabulary
    - learning content
    - cefr
    - curriculum
---

# Generate Topic Skill

Generate pedagogically-sound Korean learning content that can be consumed by multiple video production skills (`create-remotion-video`, `create-veo3-prompt`).

## When to Use

Use this skill when:
- Creating new Korean learning content for videos
- Building a curriculum-aligned topic library
- Need content with CEFR levels, grammar notes, and cultural context
- Want reusable content that works with different video styles

## Arguments

```
Usage: generate korean topic [topic-name] [options]

Arguments:
  topic-name          Topic slug (e.g., korean-greetings, restaurant-phrases)

Options:
  --level LEVEL       CEFR proficiency level (default: A1)
                      - A1: Beginner (basic greetings, numbers, colors)
                      - A2: Elementary (practical situations, simple conversations)
                      - B1: Intermediate (opinions, feelings, workplace)
                      - B2: Upper-Intermediate (abstract topics, nuanced expression)

  --category CAT      Topic category (default: vocabulary)
                      - vocabulary: Single words (animals, food, body parts)
                      - polite: Formality pairs (반말/존댓말)
                      - situation: Context-specific phrases (hospital, airport)
                      - sentence: Complete sentences (daily actions)
                      - grammar: Grammar pattern focus

  --scenes N          Number of scenes/items (default: 10, range: 5-15)
```

### Example Commands

| Command | Description |
|---------|-------------|
| `generate korean topic korean-greetings` | Basic greetings topic (A1, vocabulary) |
| `generate korean topic korean-greetings --level A1 --category polite` | Greetings with formality pairs |
| `generate korean topic restaurant-phrases --level A2 --category situation --scenes 12` | Restaurant scenario with 12 scenes |
| `generate korean topic feelings --level B1 --category sentence` | Intermediate feeling sentences |

## Output Structure

### Directory Structure
```
public/topics/{topic-slug}/
├── topic.json          (machine-readable content)
└── README.md           (human reference - optional)
```

### topic.json Schema

```json
{
  "metadata": {
    "topic_slug": "korean-greetings",
    "topic_name": {
      "english": "Korean Greetings",
      "korean": "한국어 인사"
    },
    "level": "A1",
    "category": "polite",
    "learning_objectives": [
      "Distinguish between formal and informal greetings",
      "Use appropriate greeting based on social context",
      "Understand bowing culture with greetings"
    ],
    "prerequisites": [],
    "related_topics": ["korean-farewells", "korean-first-meeting"],
    "total_scenes": 10,
    "estimated_duration": "2-3 minutes video",
    "romanization_system": "Revised Romanization of Korean",
    "created_at": "2025-01-15",
    "version": "1.0"
  },
  "intro": {
    "korean": "인사말을 배워봐요!",
    "english": "Let's learn greetings!",
    "voiceover": "Today we're going to learn Korean greetings. You'll learn both casual and polite forms!"
  },
  "outro": {
    "korean": "잘했어요! 이제 인사할 수 있어요!",
    "english": "Great job! Now you can greet in Korean!",
    "voiceover": "Excellent work! You've learned 10 essential Korean greetings. Practice using them with friends and family!"
  },
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
        "grammar_point": "Basic greeting interjection",
        "usage_context": "Use with friends, younger people, or people you're close to",
        "cultural_note": "Casual greeting does not require a bow, just a wave or nod",
        "common_mistakes": "Don't use with elders or in formal settings"
      },
      "visual": {
        "pose_suggestion": "waving hand casually, relaxed stance",
        "expression": "bright, friendly smile",
        "setting_hint": "school hallway, park, casual environment"
      },
      "voiceover": {
        "korean_audio": "안녕!",
        "explanation": "This means 'Hello' in the casual form. Use this with your friends!"
      }
    }
  ]
}
```

## Pedagogical Features

### 1. CEFR Level Targeting

Each topic is tagged with a CEFR level to ensure appropriate difficulty:

| Level | Learner Profile | Content Complexity |
|-------|-----------------|-------------------|
| A1 | Beginner (0-100 hours) | Single words, basic phrases |
| A2 | Elementary (100-200 hours) | Simple sentences, common situations |
| B1 | Intermediate (200-400 hours) | Complex sentences, opinions, feelings |
| B2 | Upper-Intermediate (400-600 hours) | Abstract topics, nuanced expression |

See [rules/cefr-levels.md](rules/cefr-levels.md) for detailed guidelines.

### 2. Formality Tracking

Korean has distinct formality levels that must be explicitly taught:

| Level | Korean Term | When to Use |
|-------|-------------|-------------|
| `formal` | 존댓말 (格式體) | Elders, strangers, professional settings |
| `informal` | 반말 | Friends, younger people, close relationships |
| `neutral` | 해요체 | General polite form, safe default |

### 3. Grammar Points

Each scene can include a grammar point for deeper learning:
- Verb conjugations (합니다, 해요, 해)
- Particle usage (은/는, 이/가, 을/를)
- Sentence structures (subject-object-verb)
- Honorific markers (시, 님)

### 4. Cultural Notes

Cultural context is essential for Korean language learning:
- Bowing angles (15° casual, 30° polite, 45° deep respect)
- Age-based language selection
- Situational appropriateness
- Gesture meanings

### 5. Romanization Standard

All romanization uses the **Revised Romanization of Korean** (국어의 로마자 표기법):
- Standard since 2000
- Used by Korean government
- No diacritics needed
- Examples: 한글 → hangeul, 부산 → Busan

## Workflow

### Step 1: Parse Arguments
Extract topic name, level, category, and scene count from user request.

### Step 2: Generate Content Structure
Create topic metadata and learning objectives based on category.

### Step 3: Generate Scenes
For each scene:
1. Generate Korean content appropriate to level
2. Add English translation
3. Add romanization (Revised Romanization)
4. Add pedagogical information (formality, grammar, usage, cultural note)
5. Add visual hints (pose, expression, setting)
6. Add voiceover text

### Step 4: Validate Content
- Verify romanization accuracy
- Check formality consistency
- Ensure cultural notes are accurate
- Validate grammar point descriptions

### Step 5: Save to Library
Write `topic.json` to `public/topics/{topic-slug}/`

See [rules/workflow.md](rules/workflow.md) for detailed workflow.

## Integration with Other Skills

### create-remotion-video
```bash
# Use topic library
create video --from-topic korean-greetings

# Remotion skill reads public/topics/korean-greetings/topic.json
# Adapts visual hints to Disney Pixar style
# Generates images, audio, and composition
```

### create-veo3-prompt
```bash
# Use topic library
create veo3 prompt --from-topic korean-greetings

# Veo3 skill reads public/topics/korean-greetings/topic.json
# Adapts visual hints to realistic style with camera/lighting
# Generates scene JSONs for image-to-video workflow
```

## Best Practices

### Content Quality
1. **Accuracy** - All Korean text must be grammatically correct
2. **Appropriateness** - Content matches CEFR level
3. **Completeness** - All fields populated (no empty strings)
4. **Consistency** - Same romanization system throughout

### Pedagogical Quality
1. **Progressive Difficulty** - Start simple, build complexity within topic
2. **Formality Pairing** - When teaching polite forms, include casual equivalents
3. **Usage Context** - Always explain WHEN to use each expression
4. **Cultural Integration** - Korean language is inseparable from culture

### Visual Suggestions
1. **Neutral Poses** - Describe poses without style (adapters add style)
2. **Clear Expressions** - Facial expressions should match content meaning
3. **Relevant Settings** - Setting hints should match usage context
4. **Action Clarity** - Poses should be achievable and clear

## Dependencies

- None (pure content generation)

## Related Skills

- [create-remotion-video](../create-remotion-video/SKILL.md) - Consumes topic library for Remotion videos
- [create-veo3-video](../create-veo3-video/SKILL.md) - Consumes topic library for Veo3 videos

## Detailed Documentation

- [rules/topic-schema.md](rules/topic-schema.md) - Complete JSON schema specification
- [rules/cefr-levels.md](rules/cefr-levels.md) - CEFR level guidelines and content examples
- [rules/workflow.md](rules/workflow.md) - Step-by-step generation workflow
