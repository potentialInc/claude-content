---
name: topic-templates
description: Pre-built topic templates and AI generation prompts for Korean learning videos
metadata:
  tags: templates, topics, korean, vocabulary, phrases, generation, auto
---

# Topic Templates

Pre-built templates for common Korean learning video topics, plus AI generation prompts for auto mode.

---

## Video Type Categories

Templates and themes are organized into categories for the `--type` argument:

| Type | Description | Template Examples |
|------|-------------|-------------------|
| `vocabulary` | Korean vocabulary words | Animals, Numbers, Colors, Family, Body Parts, Food, Weather |
| `polite` | Formal vs informal speech | Polite Expressions (반말/존댓말 pairs) |
| `situation` | Situational phrases | Restaurant, Airport, Hospital, Shopping |
| `other` | Custom topics | User-defined |

---

## AI Generation Themes

When using `--auto` mode, AI randomly selects a theme within the category:

### Vocabulary Themes
- Body parts (신체 부위)
- Weather (날씨)
- Food (음식)
- Household items (가정용품)
- School supplies (학용품)
- Transportation (교통수단)
- Fruits (과일)
- Vegetables (채소)
- Clothes (옷)
- Toys (장난감)

### Polite Themes
- Greetings (인사)
- Thanks & Apologies (감사/사과)
- Requests (부탁)
- Farewells (작별)
- Daily expressions (일상 표현)

### Situation Themes
- Restaurant (식당)
- Hospital (병원)
- Airport (공항)
- Hotel (호텔)
- Cafe (카페)
- Convenience store (편의점)
- Bank (은행)
- Subway (지하철)
- Shopping mall (쇼핑몰)

---

## Template Structure

```typescript
interface TopicTemplate {
  topic: string;
  topicKorean: string;
  intro: string;      // Korean intro phrase
  outro: string;      // Korean outro phrase
  character: {
    style: string;
    description: string;
  };
  items: {
    id: string;
    korean: string;
    english: string;
    romanization: string;
    pose: string;
  }[];
}
```

---

## Animals (동물)

```typescript
const animalsTemplate = {
  topic: "Korean Animals",
  topicKorean: "한국어 동물",
  intro: "동물을 배워봐요!",
  outro: "잘했어요! 이제 동물 이름을 알아요!",
  character: {
    style: "Disney Pixar style",
    description: "Korean child, friendly expression, casual clothes"
  },
  items: [
    { id: "01_dog", korean: "강아지", english: "Dog", romanization: "gangaji", pose: "petting a puppy, happy smile" },
    { id: "02_cat", korean: "고양이", english: "Cat", romanization: "goyangi", pose: "holding a kitten gently" },
    { id: "03_bird", korean: "새", english: "Bird", romanization: "sae", pose: "pointing at a bird in the sky" },
    { id: "04_fish", korean: "물고기", english: "Fish", romanization: "mulgogi", pose: "looking at a fish bowl" },
    { id: "05_rabbit", korean: "토끼", english: "Rabbit", romanization: "tokki", pose: "holding a cute rabbit" },
    { id: "06_elephant", korean: "코끼리", english: "Elephant", romanization: "kokkiri", pose: "making elephant trunk gesture" },
    { id: "07_lion", korean: "사자", english: "Lion", romanization: "saja", pose: "roaring like a lion, playful" },
    { id: "08_monkey", korean: "원숭이", english: "Monkey", romanization: "wonsungi", pose: "monkey pose, scratching head" },
    { id: "09_bear", korean: "곰", english: "Bear", romanization: "gom", pose: "bear hug pose, arms wide" },
    { id: "10_tiger", korean: "호랑이", english: "Tiger", romanization: "horangi", pose: "tiger claws pose, fierce but cute" },
  ]
};
```

---

## Polite Expressions (공손한 표현)

```typescript
const politeExpressionsTemplate = {
  topic: "Korean Polite Expressions",
  topicKorean: "공손한 표현",
  intro: "공손한 표현을 배워봐요!",
  outro: "잘했어요! 이제 공손하게 말할 수 있어요!",
  character: {
    style: "Disney Pixar style",
    description: "Korean girl, age 8, short black hair with red ribbon, yellow hanbok dress"
  },
  items: [
    { id: "01_hello_casual", korean: "안녕", english: "Hello (casual)", romanization: "annyeong", pose: "waving hand casually, bright smile" },
    { id: "02_hello_polite", korean: "안녕하세요", english: "Hello (polite)", romanization: "annyeonghaseyo", pose: "respectful bow with hands together" },
    { id: "03_thanks_casual", korean: "고마워", english: "Thank you (casual)", romanization: "gomawo", pose: "thumbs up with happy smile" },
    { id: "04_thanks_polite", korean: "감사합니다", english: "Thank you (polite)", romanization: "gamsahamnida", pose: "deep bow with grateful expression" },
    { id: "05_bye_casual", korean: "잘 가", english: "Goodbye (casual)", romanization: "jal ga", pose: "waving goodbye casually" },
    { id: "06_bye_polite", korean: "안녕히 가세요", english: "Goodbye (polite)", romanization: "annyeonghi gaseyo", pose: "polite bow for farewell" },
    { id: "07_meet_casual", korean: "반가워", english: "Nice to meet you (casual)", romanization: "bangawo", pose: "excited expression, arms open" },
    { id: "08_meet_polite", korean: "반갑습니다", english: "Nice to meet you (polite)", romanization: "bangapseumnida", pose: "polite handshake gesture with bow" },
    { id: "09_night_casual", korean: "잘 자", english: "Good night (casual)", romanization: "jal ja", pose: "sleepy yawn, hand covering mouth" },
    { id: "10_night_polite", korean: "안녕히 주무세요", english: "Good night (polite)", romanization: "annyeonghi jumuseyo", pose: "gentle bow with peaceful smile" },
  ]
};
```

---

## Restaurant Phrases (식당 표현)

```typescript
const restaurantTemplate = {
  topic: "Korean Restaurant Phrases",
  topicKorean: "식당 표현",
  intro: "식당에서 쓰는 말을 배워봐요!",
  outro: "잘했어요! 이제 식당에서 주문할 수 있어요!",
  character: {
    style: "Disney Pixar style",
    description: "Korean person, casual clothes, friendly expression"
  },
  items: [
    { id: "01_menu", korean: "메뉴판 주세요", english: "Menu please", romanization: "menyupan juseyo", pose: "requesting gesture with hands" },
    { id: "02_order", korean: "주문할게요", english: "I'll order", romanization: "jumunhalgeyo", pose: "pointing at menu, ready to order" },
    { id: "03_this", korean: "이거 주세요", english: "This please", romanization: "igeo juseyo", pose: "pointing at item on menu" },
    { id: "04_water", korean: "물 주세요", english: "Water please", romanization: "mul juseyo", pose: "drinking gesture" },
    { id: "05_delicious", korean: "맛있어요", english: "It's delicious", romanization: "masisseoyo", pose: "happy eating gesture, thumbs up" },
    { id: "06_check", korean: "계산해 주세요", english: "Check please", romanization: "gyesanhae juseyo", pose: "calling waiter gesture" },
    { id: "07_card", korean: "카드로 할게요", english: "By card", romanization: "kadeuro halgeyo", pose: "showing card gesture" },
    { id: "08_cash", korean: "현금으로 할게요", english: "By cash", romanization: "hyeongeumeuro halgeyo", pose: "showing money gesture" },
    { id: "09_takeout", korean: "포장해 주세요", english: "To go please", romanization: "pojanghae juseyo", pose: "packing gesture" },
    { id: "10_thanks", korean: "잘 먹겠습니다", english: "I'll eat well", romanization: "jal meokgesseumnida", pose: "grateful bow before eating" },
  ]
};
```

---

## Numbers (숫자)

```typescript
const numbersTemplate = {
  topic: "Korean Numbers",
  topicKorean: "한국어 숫자",
  intro: "숫자를 배워봐요!",
  outro: "잘했어요! 이제 숫자를 알아요!",
  character: {
    style: "Disney Pixar style",
    description: "Korean child, school uniform, cheerful expression"
  },
  items: [
    { id: "01_one", korean: "하나", english: "One", romanization: "hana", pose: "holding up one finger" },
    { id: "02_two", korean: "둘", english: "Two", romanization: "dul", pose: "holding up two fingers" },
    { id: "03_three", korean: "셋", english: "Three", romanization: "set", pose: "holding up three fingers" },
    { id: "04_four", korean: "넷", english: "Four", romanization: "net", pose: "holding up four fingers" },
    { id: "05_five", korean: "다섯", english: "Five", romanization: "daseot", pose: "holding up five fingers" },
    { id: "06_six", korean: "여섯", english: "Six", romanization: "yeoseot", pose: "showing six with both hands" },
    { id: "07_seven", korean: "일곱", english: "Seven", romanization: "ilgop", pose: "showing seven with both hands" },
    { id: "08_eight", korean: "여덟", english: "Eight", romanization: "yeodeol", pose: "showing eight with both hands" },
    { id: "09_nine", korean: "아홉", english: "Nine", romanization: "ahop", pose: "showing nine with both hands" },
    { id: "10_ten", korean: "열", english: "Ten", romanization: "yeol", pose: "showing ten fingers spread" },
  ]
};
```

---

## Colors (색깔)

```typescript
const colorsTemplate = {
  topic: "Korean Colors",
  topicKorean: "한국어 색깔",
  intro: "색깔을 배워봐요!",
  outro: "잘했어요! 이제 색깔을 알아요!",
  character: {
    style: "Disney Pixar style",
    description: "Korean child, artist smock, holding paintbrush"
  },
  items: [
    { id: "01_red", korean: "빨간색", english: "Red", romanization: "ppalgansaek", pose: "holding red paint, happy" },
    { id: "02_orange", korean: "주황색", english: "Orange", romanization: "juhwangsaek", pose: "holding orange fruit" },
    { id: "03_yellow", korean: "노란색", english: "Yellow", romanization: "noransaek", pose: "pointing at sun" },
    { id: "04_green", korean: "초록색", english: "Green", romanization: "choroksaek", pose: "holding leaf" },
    { id: "05_blue", korean: "파란색", english: "Blue", romanization: "paransaek", pose: "pointing at sky" },
    { id: "06_purple", korean: "보라색", english: "Purple", romanization: "borasaek", pose: "holding grapes" },
    { id: "07_pink", korean: "분홍색", english: "Pink", romanization: "bunhongsaek", pose: "holding pink flower" },
    { id: "08_white", korean: "하얀색", english: "White", romanization: "hayansaek", pose: "holding cloud shape" },
    { id: "09_black", korean: "검은색", english: "Black", romanization: "geomeunsaek", pose: "wearing black hat" },
    { id: "10_brown", korean: "갈색", english: "Brown", romanization: "galsaek", pose: "holding chocolate" },
  ]
};
```

---

## Family (가족)

```typescript
const familyTemplate = {
  topic: "Korean Family",
  topicKorean: "한국어 가족",
  intro: "가족을 배워봐요!",
  outro: "잘했어요! 이제 가족 이름을 알아요!",
  character: {
    style: "Disney Pixar style",
    description: "Korean child, family photo in background"
  },
  items: [
    { id: "01_mom", korean: "엄마", english: "Mom", romanization: "eomma", pose: "hugging gesture, loving" },
    { id: "02_dad", korean: "아빠", english: "Dad", romanization: "appa", pose: "strong pose, protective" },
    { id: "03_sister", korean: "언니/누나", english: "Older Sister", romanization: "eonni/nuna", pose: "walking together gesture" },
    { id: "04_brother", korean: "오빠/형", english: "Older Brother", romanization: "oppa/hyeong", pose: "piggyback gesture" },
    { id: "05_younger", korean: "동생", english: "Younger Sibling", romanization: "dongsaeng", pose: "playing together" },
    { id: "06_grandma", korean: "할머니", english: "Grandmother", romanization: "halmeoni", pose: "gentle, caring gesture" },
    { id: "07_grandpa", korean: "할아버지", english: "Grandfather", romanization: "harabeoji", pose: "wise, kind expression" },
    { id: "08_aunt", korean: "이모/고모", english: "Aunt", romanization: "imo/gomo", pose: "gift giving gesture" },
    { id: "09_uncle", korean: "삼촌", english: "Uncle", romanization: "samchon", pose: "playful, fun gesture" },
    { id: "10_cousin", korean: "사촌", english: "Cousin", romanization: "sachon", pose: "playing together, friends" },
  ]
};
```

---

## Using Templates

When user requests a topic, look for matching template and use it to:
1. Extract items and character info
2. Generate base image
3. Create variation images with specified poses
4. Generate TTS with intro/outro
5. Build composition using items array

---

## Auto Mode Usage

When `--auto` flag is used, AI generates new topics instead of using pre-built templates.

### Auto Mode Process
1. Parse `--type` argument (default: vocabulary)
2. Select random theme within category
3. AI generates topic structure with 10 items
4. Validate generated content
5. Proceed with normal video generation workflow

### Example Auto Mode Commands

```bash
# Generate random vocabulary video
create korean video --auto

# Generate polite expressions video
create korean video --type polite --auto

# Generate situational phrases video
create korean video --type situation --auto
```

### AI Generation Output Format

The AI must generate content matching this exact structure:

```typescript
interface GeneratedTopic {
  topic: string;           // English topic name
  topicKorean: string;     // Korean topic name
  intro: string;           // Korean intro phrase
  outro: string;           // Korean outro phrase
  items: {
    korean: string;        // Korean word/phrase
    english: string;       // English translation
    romanization: string;  // Romanized pronunciation
    pose: string;          // Character pose description
  }[];                     // Exactly 10 items
}
```

### Validation Rules
- `items` array must have exactly 10 entries
- All string fields must be non-empty
- `romanization` must be lowercase ASCII
- `pose` should be descriptive for image generation
