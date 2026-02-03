---
name: workflow
description: End-to-end workflow for generating videos with AI voice
metadata:
  tags: workflow, video, generation, process, steps
---

# Video Generation Workflow

Complete workflow for generating a Remotion video with ElevenLabs voice narration.

## Overview

```
Script → Voice Generation → Composition → Preview → Render
```

## Step 1: Prepare Your Script

Create a script file or define your content:

```typescript
// scripts/video-script.ts
export const videoScript = {
  title: "My Awesome Video",
  segments: [
    {
      id: "intro",
      text: "Welcome to this amazing video!",
      duration: 90, // frames (3 seconds at 30fps)
    },
    {
      id: "main-point-1",
      text: "Here is the first important point.",
      duration: 120,
    },
    {
      id: "main-point-2",
      text: "And here is another key insight.",
      duration: 120,
    },
    {
      id: "outro",
      text: "Thanks for watching! Don't forget to subscribe.",
      duration: 90,
    },
  ],
};
```

## Step 2: Generate Voice Audio

Create a TTS generation script:

```typescript
// scripts/generate-video-audio.ts
import { ElevenLabsClient } from "elevenlabs";
import * as fs from "fs";
import * as path from "path";
import { videoScript } from "./video-script";

const VOICE_ID = "mYk0rAapHek2oTw18z8x";

async function generateAudio() {
  const apiKey = process.env.ELEVENLABS_API_KEY;
  if (!apiKey) {
    console.error("ELEVENLABS_API_KEY not set");
    process.exit(1);
  }

  const client = new ElevenLabsClient({ apiKey });
  const outputDir = path.join(process.cwd(), "public/audio/video");

  if (!fs.existsSync(outputDir)) {
    fs.mkdirSync(outputDir, { recursive: true });
  }

  for (const segment of videoScript.segments) {
    const filepath = path.join(outputDir, `${segment.id}.mp3`);

    if (fs.existsSync(filepath)) {
      console.log(`✓ ${segment.id} exists`);
      continue;
    }

    console.log(`Generating: ${segment.id}...`);
    const audio = await client.textToSpeech.convert(VOICE_ID, {
      text: segment.text,
      model_id: "eleven_multilingual_v2",
      output_format: "mp3_44100_128",
    });

    const chunks: Buffer[] = [];
    for await (const chunk of audio) {
      chunks.push(Buffer.from(chunk));
    }
    fs.writeFileSync(filepath, Buffer.concat(chunks));
    console.log(`✓ ${segment.id} saved`);

    await new Promise((r) => setTimeout(r, 500));
  }

  console.log("✅ Audio generation complete!");
}

generateAudio().catch(console.error);
```

Run:
```bash
npx ts-node scripts/generate-video-audio.ts
```

## Step 3: Copy Assets

Ensure assets are in the public folder:

```bash
# Copy logo
cp Resources/logo.png public/logo.png
```

## Step 4: Create the Composition

Create your video composition:

```tsx
// src/MyGeneratedVideo.tsx
import {
  AbsoluteFill,
  Audio,
  Img,
  Sequence,
  staticFile,
  useCurrentFrame,
  useVideoConfig,
  spring,
  interpolate,
} from "remotion";
import { TransitionSeries, springTiming } from "@remotion/transitions";
import { fade } from "@remotion/transitions/fade";

const LOGO_PATH = staticFile("logo.png");

// Scene component for each segment
const SceneSegment: React.FC<{
  text: string;
  audioSrc: string;
}> = ({ text, audioSrc }) => {
  const frame = useCurrentFrame();
  const { fps, durationInFrames } = useVideoConfig();

  const textOpacity = spring({
    frame,
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
    <AbsoluteFill className="bg-gradient-to-b from-indigo-900 to-slate-900 flex items-center justify-center">
      <Audio src={staticFile(audioSrc)} />

      <div
        style={{
          opacity: textOpacity * exitOpacity,
          transform: `translateY(${interpolate(textOpacity, [0, 1], [30, 0])}px)`,
        }}
        className="px-12 text-center"
      >
        <p className="text-5xl font-semibold text-white leading-tight">
          {text}
        </p>
      </div>
    </AbsoluteFill>
  );
};

// Intro scene with logo
const IntroScene: React.FC<{ title: string; audioSrc: string }> = ({
  title,
  audioSrc,
}) => {
  const frame = useCurrentFrame();
  const { fps } = useVideoConfig();

  const logoScale = spring({
    frame,
    fps,
    config: { damping: 12, stiffness: 100 },
  });

  const titleOpacity = spring({
    frame: frame - 15,
    fps,
    config: { damping: 200 },
  });

  return (
    <AbsoluteFill className="bg-gradient-to-b from-slate-900 via-indigo-950 to-slate-900 flex flex-col items-center justify-center">
      <Audio src={staticFile(audioSrc)} />

      <div style={{ transform: `scale(${logoScale})` }} className="mb-12">
        <Img src={LOGO_PATH} width={250} />
      </div>

      <h1
        style={{ opacity: Math.max(0, titleOpacity) }}
        className="text-6xl font-bold text-white text-center px-8"
      >
        {title}
      </h1>
    </AbsoluteFill>
  );
};

// Outro scene
const OutroScene: React.FC<{ audioSrc: string }> = ({ audioSrc }) => {
  const frame = useCurrentFrame();
  const { fps } = useVideoConfig();

  const scale = spring({
    frame,
    fps,
    config: { damping: 10, stiffness: 80 },
  });

  return (
    <AbsoluteFill className="bg-gradient-to-b from-slate-900 via-indigo-950 to-slate-900 flex flex-col items-center justify-center">
      <Audio src={staticFile(audioSrc)} />

      <div style={{ transform: `scale(${scale})` }}>
        <Img src={LOGO_PATH} width={300} />
      </div>

      <p
        style={{ opacity: spring({ frame: frame - 20, fps, config: { damping: 200 } }) }}
        className="text-4xl text-white mt-8"
      >
        Thanks for watching!
      </p>
    </AbsoluteFill>
  );
};

// Main composition
export const MyGeneratedVideo: React.FC = () => {
  return (
    <TransitionSeries>
      <TransitionSeries.Sequence durationInFrames={90}>
        <IntroScene title="My Awesome Video" audioSrc="audio/video/intro.mp3" />
      </TransitionSeries.Sequence>

      <TransitionSeries.Transition
        presentation={fade()}
        timing={springTiming({ config: { damping: 200 }, durationInFrames: 15 })}
      />

      <TransitionSeries.Sequence durationInFrames={120}>
        <SceneSegment
          text="Here is the first important point."
          audioSrc="audio/video/main-point-1.mp3"
        />
      </TransitionSeries.Sequence>

      <TransitionSeries.Transition
        presentation={fade()}
        timing={springTiming({ config: { damping: 200 }, durationInFrames: 15 })}
      />

      <TransitionSeries.Sequence durationInFrames={120}>
        <SceneSegment
          text="And here is another key insight."
          audioSrc="audio/video/main-point-2.mp3"
        />
      </TransitionSeries.Sequence>

      <TransitionSeries.Transition
        presentation={fade()}
        timing={springTiming({ config: { damping: 200 }, durationInFrames: 15 })}
      />

      <TransitionSeries.Sequence durationInFrames={90}>
        <OutroScene audioSrc="audio/video/outro.mp3" />
      </TransitionSeries.Sequence>
    </TransitionSeries>
  );
};
```

## Step 5: Register the Composition

Add to `src/Root.tsx`:

```tsx
import { Composition } from "remotion";
import { MyGeneratedVideo } from "./MyGeneratedVideo";

export const RemotionRoot: React.FC = () => {
  return (
    <>
      {/* ... other compositions ... */}
      <Composition
        id="MyGeneratedVideo"
        component={MyGeneratedVideo}
        durationInFrames={450} // Total: 90+120+120+90 + transitions
        fps={30}
        width={1080}
        height={1920}
      />
    </>
  );
};
```

## Step 6: Preview

Start the Remotion studio:

```bash
npm run dev
```

Navigate to your composition and preview the video.

## Step 7: Render

Render the final video:

```bash
# MP4 output
npx remotion render MyGeneratedVideo out/my-video.mp4

# With specific codec
npx remotion render MyGeneratedVideo out/my-video.mp4 --codec h264

# High quality
npx remotion render MyGeneratedVideo out/my-video.mp4 --crf 18
```

## Complete Workflow Script

Create a one-command workflow:

```json
// package.json
{
  "scripts": {
    "generate-video": "ts-node scripts/generate-video-audio.ts && npm run dev"
  }
}
```

Run:
```bash
npm run generate-video
```

## Tips

1. **Audio Duration**: Use `getAudioDurationInSeconds` from `@remotion/media` to calculate exact durations
2. **Caching**: The TTS script skips existing files - delete them to regenerate
3. **Rate Limits**: ElevenLabs has rate limits - add delays between requests
4. **Preview**: Always preview before final render to catch issues
5. **Assets**: Keep assets organized in `public/` subdirectories
