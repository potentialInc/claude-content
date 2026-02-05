---
name: cefr-levels
description: CEFR level guidelines and Korean language content examples
metadata:
  tags: cefr, levels, proficiency, curriculum, pedagogy
---

# CEFR Level Guidelines for Korean

Guidelines for creating Korean learning content at appropriate CEFR (Common European Framework of Reference) levels.

## Overview

| Level | Name | Study Hours | Korean Equivalent |
|-------|------|-------------|-------------------|
| A1 | Beginner | 0-100 | TOPIK 1급 |
| A2 | Elementary | 100-200 | TOPIK 2급 |
| B1 | Intermediate | 200-400 | TOPIK 3-4급 |
| B2 | Upper-Intermediate | 400-600 | TOPIK 5-6급 |

## A1 - Beginner (입문)

### Learner Profile
- Complete beginners with no prior Korean knowledge
- Learning Hangul alphabet
- Building basic vocabulary (500-1000 words)
- Cannot form sentences independently

### Content Characteristics
- Single words or very short phrases
- High-frequency, concrete vocabulary
- Visual/tangible concepts
- Simple, repetitive structures

### Appropriate Topics

| Category | Topics |
|----------|--------|
| vocabulary | Numbers (1-10), Colors, Animals, Body parts, Family members, Food (common items), Weather (basic) |
| polite | Basic greetings (안녕/안녕하세요), Thank you (고마워/감사합니다), Goodbye (잘 가/안녕히 가세요) |
| situation | Self-introduction (이름, 나이), Simple yes/no responses |
| sentence | "This is..." (이거 __예요), "I am..." (저는 __이에요) |

### Example Content (A1)

```json
{
  "korean": "사과",
  "english": "Apple",
  "romanization": "sagwa",
  "pedagogy": {
    "level": "A1",
    "formality": "neutral",
    "grammar_point": "Basic noun",
    "usage_context": "Naming fruit when shopping or eating",
    "cultural_note": "Apples are popular gifts in Korea, especially during Chuseok"
  }
}
```

### Grammar at A1
- Noun + 이에요/예요 (to be)
- Basic particles: 이/가 (subject), 은/는 (topic)
- Simple counters: 개 (general), 마리 (animals)
- Question words: 뭐 (what), 누구 (who)

### Avoid at A1
- Complex verb conjugations
- Honorific speech (beyond basic politeness)
- Abstract vocabulary
- Compound sentences
- Idiomatic expressions

---

## A2 - Elementary (초급)

### Learner Profile
- Comfortable with Hangul
- Basic vocabulary foundation (1000-2000 words)
- Can form simple sentences
- Understands basic grammar patterns

### Content Characteristics
- Short sentences
- Common everyday situations
- Practical, functional language
- Simple verb conjugations (present, past)

### Appropriate Topics

| Category | Topics |
|----------|--------|
| vocabulary | Directions, Transportation, Rooms in a house, School subjects, Hobbies, Clothes |
| polite | Requests (주세요), Apologies (죄송합니다), Asking permission |
| situation | Restaurant ordering, Shopping, Asking directions, Making appointments |
| sentence | Want (싶어요), Can/Cannot (수 있어요/없어요), Have/Don't have (있어요/없어요) |

### Example Content (A2)

```json
{
  "korean": "물 주세요",
  "english": "Water, please",
  "romanization": "mul juseyo",
  "pedagogy": {
    "level": "A2",
    "formality": "neutral",
    "grammar_point": "Noun + 주세요 (please give)",
    "usage_context": "Requesting items at restaurants, stores, or from others",
    "cultural_note": "In Korean restaurants, you often need to call the server with '저기요' first"
  }
}
```

### Grammar at A2
- Verb conjugation: present (아/어요), past (았/었어요)
- Want: -고 싶어요
- Can/Cannot: -(으)ㄹ 수 있어요/없어요
- Object particle: 을/를
- Location particles: 에, 에서
- Time expressions: 어제, 오늘, 내일

### Avoid at A2
- Passive voice
- Complex honorifics
- Subjunctive mood
- Long dependent clauses
- Specialized vocabulary

---

## B1 - Intermediate (중급)

### Learner Profile
- Solid grammar foundation
- Vocabulary of 2000-4000 words
- Can participate in conversations
- Comfortable with different speech levels

### Content Characteristics
- Complex sentences
- Abstract concepts
- Opinions and feelings
- Conditional statements

### Appropriate Topics

| Category | Topics |
|----------|--------|
| vocabulary | Emotions, Personality traits, Work/Career, Health, Environment |
| polite | Formal speech (합니다체), Honorific verbs (드시다, 계시다), Humble speech |
| situation | Job interviews, Doctor visits, Giving opinions, Making plans |
| sentence | Because (-(으)니까, -아/어서), If (-(으)면), While (-면서) |
| grammar | Verb connectors, Reported speech, Comparative forms |

### Example Content (B1)

```json
{
  "korean": "회의가 있어서 늦을 것 같아요",
  "english": "I think I'll be late because I have a meeting",
  "romanization": "hoeiga isseoseo neujeul geot gatayo",
  "pedagogy": {
    "level": "B1",
    "formality": "neutral",
    "grammar_point": "-아/어서 (because) + -(으)ㄹ 것 같다 (seems like)",
    "usage_context": "Explaining reasons for delays, making excuses politely",
    "cultural_note": "Koreans often soften statements with 것 같아요 to be less direct"
  }
}
```

### Grammar at B1
- Connective endings: -고, -아/어서, -(으)니까, -(으)면
- Future/Conjecture: -(으)ㄹ 거예요, -(으)ㄹ 것 같아요
- Noun modifiers: -(으)ㄴ, -는, -(으)ㄹ
- Reported speech: -다고 하다, -냐고 하다, -라고 하다
- Honorific system: subject honorifics, object honorifics

### Avoid at B1
- Highly specialized jargon
- Literary forms
- Archaic expressions
- Regional dialects

---

## B2 - Upper-Intermediate (중상급)

### Learner Profile
- Near-fluent in everyday situations
- Vocabulary of 4000+ words
- Can express nuanced opinions
- Comfortable with most honorific forms

### Content Characteristics
- Nuanced expression
- Abstract discussion
- Sophisticated grammar
- Idiomatic language

### Appropriate Topics

| Category | Topics |
|----------|--------|
| vocabulary | Social issues, Technology, Culture, Philosophy, Current events |
| polite | Indirect speech, Humble forms (제가, 저희), Business Korean |
| situation | Negotiations, Debates, Presentations, Formal complaints |
| sentence | Despite (-음에도 불구하고), In order to (-기 위해서), As a result (-는 바람에) |
| grammar | Complex sentence patterns, Written vs spoken forms |

### Example Content (B2)

```json
{
  "korean": "그렇게 생각하실 수도 있지만, 다른 관점에서 보면...",
  "english": "You may think that way, but from another perspective...",
  "romanization": "geureoke saenggakhasil sudo itjiman, dareun gwanjeomeseo bomyeon",
  "pedagogy": {
    "level": "B2",
    "formality": "formal",
    "grammar_point": "-(으)ㄹ 수도 있다 (possibility) + 관점에서 (from perspective)",
    "usage_context": "Politely disagreeing in discussions, presenting alternative viewpoints",
    "cultural_note": "Koreans often acknowledge the other person's view before presenting their own to maintain harmony"
  }
}
```

### Grammar at B2
- Advanced connectors: -더라도, -든지, -건 간에
- Written style: -다, -(으)ㄴ다, -는다
- Passive constructions: -아/어지다, -게 되다
- Causative: -게 하다, -시키다
- Idiomatic expressions and proverbs

---

## Level Selection Guidelines

### When to Use Each Level

| Learner Type | Recommended Level |
|--------------|-------------------|
| Children (5-10) | A1 only |
| Children (10-15) | A1-A2 |
| Adult beginners | A1-A2 |
| Heritage speakers | B1-B2 |
| Business learners | A2-B1 |
| Academic learners | B1-B2 |

### Content Mixing

For topics spanning multiple formality levels (e.g., polite expressions), scenes can have different levels:

```json
{
  "metadata": {
    "level": "A1"  // Overall topic level
  },
  "scenes": [
    {
      "scene_id": 1,
      "pedagogy": { "level": "A1" }  // Matches overall
    },
    {
      "scene_id": 2,
      "pedagogy": { "level": "A2" }  // Slightly higher for contrast
    }
  ]
}
```

### Level Progression Within Topic

For multi-scene topics, consider:
1. Start with simpler examples (lower level)
2. Progress to more complex examples (higher level)
3. End with review of key points

Example progression for "Greetings":
1. 안녕 (A1 informal)
2. 안녕하세요 (A1 polite)
3. 처음 뵙겠습니다 (A2 formal)
4. 오랜만이에요 (A2 reunion)
5. 반갑습니다 (A1 polite)

---

## Assessment Alignment

### TOPIK Correlation

| CEFR | TOPIK Level | Skills Tested |
|------|-------------|---------------|
| A1 | TOPIK I (1급) | Basic vocabulary, simple sentences |
| A2 | TOPIK I (2급) | Daily conversations, simple texts |
| B1 | TOPIK II (3-4급) | General topics, paragraph writing |
| B2 | TOPIK II (5-6급) | Complex texts, formal writing |

### Using Assessment for Content

When creating content:
1. Reference TOPIK vocabulary lists for level-appropriate words
2. Use TOPIK grammar patterns for the target level
3. Match sentence complexity to TOPIK reading passages
4. Consider TOPIK listening dialogue styles
