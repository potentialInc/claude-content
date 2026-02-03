---
name: video-composition
description: Remotion composition template for Korean learning videos
metadata:
  tags: remotion, composition, video, template, react
---

# Video Composition Template

Remotion composition template for creating Korean learning videos.

## Video Specifications

- **Format**: Reels (Vertical)
- **Width**: 1080px
- **Height**: 1920px
- **FPS**: 30
- **Aspect Ratio**: 9:16

## Scene Structure

```
1. Intro Scene (3 sec) → 2. Item Scenes (4 sec each) → 3. Summary Scene (5 sec)
```

## Complete Composition Template

```tsx
// src/{TopicName}Video.tsx
import {
  AbsoluteFill,
  Audio,
  Img,
  interpolate,
  Sequence,
  spring,
  staticFile,
  useCurrentFrame,
  useVideoConfig,
} from "remotion";
import { TransitionSeries, springTiming } from "@remotion/transitions";
import { fade } from "@remotion/transitions/fade";
import { slide } from "@remotion/transitions/slide";
import { loadFont as loadNotoSansKR } from "@remotion/google-fonts/NotoSansKR";
import { loadFont as loadInter } from "@remotion/google-fonts/Inter";
import { FloatingParticles, AnimatedCheckmark } from "./animations";

// Load fonts
const { fontFamily: koreanFont } = loadNotoSansKR();
const { fontFamily: englishFont } = loadInter();

// Logo path
const LOGO_PATH = staticFile("K Talk Live(Square).png");

// Paths
const getImagePath = (id: string) => staticFile(`images/{topic}/${id}.png`);
const getAudioPath = (id: string) => staticFile(`audio/{topic}/${id}.mp3`);
const INTRO_AUDIO = staticFile("audio/{topic}/intro.mp3");
const OUTRO_AUDIO = staticFile("audio/{topic}/outro.mp3");

// Duration constants
const INTRO_DURATION = 90;
const ITEM_DURATION = 120;
const SUMMARY_DURATION = 150;
const TRANSITION_DURATION = 15;

// Item data
const ITEMS = [
  {
    id: "01_item",
    korean: "한국어",
    english: "Korean",
    romanization: "hangugeo",
    image: "01_item",
    audio: "01_item",
  },
  // ... more items
];

// Color palette
const COLORS = [
  { bg: "from-rose-600 to-pink-800", accent: "#fda4af" },
  { bg: "from-amber-600 to-orange-800", accent: "#fcd34d" },
  { bg: "from-emerald-600 to-teal-800", accent: "#6ee7b7" },
  { bg: "from-sky-600 to-blue-800", accent: "#7dd3fc" },
  { bg: "from-violet-600 to-purple-800", accent: "#c4b5fd" },
];

// Intro Scene
const IntroScene: React.FC = () => {
  const frame = useCurrentFrame();
  const { fps, durationInFrames } = useVideoConfig();

  const logoScale = spring({
    frame,
    fps,
    config: { damping: 10, stiffness: 80 },
  });

  const titleScale = spring({
    frame: frame - 0.2 * fps,
    fps,
    config: { damping: 12, stiffness: 100 },
  });

  const exitOpacity = interpolate(
    frame,
    [durationInFrames - 15, durationInFrames],
    [1, 0],
    { extrapolateLeft: "clamp" }
  );

  return (
    <AbsoluteFill className="bg-gradient-to-b from-slate-900 via-indigo-950 to-slate-900 flex flex-col items-center justify-center">
      <Sequence from={Math.floor(fps * 0.5)}>
        <Audio src={INTRO_AUDIO} volume={0.8} />
      </Sequence>

      <FloatingParticles count={20} color="rgba(255,255,255,0.1)" />

      <div style={{ opacity: exitOpacity }} className="text-center px-8">
        <div style={{ transform: `scale(${logoScale})` }} className="mb-12">
          <Img src={LOGO_PATH} width={280} className="mx-auto" />
        </div>

        <div
          style={{
            fontFamily: englishFont,
            transform: `scale(${Math.max(0, titleScale)})`,
          }}
          className="text-5xl font-black text-white mb-4"
        >
          {/* Topic Title */}
        </div>

        <div
          style={{ fontFamily: koreanFont }}
          className="text-4xl font-bold text-violet-200"
        >
          {/* Topic Korean */}
        </div>
      </div>
    </AbsoluteFill>
  );
};

// Item Scene
const ItemScene: React.FC<{
  item: (typeof ITEMS)[0];
  colors: (typeof COLORS)[0];
}> = ({ item, colors }) => {
  const frame = useCurrentFrame();
  const { fps, durationInFrames } = useVideoConfig();

  const imageScale = spring({
    frame,
    fps,
    config: { damping: 12, stiffness: 100 },
  });

  const textProgress = spring({
    frame: frame - 0.3 * fps,
    fps,
    config: { damping: 15, stiffness: 80 },
  });

  const exitOpacity = interpolate(
    frame,
    [durationInFrames - 15, durationInFrames],
    [1, 0],
    { extrapolateLeft: "clamp" }
  );

  return (
    <AbsoluteFill className={`bg-gradient-to-b ${colors.bg}`}>
      <Sequence from={Math.floor(fps * 0.3)}>
        <Audio src={getAudioPath(item.audio)} volume={0.8} />
      </Sequence>

      <div
        style={{ opacity: exitOpacity }}
        className="flex flex-col items-center justify-center h-full px-8"
      >
        {/* Character Image */}
        <div style={{ transform: `scale(${imageScale})` }} className="mb-8">
          <Img
            src={getImagePath(item.image)}
            style={{ width: 500, height: 500, objectFit: "contain" }}
          />
        </div>

        {/* Korean Text */}
        <div
          style={{
            fontFamily: koreanFont,
            opacity: Math.max(0, textProgress),
            transform: `translateY(${interpolate(Math.max(0, textProgress), [0, 1], [30, 0])}px)`,
          }}
          className="text-7xl font-bold text-white text-center mb-4"
        >
          {item.korean}
        </div>

        {/* Romanization */}
        <div
          style={{
            fontFamily: englishFont,
            opacity: Math.max(0, textProgress),
          }}
          className="text-2xl text-white/70 italic mb-4"
        >
          {item.romanization}
        </div>

        {/* English */}
        <div
          style={{
            fontFamily: englishFont,
            color: colors.accent,
            opacity: Math.max(0, textProgress),
          }}
          className="text-3xl font-semibold"
        >
          {item.english}
        </div>
      </div>
    </AbsoluteFill>
  );
};

// Summary Scene
const SummaryScene: React.FC = () => {
  const frame = useCurrentFrame();
  const { fps } = useVideoConfig();

  const logoProgress = spring({
    frame,
    fps,
    config: { damping: 12, stiffness: 100 },
  });

  return (
    <AbsoluteFill className="bg-gradient-to-b from-slate-900 via-indigo-950 to-slate-900 flex flex-col items-center pt-12">
      <Sequence from={Math.floor(fps * 0.5)}>
        <Audio src={OUTRO_AUDIO} volume={0.8} />
      </Sequence>

      <FloatingParticles count={25} color="rgba(255,255,255,0.15)" />

      <div style={{ transform: `scale(${logoProgress})` }} className="mb-6">
        <Img src={LOGO_PATH} width={180} className="mx-auto" />
      </div>

      <div
        style={{ fontFamily: englishFont }}
        className="text-2xl font-bold text-white mb-6 text-center px-6"
      >
        Great job! You learned {ITEMS.length} new words!
      </div>

      {/* Summary grid */}
      <div className="flex flex-col gap-3 px-4 w-full">
        {ITEMS.map((item, i) => {
          const cardProgress = spring({
            frame: frame - 0.3 * fps - i * 3,
            fps,
            config: { damping: 10, stiffness: 100 },
          });

          return (
            <div
              key={item.id}
              style={{
                opacity: Math.max(0, cardProgress),
                transform: `translateY(${interpolate(Math.max(0, cardProgress), [0, 1], [20, 0])}px)`,
              }}
              className={`bg-gradient-to-r ${COLORS[i % COLORS.length].bg} rounded-xl p-3 flex items-center justify-between`}
            >
              <div style={{ fontFamily: koreanFont }} className="text-xl font-bold text-white">
                {item.korean}
              </div>
              <div style={{ fontFamily: englishFont }} className="text-lg text-white/80">
                {item.english}
              </div>
            </div>
          );
        })}
      </div>

      <div
        style={{
          marginTop: 20,
          opacity: spring({ frame: frame - fps * 2, fps, config: { damping: 200 } }),
        }}
      >
        <AnimatedCheckmark delay={fps * 2} size={50} color="#22c55e" />
      </div>
    </AbsoluteFill>
  );
};

// Main Composition
export const {TopicName}Video: React.FC = () => {
  return (
    <TransitionSeries>
      <TransitionSeries.Sequence durationInFrames={INTRO_DURATION}>
        <IntroScene />
      </TransitionSeries.Sequence>

      <TransitionSeries.Transition
        presentation={fade()}
        timing={springTiming({ config: { damping: 200 }, durationInFrames: TRANSITION_DURATION })}
      />

      {ITEMS.flatMap((item, i) => {
        const elements = [
          <TransitionSeries.Sequence key={`item-${i}`} durationInFrames={ITEM_DURATION}>
            <ItemScene item={item} colors={COLORS[i % COLORS.length]} />
          </TransitionSeries.Sequence>,
        ];

        elements.push(
          <TransitionSeries.Transition
            key={`trans-${i}`}
            presentation={i < ITEMS.length - 1 ? slide({ direction: "from-right" }) : fade()}
            timing={springTiming({ config: { damping: 200 }, durationInFrames: TRANSITION_DURATION })}
          />
        );

        return elements;
      })}

      <TransitionSeries.Sequence durationInFrames={SUMMARY_DURATION}>
        <SummaryScene />
      </TransitionSeries.Sequence>
    </TransitionSeries>
  );
};
```

## Register in Root.tsx

```tsx
import { {TopicName}Video } from "./{TopicName}";

// Inside RemotionRoot:
<Composition
  id="{TopicName}"
  component={{TopicName}Video}
  durationInFrames={900} // Calculate based on items
  fps={30}
  width={1080}
  height={1920}
/>
```

## Duration Calculation

```typescript
const calculateDuration = (itemCount: number) => {
  const INTRO = 90;
  const ITEM = 120;
  const SUMMARY = 150;
  const TRANSITION = 15;

  return INTRO + (itemCount * ITEM) + SUMMARY + ((itemCount + 1) * TRANSITION);
};

// Example: 5 items
// 90 + (5 * 120) + 150 + (6 * 15) = 90 + 600 + 150 + 90 = 930 frames = 31 seconds
```

## Transition Options

```typescript
import { fade } from "@remotion/transitions/fade";
import { slide } from "@remotion/transitions/slide";
import { wipe } from "@remotion/transitions/wipe";

// Fade between scenes
presentation={fade()}

// Slide from direction
presentation={slide({ direction: "from-right" })}
presentation={slide({ direction: "from-bottom" })}

// Wipe transition
presentation={wipe({ direction: "from-left" })}
```

---

## Best Practices & Lessons Learned

### 1. Item Scene Background

**Problem**: AI-generated images may have backgrounds that are slightly off-white, creating visible edges against pure white backgrounds.

**Recommended Solution**: Remove image backgrounds using `rembg` (see workflow.md Step 3.5), then use pure white:

```tsx
// BEST - after removing backgrounds with rembg
<AbsoluteFill style={{ background: "#ffffff" }}>
```

**Alternative Solution**: If not removing backgrounds, use a light gradient to mask differences:

```tsx
// OK - gradient masks slight color differences
<AbsoluteFill style={{ background: "linear-gradient(to bottom, #fafafa, #f5f5f5)" }}>
```

### 2. Summary Scene Text Sizing

**Problem**: Default Tailwind text classes (`text-sm`, `text-lg`) are too small for 1080x1920 video format.

**Solution**: Use explicit `fontSize` values in inline styles:

```tsx
// Summary card styling - RECOMMENDED sizes
<div className="flex flex-col gap-3 px-6 w-full">
  {items.map((item, i) => (
    <div className="bg-white/15 backdrop-blur rounded-xl p-4 flex items-center gap-4">
      {/* Number - 24px */}
      <div style={{ fontFamily: englishFont, fontSize: 24, minWidth: 50 }}>
        #{i + 1}
      </div>
      {/* Korean - 32px */}
      <div style={{ fontFamily: koreanFont, fontSize: 32 }}>
        {item.korean}
      </div>
      {/* English - 22px */}
      <div style={{ fontFamily: englishFont, fontSize: 22 }}>
        {item.english}
      </div>
    </div>
  ))}
</div>
```

### 3. Summary Scene Header Sizing

Keep logo and emoji smaller to give more room for the phrase list:

```tsx
// Logo - smaller for summary
<Img src={LOGO_PATH} width={140} />

// Emoji - 50px instead of 60px
<div style={{ fontSize: 50 }}>🍽️</div>

// Title - explicit 36px
<div style={{ fontSize: 36 }}>Now you can order!</div>

// Checkmark - larger for visibility
<AnimatedCheckmark size={70} color="#22c55e" />
```

### 4. Spacing in Summary Scene

Use tighter spacing at the top to fit all cards:

```tsx
// Tighter margins
className="mb-2"  // instead of mb-4
className="gap-3" // instead of gap-2
className="px-6"  // instead of px-4
```

### 5. Instagram UI Safe Zone

**Problem**: Instagram overlays profile information, like/comment buttons, and caption text at the bottom of Reels. Text placed too low will be covered.

**Solution**: Use large bottom padding (`pb-64`) and minimize gaps between elements to push content higher:

```tsx
// Item/Phrase Scene container - RECOMMENDED
<div
  style={{ opacity: exitOpacity }}
  className="flex flex-col items-center h-full pt-16 pb-64"
>
  {/* Logo */}
  <div className="mb-6">...</div>

  {/* Number badge - minimal margin */}
  <div className="mb-2">...</div>

  {/* English title - minimal margin */}
  <div className="mb-2">...</div>

  {/* Character Image - no vertical margin */}
  <div
    style={{ minHeight: 550 }}
    className="my-0"
  >
    <Img style={{ width: 800, height: 800 }} />
  </div>

  {/* Korean text and romanization stay above Instagram UI */}
</div>
```

**Key values**:
- Container: `pb-64` (large bottom padding)
- Badge margin: `mb-2` (minimal)
- Title margin: `mb-2` (minimal)
- Image margin: `my-0` (none)
- Image minHeight: `550` (reduced from 650)
