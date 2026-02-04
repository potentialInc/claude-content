---
name: workflow
description: Step-by-step workflow for generating Korean learning topics
metadata:
  tags: workflow, process, generation, steps
---

# Topic Generation Workflow

Complete step-by-step process for generating Korean learning topic files.

## Overview

```
0. Parse Args → 1. Select Theme → 2. Generate Metadata → 3. Generate Intro/Outro → 4. Generate Scenes → 5. Validate → 6. Save to Library
```

---

## Step 0: Parse Arguments

Extract parameters from user request.

### Required Parameters
- `topic-name`: Topic slug (e.g., "korean-greetings")

### Optional Parameters (with defaults)
- `--level`: CEFR level (default: A1)
- `--category`: Content category (default: vocabulary)
- `--scenes`: Number of scenes (default: 10)

### Parsing Examples

| User Request | Parsed Arguments |
|--------------|------------------|
| `generate korean topic korean-greetings` | level=A1, category=vocabulary, scenes=10 |
| `generate korean topic restaurant --level A2 --category situation` | level=A2, category=situation, scenes=10 |
| `generate korean topic feelings --level B1 --scenes 8` | level=B1, category=vocabulary, scenes=8 |

---

## Step 1: Select Theme Based on Category

Based on the category, select appropriate themes and content patterns.

### Category: vocabulary
Generate single-word items with visual concepts.

**Theme Selection Prompt:**
```
Select a concrete, visual vocabulary theme for {topic-name}:
- Body parts, animals, colors, numbers, food, weather, clothes, etc.
- Focus on high-frequency, tangible nouns
- Appropriate for level {level}
```

### Category: polite
Generate formality pairs (반말/존댓말).

**Theme Selection Prompt:**
```
Generate pairs of informal/formal expressions for {topic-name}:
- Include both casual (반말) and polite (존댓말/해요체) forms
- Focus on functional phrases (greetings, thanks, requests)
- Show clear contrast between formality levels
```

### Category: situation
Generate context-specific phrases.

**Theme Selection Prompt:**
```
Generate practical phrases for the {topic-name} situation:
- Include phrases in order of use (arrival → interaction → departure)
- Focus on what a visitor/customer would need to say
- Include common responses they might hear
```

### Category: sentence
Generate complete sentences for daily life.

**Theme Selection Prompt:**
```
Generate everyday sentences related to {topic-name}:
- Focus on Subject-Object-Verb patterns
- Include variations (positive, negative, question forms)
- Use consistent verb ending (합니다체 or 해요체)
```

### Category: grammar
Generate sentences illustrating a grammar pattern.

**Theme Selection Prompt:**
```
Generate sentences demonstrating the {topic-name} grammar pattern:
- Show pattern in multiple contexts
- Progress from simple to complex usage
- Include both spoken and written examples
```

---

## Step 2: Generate Metadata

Create the topic metadata structure.

### Metadata Template

```json
{
  "topic_slug": "{topic-name}",
  "topic_name": {
    "english": "{Topic Name in English}",
    "korean": "{Topic Name in Korean}"
  },
  "level": "{level}",
  "category": "{category}",
  "learning_objectives": [
    "{Objective 1: What learner will be able to DO}",
    "{Objective 2: What learner will UNDERSTAND}",
    "{Objective 3: Cultural/situational awareness}"
  ],
  "prerequisites": [],
  "related_topics": [],
  "total_scenes": {scenes},
  "estimated_duration": "{1-2 minutes for 5 scenes, 2-3 for 10}",
  "romanization_system": "Revised Romanization of Korean",
  "created_at": "{YYYY-MM-DD}",
  "version": "1.0"
}
```

### Learning Objectives Guidelines

Write objectives using action verbs:
- **DO**: "Use X in Y situation", "Distinguish between X and Y"
- **UNDERSTAND**: "Recognize X pattern", "Know when to use X"
- **CULTURAL**: "Understand X custom", "Apply appropriate formality"

Example objectives for "korean-greetings":
```json
[
  "Use appropriate greetings based on formality level",
  "Distinguish between casual and polite greeting forms",
  "Understand bowing customs that accompany greetings"
]
```

---

## Step 3: Generate Intro/Outro

Create intro and outro content.

### Intro Template

```json
{
  "korean": "{Encouraging phrase in Korean}",
  "english": "{English translation}",
  "voiceover": "{Longer explanation - what will be learned, why it's useful}"
}
```

**Intro Guidelines:**
- Korean phrase: Short, encouraging (5-10 syllables)
- Use 배워봐요 (let's learn), 해봐요 (let's try)
- Voiceover: Explain what will be learned and preview content

### Outro Template

```json
{
  "korean": "{Praising phrase in Korean}",
  "english": "{English translation}",
  "voiceover": "{Encouragement, summary, call to practice}"
}
```

**Outro Guidelines:**
- Korean phrase: Praising, motivating (5-15 syllables)
- Use 잘했어요 (well done), 훌륭해요 (excellent)
- Voiceover: Summarize what was learned, encourage practice

---

## Step 4: Generate Scenes

Generate each scene with full content.

### Scene Generation Process

For each scene (1 to {scenes}):

#### 4a. Generate Core Content

```json
{
  "korean": "{Korean text}",
  "english": "{English translation}",
  "romanization": "{Revised Romanization}"
}
```

**Romanization Rules:**
- Use Revised Romanization of Korean (RR)
- All lowercase
- No diacritics or special marks
- No hyphens within words
- Space between words

| Hangul | Romanization |
|--------|--------------|
| ㄱ | g/k |
| ㄴ | n |
| ㄷ | d/t |
| ㄹ | r/l |
| ㅁ | m |
| ㅂ | b/p |
| ㅅ | s |
| ㅇ | ng (final) / silent (initial) |
| ㅈ | j |
| ㅊ | ch |
| ㅋ | k |
| ㅌ | t |
| ㅍ | p |
| ㅎ | h |

#### 4b. Generate Pedagogy Information

```json
{
  "level": "{A1|A2|B1|B2}",
  "formality": "{formal|informal|neutral}",
  "grammar_point": "{Grammar being demonstrated}",
  "usage_context": "{When/where to use this}",
  "cultural_note": "{Cultural information}",
  "common_mistakes": "{What learners often get wrong}"
}
```

**Pedagogy Guidelines:**
- `level`: Usually matches metadata level, but can vary within topic
- `formality`: Be precise - formal (합니다), neutral (해요), informal (해)
- `grammar_point`: Identify the key pattern (can be "Basic noun" for vocabulary)
- `usage_context`: Be specific about situations
- `cultural_note`: Add relevant Korean cultural context
- `common_mistakes`: Optional but helpful

#### 4c. Generate Visual Information

```json
{
  "pose_suggestion": "{Description of body position and gestures}",
  "expression": "{Facial expression}",
  "setting_hint": "{Background/environment suggestion}"
}
```

**Visual Guidelines:**
- **pose_suggestion**: Describe in style-neutral terms (no "cute", "Disney", etc.)
- **expression**: Match emotion to content meaning
- **setting_hint**: Match context to usage_context

| Content Type | Pose Examples |
|--------------|---------------|
| Greeting | waving, bowing, hand raised |
| Question | hand raised, tilted head, curious posture |
| Food | eating gesture, holding utensils, pointing at food |
| Feeling | body language matching emotion |
| Request | gesturing toward item, open hands |

#### 4d. Generate Voiceover Information

```json
{
  "korean_audio": "{Exact Korean text for TTS}",
  "explanation": "{English explanation for educational voiceover}"
}
```

**Voiceover Guidelines:**
- `korean_audio`: May differ from `content.korean` (e.g., add emphasis markers)
- `explanation`: Explain meaning AND usage in simple terms
- Keep explanation under 50 words
- Use "you" and direct address

---

## Step 5: Validate Content

Before saving, validate all content.

### Validation Checklist

#### Structure Validation
- [ ] `metadata.total_scenes` equals `scenes.length`
- [ ] All `scene_id` values are sequential (1, 2, 3...)
- [ ] All required fields are present
- [ ] No empty strings in required fields

#### Content Validation
- [ ] All Korean text is grammatically correct
- [ ] All romanization follows RR system
- [ ] All English translations are accurate
- [ ] Formality labels match actual speech levels

#### Consistency Validation
- [ ] Same romanization conventions throughout
- [ ] Level-appropriate vocabulary
- [ ] Related content within scenes
- [ ] Visual suggestions match content meaning

#### Length Validation
- [ ] topic_slug: 3-50 characters
- [ ] korean content: 1-100 characters
- [ ] romanization: lowercase ASCII only
- [ ] scenes array: 5-15 items

---

## Step 6: Save to Library

Write the validated content to the topic library.

### Directory Creation
```bash
mkdir -p public/topics/{topic-slug}
```

### File Writing
```
public/topics/{topic-slug}/topic.json
```

### Post-Save Verification
1. Verify file exists and is valid JSON
2. Report topic location to user
3. Suggest next steps (use with create-remotion-video or create-veo3-prompt)

---

## Complete Example Walkthrough

### User Request
```
generate korean topic korean-restaurant --level A2 --category situation --scenes 8
```

### Step 0: Parse Arguments
```
topic-name: korean-restaurant
level: A2
category: situation
scenes: 8
```

### Step 1: Select Theme
```
Situation: Restaurant ordering and dining
Phrases to cover: entering, ordering, asking for items, paying, leaving
```

### Step 2: Generate Metadata
```json
{
  "topic_slug": "korean-restaurant",
  "topic_name": {
    "english": "Korean Restaurant Phrases",
    "korean": "한국 식당 표현"
  },
  "level": "A2",
  "category": "situation",
  "learning_objectives": [
    "Order food and drinks at a Korean restaurant",
    "Request items politely using 주세요",
    "Understand basic restaurant etiquette in Korea"
  ],
  "prerequisites": ["korean-greetings"],
  "related_topics": ["korean-cafe", "korean-shopping"],
  "total_scenes": 8,
  "estimated_duration": "2-3 minutes video",
  "romanization_system": "Revised Romanization of Korean",
  "created_at": "2025-01-15",
  "version": "1.0"
}
```

### Step 3: Generate Intro/Outro
```json
{
  "intro": {
    "korean": "식당에서 쓰는 말을 배워봐요!",
    "english": "Let's learn restaurant phrases!",
    "voiceover": "Today we're learning how to order food at a Korean restaurant. These phrases will help you dine like a local!"
  },
  "outro": {
    "korean": "잘했어요! 이제 주문할 수 있어요!",
    "english": "Great job! Now you can order!",
    "voiceover": "Excellent work! You've learned 8 essential restaurant phrases. Try using them at your favorite Korean restaurant!"
  }
}
```

### Step 4: Generate Scenes (showing 2 of 8)

**Scene 1:**
```json
{
  "scene_id": 1,
  "content": {
    "korean": "저기요",
    "english": "Excuse me (to call server)",
    "romanization": "jeogiyo"
  },
  "pedagogy": {
    "level": "A2",
    "formality": "neutral",
    "grammar_point": "Attention-getting interjection",
    "usage_context": "Call server when ready to order or need something",
    "cultural_note": "In Korea, you must call the server - they won't come automatically",
    "common_mistakes": "Don't shout too loudly, but speak clearly enough to be heard"
  },
  "visual": {
    "pose_suggestion": "raising hand slightly, looking toward server direction",
    "expression": "polite, expectant",
    "setting_hint": "seated at restaurant table"
  },
  "voiceover": {
    "korean_audio": "저기요!",
    "explanation": "Say 'jeogiyo' to get the server's attention. Raise your hand a little when you say it!"
  }
}
```

**Scene 2:**
```json
{
  "scene_id": 2,
  "content": {
    "korean": "메뉴판 주세요",
    "english": "Menu, please",
    "romanization": "menyupan juseyo"
  },
  "pedagogy": {
    "level": "A2",
    "formality": "neutral",
    "grammar_point": "Noun + 주세요 (please give me)",
    "usage_context": "Request menu when first seated or want to see it again",
    "cultural_note": "Many Korean restaurants have picture menus to help with ordering"
  },
  "visual": {
    "pose_suggestion": "open hands gesturing politely, slight bow",
    "expression": "polite, friendly",
    "setting_hint": "restaurant table, Korean restaurant interior"
  },
  "voiceover": {
    "korean_audio": "메뉴판 주세요!",
    "explanation": "Add 'juseyo' after any item to politely ask for it. Menu-pan means the menu!"
  }
}
```

### Step 5: Validate
- [x] 8 scenes with sequential IDs
- [x] All Korean grammatically correct
- [x] All romanization follows RR
- [x] Level-appropriate (A2 situation phrases)

### Step 6: Save
```
Saved: public/topics/korean-restaurant/topic.json
```

---

## Quick Reference: Generation Prompts by Category

### Vocabulary Prompt
```
Generate {scenes} Korean vocabulary items for "{topic-name}" at CEFR level {level}.
For each item provide:
- Korean word
- English translation
- Romanization (Revised Romanization)
- Pedagogy: level, formality, grammar point, usage, cultural note
- Visual: neutral pose suggestion, expression, setting
- Voiceover: Korean audio text, English explanation
```

### Polite Expressions Prompt
```
Generate {scenes} Korean expressions showing formality contrast for "{topic-name}".
Alternate between casual (반말) and polite (존댓말/해요체) forms.
For each include pedagogy explaining when to use each formality level.
```

### Situation Prompt
```
Generate {scenes} Korean phrases for the "{topic-name}" situation.
Order phrases by typical use sequence (entering → interacting → leaving).
Include both what the learner says and what they might hear in response.
```

### Sentence Prompt
```
Generate {scenes} Korean sentences related to "{topic-name}" at CEFR level {level}.
Use consistent verb endings (합니다체 for formal, 해요체 for neutral).
Include positive, negative, and question forms where appropriate.
```
