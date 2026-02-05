---
name: topic-schema
description: Complete JSON schema specification for Korean learning topic files
metadata:
  tags: schema, json, specification, validation
---

# Topic Schema Specification

Complete JSON schema for `topic.json` files in the topic library.

## File Location

```
public/topics/{topic-slug}/topic.json
```

## Complete Schema

```typescript
interface TopicFile {
  metadata: TopicMetadata;
  intro: IntroOutro;
  outro: IntroOutro;
  scenes: Scene[];
}

interface TopicMetadata {
  // Unique identifier (URL-safe, lowercase, hyphens)
  topic_slug: string;

  // Display names
  topic_name: {
    english: string;  // "Korean Greetings"
    korean: string;   // "한국어 인사"
  };

  // CEFR level: A1, A2, B1, B2
  level: "A1" | "A2" | "B1" | "B2";

  // Content category
  category: "vocabulary" | "polite" | "situation" | "sentence" | "grammar";

  // Learning objectives (3-5 items)
  learning_objectives: string[];

  // Prerequisites (other topic slugs)
  prerequisites: string[];

  // Related topics (other topic slugs)
  related_topics: string[];

  // Number of scenes in this topic
  total_scenes: number;

  // Estimated video duration
  estimated_duration: string;  // "2-3 minutes video"

  // Romanization system used
  romanization_system: "Revised Romanization of Korean";

  // Creation date (ISO 8601)
  created_at: string;  // "2025-01-15"

  // Schema version
  version: string;  // "1.0"
}

interface IntroOutro {
  // Korean phrase
  korean: string;

  // English translation
  english: string;

  // Full voiceover text (may be longer than korean/english)
  voiceover: string;
}

interface Scene {
  // Scene number (1-indexed)
  scene_id: number;

  // Core content
  content: SceneContent;

  // Educational information
  pedagogy: ScenePedagogy;

  // Visual suggestions (style-neutral)
  visual: SceneVisual;

  // Audio/narration
  voiceover: SceneVoiceover;
}

interface SceneContent {
  // Korean text (Hangul)
  korean: string;

  // English translation
  english: string;

  // Romanization (Revised Romanization of Korean)
  romanization: string;
}

interface ScenePedagogy {
  // CEFR level for this specific item
  level: "A1" | "A2" | "B1" | "B2";

  // Formality level
  formality: "formal" | "informal" | "neutral";

  // Grammar point being taught (optional)
  grammar_point: string;

  // When/where to use this expression
  usage_context: string;

  // Cultural information
  cultural_note: string;

  // Common learner mistakes (optional)
  common_mistakes?: string;
}

interface SceneVisual {
  // Pose description (style-neutral)
  pose_suggestion: string;

  // Facial expression
  expression: string;

  // Environment/background hint
  setting_hint: string;
}

interface SceneVoiceover {
  // Exact Korean text for TTS
  korean_audio: string;

  // Explanation in English (for educational voiceover)
  explanation: string;
}
```

## Field Specifications

### topic_slug
- Format: lowercase letters, numbers, hyphens only
- Pattern: `^[a-z0-9]+(-[a-z0-9]+)*$`
- Examples: `korean-greetings`, `restaurant-phrases`, `body-parts`

### level
- Values: `A1`, `A2`, `B1`, `B2`
- See [cefr-levels.md](cefr-levels.md) for detailed guidance

### category
| Category | Description | Example Topics |
|----------|-------------|----------------|
| `vocabulary` | Single words | animals, colors, numbers, body-parts |
| `polite` | Formality pairs | greetings, thanks, apologies |
| `situation` | Context-specific | restaurant, hospital, airport |
| `sentence` | Complete sentences | daily-actions, feelings |
| `grammar` | Grammar focus | verb-conjugation, particles |

### formality
| Value | Korean Term | Description |
|-------|-------------|-------------|
| `formal` | 존댓말/격식체 | Polite formal (합니다/습니다) |
| `informal` | 반말 | Casual speech (해/어) |
| `neutral` | 해요체 | Polite informal (해요/어요) |

### romanization
- System: Revised Romanization of Korean (RR)
- All lowercase
- No diacritics
- No hyphens within words
- Examples:
  - 안녕하세요 → annyeonghaseyo
  - 감사합니다 → gamsahamnida
  - 서울 → seoul
  - 부산 → busan

## Validation Rules

### Required Fields
All fields are required unless marked optional in the schema.

### String Constraints
- `topic_slug`: 3-50 characters
- `korean`: 1-100 characters
- `english`: 1-200 characters
- `romanization`: 1-100 characters, lowercase ASCII only
- `voiceover`: 1-500 characters
- `pose_suggestion`: 10-200 characters
- `expression`: 5-100 characters
- `setting_hint`: 10-200 characters

### Array Constraints
- `learning_objectives`: 3-5 items
- `scenes`: 5-15 items

### Consistency Rules
1. `metadata.total_scenes` must equal `scenes.length`
2. `scene_id` values must be sequential starting from 1
3. All `romanization` fields must use consistent romanization rules
4. `metadata.level` should match the majority of `pedagogy.level` values

## Example: Complete topic.json

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
    "total_scenes": 5,
    "estimated_duration": "1-2 minutes video",
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
    "voiceover": "Excellent work! You've learned 5 essential Korean greetings. Practice using them every day!"
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
    },
    {
      "scene_id": 2,
      "content": {
        "korean": "안녕하세요",
        "english": "Hello (polite)",
        "romanization": "annyeonghaseyo"
      },
      "pedagogy": {
        "level": "A1",
        "formality": "neutral",
        "grammar_point": "Polite present tense with 하세요",
        "usage_context": "Use with elders, strangers, or in professional settings",
        "cultural_note": "Usually accompanied by a slight bow (15-30 degrees)",
        "common_mistakes": "Forgetting the bow when greeting elders"
      },
      "visual": {
        "pose_suggestion": "slight bow with hands at sides or together",
        "expression": "warm, respectful smile",
        "setting_hint": "office entrance, school entrance, meeting room"
      },
      "voiceover": {
        "korean_audio": "안녕하세요!",
        "explanation": "This is the polite way to say 'Hello'. Use this when meeting someone new or speaking to someone older than you."
      }
    }
  ]
}
```

## Extensibility

The schema is designed to be extensible. Future versions may add:

### Planned Fields
- `audio_file`: Pre-generated audio file path
- `difficulty_score`: Numeric difficulty within level
- `frequency_rank`: How common this expression is
- `register`: Additional formality distinctions
- `synonyms`: Alternative expressions
- `antonyms`: Opposite expressions
- `example_sentences`: Usage examples

### Version Compatibility
- Current version: `1.0`
- Consuming skills should check `metadata.version`
- New optional fields will not require version bump
- Required field changes will increment major version
