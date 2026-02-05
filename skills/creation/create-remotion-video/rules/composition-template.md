---
name: composition-template
description: Reels composition template for video generation
metadata:
  tags: remotion, composition, reels, template, 1080x1920
---

# Reels Composition Template

Use this template for creating vertical video compositions in reels format (9:16 aspect ratio).

## Composition Registration

Register your composition in `src/Root.tsx`:

```tsx
import { Composition } from "remotion";
import { MyReelsVideo } from "./MyReelsVideo";

export const RemotionRoot: React.FC = () => {
  return (
    <>
      <Composition
        id="MyReelsVideo"
        component={MyReelsVideo}
        durationInFrames={300} // Adjust based on audio duration
        fps={30}
        width={1080}
        height={1920}
      />
    </>
  );
};
```

## Dynamic Duration with calculateMetadata

For videos with variable audio length, use `calculateMetadata`:

```tsx
import { Composition, CalculateMetadataFunction } from "remotion";
import { getAudioDurationInSeconds } from "@remotion/media";
import { MyReelsVideo, MyReelsVideoProps } from "./MyReelsVideo";

const calculateMetadata: CalculateMetadataFunction<MyReelsVideoProps> = async ({
  props,
}) => {
  const audioDuration = await getAudioDurationInSeconds(props.audioSrc);
  const fps = 30;
  const durationInFrames = Math.ceil(audioDuration * fps) + 30; // Add 1 second buffer

  return {
    durationInFrames,
    props,
  };
};

export const RemotionRoot: React.FC = () => {
  return (
    <Composition
      id="MyReelsVideo"
      component={MyReelsVideo}
      durationInFrames={300} // Placeholder, will be overridden
      fps={30}
      width={1080}
      height={1920}
      defaultProps={{
        audioSrc: "audio/narration.mp3",
      }}
      calculateMetadata={calculateMetadata}
    />
  );
};
```

## Basic Reels Component Structure

```tsx
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

export type MyReelsVideoProps = {
  audioSrc: string;
  title?: string;
};

export const MyReelsVideo: React.FC<MyReelsVideoProps> = ({
  audioSrc,
  title = "My Video",
}) => {
  const frame = useCurrentFrame();
  const { fps, durationInFrames } = useVideoConfig();

  // Logo animation
  const logoScale = spring({
    frame,
    fps,
    config: { damping: 12, stiffness: 100 },
  });

  // Fade out at the end
  const fadeOut = interpolate(
    frame,
    [durationInFrames - 30, durationInFrames],
    [1, 0],
    { extrapolateLeft: "clamp" }
  );

  return (
    <AbsoluteFill className="bg-gradient-to-b from-slate-900 to-slate-800">
      {/* Audio narration */}
      <Audio src={staticFile(audioSrc)} />

      {/* Logo at top */}
      <div
        style={{
          position: "absolute",
          top: 80,
          left: "50%",
          transform: `translateX(-50%) scale(${logoScale})`,
          opacity: fadeOut,
        }}
      >
        <Img src={staticFile("logo.png")} width={200} />
      </div>

      {/* Main content area */}
      <div
        style={{
          position: "absolute",
          top: "50%",
          left: "50%",
          transform: "translate(-50%, -50%)",
          textAlign: "center",
          opacity: fadeOut,
        }}
      >
        <h1
          style={{
            fontSize: 72,
            fontWeight: "bold",
            color: "white",
          }}
        >
          {title}
        </h1>
      </div>
    </AbsoluteFill>
  );
};
```

## Using the Logo

Copy the logo to the public folder first:

```bash
cp Resources/logo.png public/logo.png
```

Then reference it in your composition:

```tsx
import { Img, staticFile } from "remotion";

<Img src={staticFile("logo.png")} width={200} />
```

## Scene Transitions

Use `@remotion/transitions` for smooth scene transitions:

```tsx
import { TransitionSeries, springTiming } from "@remotion/transitions";
import { fade } from "@remotion/transitions/fade";
import { slide } from "@remotion/transitions/slide";

export const MyReelsVideo: React.FC = () => {
  return (
    <TransitionSeries>
      <TransitionSeries.Sequence durationInFrames={90}>
        <IntroScene />
      </TransitionSeries.Sequence>

      <TransitionSeries.Transition
        presentation={fade()}
        timing={springTiming({ config: { damping: 200 }, durationInFrames: 15 })}
      />

      <TransitionSeries.Sequence durationInFrames={120}>
        <MainScene />
      </TransitionSeries.Sequence>

      <TransitionSeries.Transition
        presentation={slide({ direction: "from-bottom" })}
        timing={springTiming({ config: { damping: 200 }, durationInFrames: 15 })}
      />

      <TransitionSeries.Sequence durationInFrames={90}>
        <OutroScene />
      </TransitionSeries.Sequence>
    </TransitionSeries>
  );
};
```

## Tailwind CSS Support

This project includes TailwindCSS v4 support. Use classes directly:

```tsx
<AbsoluteFill className="bg-gradient-to-b from-indigo-900 via-purple-900 to-slate-900">
  <div className="flex flex-col items-center justify-center h-full">
    <h1 className="text-6xl font-bold text-white">Hello World</h1>
  </div>
</AbsoluteFill>
```

## Rendering

Preview your composition:
```bash
npm run dev
```

Render to MP4:
```bash
npx remotion render MyReelsVideo out/my-video.mp4
```
