---
name: platforms
description: Platform-specific requirements and smart platform suggestions
metadata:
  tags: platforms, aspect-ratio, requirements, validation
---

# Platform Requirements

This document describes requirements and best practices for uploading videos to different social media platforms.

## Supported Platforms

The Upload Posts API supports uploading to the following platforms:

- **facebook** - Facebook posts and Reels
- **instagram** - Instagram Feed, Reels, Stories
- **threads** - Threads video posts
- **tiktok** - TikTok short-form videos
- **youtube** - YouTube videos and Shorts
- **linkedin** - LinkedIn professional content
- **x** - X (Twitter) video posts
- **pinterest** - Pinterest video pins
- **bluesky** - Bluesky video posts

## Default Platforms

For this project, the default platforms are:
- **facebook**
- **instagram**
- **threads**

These can be customized in `.upload-config.json`.

## Aspect Ratio Guidelines

Different platforms have different optimal aspect ratios. Choose platforms based on your video dimensions:

### 9:16 (Vertical) - 1080x1920

**Best for**: Mobile-first short-form content

**Recommended Platforms**:
- TikTok (primary platform for vertical videos)
- Instagram Reels
- Instagram Stories
- YouTube Shorts
- Facebook Reels
- Threads

**Remotion Composition**:
```typescript
{
  id: "VerticalVideo",
  width: 1080,
  height: 1920,
  fps: 30,
  durationInFrames: 900  // 30 seconds at 30fps
}
```

### 16:9 (Horizontal) - 1920x1080

**Best for**: Desktop-first long-form content

**Recommended Platforms**:
- YouTube (primary platform for horizontal videos)
- Facebook
- LinkedIn
- X (Twitter)

**Remotion Composition**:
```typescript
{
  id: "HorizontalVideo",
  width: 1920,
  height: 1080,
  fps: 30,
  durationInFrames: 1800  // 60 seconds at 30fps
}
```

### 1:1 (Square) - 1080x1080

**Best for**: Feed posts across platforms

**Recommended Platforms**:
- Instagram Feed
- Facebook Feed
- LinkedIn
- X (Twitter)

**Remotion Composition**:
```typescript
{
  id: "SquareVideo",
  width: 1080,
  height: 1080,
  fps: 30,
  durationInFrames: 600  // 20 seconds at 30fps
}
```

## Platform-Specific Requirements

### Facebook

**Supported Formats**: MP4, MOV
**Aspect Ratios**: 9:16, 16:9, 1:1, 4:5
**Duration**: 1 second to 240 minutes
**File Size**: Max 10GB
**Resolution**: Min 600x315 (landscape), 600x600 (square)

**Best Practices**:
- Keep videos under 3 minutes for better engagement
- Use 16:9 for desktop, 1:1 for mobile feed
- Include captions (80% watch without sound)

**Optimal**:
- Resolution: 1920x1080 (16:9) or 1080x1080 (1:1)
- Duration: 1-3 minutes
- File size: Under 500MB

### Instagram

**Reels**:
- Aspect Ratio: 9:16 (vertical)
- Duration: 15-90 seconds
- Resolution: 1080x1920
- File Size: Max 4GB
- Format: MP4

**Feed**:
- Aspect Ratio: 1:1 (square) or 4:5 (portrait)
- Duration: 3-60 seconds
- Resolution: 1080x1080 (square)
- File Size: Max 4GB

**Stories**:
- Aspect Ratio: 9:16 (vertical)
- Duration: Up to 15 seconds per story
- Resolution: 1080x1920
- File Size: Max 4GB

**Best Practices**:
- Use vertical (9:16) for Reels and Stories
- Keep videos under 60 seconds for feed
- Add captions for accessibility
- Use trending audio for Reels

### Threads

**Supported Formats**: MP4
**Aspect Ratios**: 9:16, 16:9, 1:1
**Duration**: Up to 5 minutes
**File Size**: Max 1GB
**Resolution**: Recommended 1080x1920 (vertical)

**Best Practices**:
- Keep videos short (under 60 seconds)
- Use vertical format for mobile optimization
- Add engaging captions in the post text

### TikTok

**Supported Formats**: MP4, MOV
**Aspect Ratio**: 9:16 (vertical) - strict requirement
**Duration**: 15 seconds to 10 minutes
**File Size**: Max 287MB (Android), 287MB (iOS)
**Resolution**: 1080x1920 recommended

**Best Practices**:
- **Must be vertical** - TikTok does not work well with horizontal videos
- Optimal duration: 15-60 seconds for maximum engagement
- Hook viewers in first 3 seconds
- Use trending sounds/music
- Add text overlays and captions

**Optimal**:
- Resolution: 1080x1920
- Duration: 15-60 seconds
- FPS: 30fps
- File size: Under 50MB

### YouTube

**Regular Videos**:
- Aspect Ratio: 16:9 (horizontal)
- Duration: Unlimited
- Resolution: Up to 8K (7680x4320)
- File Size: Max 256GB

**Shorts**:
- Aspect Ratio: 9:16 (vertical)
- Duration: Up to 60 seconds
- Resolution: 1080x1920
- File Size: Max 256GB

**Best Practices**:
- Use 16:9 for regular videos
- Use 9:16 for Shorts (vertical)
- Optimal regular video: 5-15 minutes
- Optimal Shorts: 15-59 seconds
- Add chapters for longer videos

### LinkedIn

**Supported Formats**: MP4, MOV, AVI
**Aspect Ratios**: 1:1, 16:9, 9:16, 2:3, 4:5
**Duration**: 3 seconds to 10 minutes
**File Size**: Max 5GB
**Resolution**: Min 256x144, Max 4096x2304

**Best Practices**:
- Keep videos 30 seconds to 3 minutes
- Use 1:1 (square) for feed
- Professional, educational content performs best
- Add captions (many watch muted)

**Optimal**:
- Resolution: 1920x1080 (16:9) or 1080x1080 (1:1)
- Duration: 1-3 minutes
- Professional tone and appearance

### X (Twitter)

**Supported Formats**: MP4, MOV
**Aspect Ratios**: 1:1, 16:9, 9:16
**Duration**: 0.5 to 140 seconds (2 minutes 20 seconds)
**File Size**: Max 512MB
**Resolution**: Min 32x32, Max 1920x1200 (or 1200x1900)

**Best Practices**:
- Keep videos under 60 seconds
- Use 16:9 or 1:1 for best display
- First frame matters (acts as thumbnail)
- Add captions for accessibility

### Pinterest

**Supported Formats**: MP4, MOV, M4V
**Aspect Ratios**: 2:3, 9:16, 1:1 (vertical preferred)
**Duration**: 4 seconds to 15 minutes
**File Size**: Max 2GB
**Resolution**: Recommended 1000x1500 or 1080x1920

**Best Practices**:
- Use vertical formats (2:3 or 9:16)
- Include text overlays with instructions/tips
- First 3 seconds should grab attention
- Optimal duration: 6-15 seconds

### Bluesky

**Supported Formats**: MP4
**Aspect Ratios**: Various (9:16, 16:9, 1:1)
**Duration**: Up to 60 seconds
**File Size**: Max 50MB
**Resolution**: Recommended 1080x1920 or 1920x1080

**Best Practices**:
- Keep videos under 30 seconds
- Use appropriate aspect ratio for content type
- Ensure file size is under 50MB

## Smart Platform Detection

The upload script automatically suggests platforms based on video aspect ratio:

### Detection Logic

```typescript
function suggestPlatforms(width: number, height: number, duration: number): string[] {
  const aspectRatio = width / height;
  const durationSeconds = duration / 30; // Assuming 30fps

  // Vertical video (9:16)
  if (Math.abs(aspectRatio - 9/16) < 0.1) {
    if (durationSeconds <= 60) {
      return ['tiktok', 'instagram', 'youtube', 'threads'];
    }
    return ['instagram', 'youtube', 'threads'];
  }

  // Horizontal video (16:9)
  if (Math.abs(aspectRatio - 16/9) < 0.1) {
    return ['youtube', 'facebook', 'linkedin', 'x'];
  }

  // Square video (1:1)
  if (Math.abs(aspectRatio - 1) < 0.1) {
    return ['instagram', 'facebook', 'linkedin', 'x'];
  }

  // Default fallback
  return ['youtube', 'facebook'];
}
```

### Example Suggestions

**1080x1920 (9:16) - 30 seconds**:
```
Suggested platforms: tiktok, instagram, youtube, threads
Reason: Vertical short-form video ideal for mobile platforms
```

**1920x1080 (16:9) - 5 minutes**:
```
Suggested platforms: youtube, facebook, linkedin, x
Reason: Horizontal long-form video ideal for desktop platforms
```

**1080x1080 (1:1) - 20 seconds**:
```
Suggested platforms: instagram, facebook, linkedin, x
Reason: Square video works well in social feeds
```

## Platform Validation

Before uploading, validate video meets platform requirements:

```typescript
interface PlatformRequirements {
  aspectRatios: number[];
  minDuration: number;  // seconds
  maxDuration: number;  // seconds
  maxFileSize: number;  // MB
}

const requirements: Record<string, PlatformRequirements> = {
  tiktok: {
    aspectRatios: [9/16],
    minDuration: 1,
    maxDuration: 600,
    maxFileSize: 287
  },
  instagram: {
    aspectRatios: [9/16, 1/1, 4/5],
    minDuration: 3,
    maxDuration: 90,
    maxFileSize: 4096
  },
  youtube: {
    aspectRatios: [16/9, 9/16],
    minDuration: 1,
    maxDuration: 999999,
    maxFileSize: 262144  // 256GB
  },
  // ... more platforms
};

function validateForPlatform(
  platform: string,
  width: number,
  height: number,
  durationSeconds: number,
  fileSizeMB: number
): { valid: boolean; warnings: string[] } {
  const req = requirements[platform];
  if (!req) return { valid: true, warnings: [] };

  const warnings: string[] = [];
  const aspectRatio = width / height;

  // Check aspect ratio
  const matchesAspectRatio = req.aspectRatios.some(
    ar => Math.abs(aspectRatio - ar) < 0.1
  );

  if (!matchesAspectRatio) {
    warnings.push(
      `Aspect ratio ${aspectRatio.toFixed(2)} may not be optimal for ${platform}`
    );
  }

  // Check duration
  if (durationSeconds < req.minDuration) {
    warnings.push(`Video is too short for ${platform} (min: ${req.minDuration}s)`);
  }

  if (durationSeconds > req.maxDuration) {
    warnings.push(`Video is too long for ${platform} (max: ${req.maxDuration}s)`);
  }

  // Check file size
  if (fileSizeMB > req.maxFileSize) {
    warnings.push(
      `File size (${fileSizeMB.toFixed(1)}MB) exceeds ${platform} limit (${req.maxFileSize}MB)`
    );
  }

  return {
    valid: warnings.length === 0,
    warnings
  };
}
```

## Platform Presets

Define common platform combinations in `.upload-config.json`:

```json
{
  "platform_presets": {
    "short-form": ["tiktok", "instagram", "youtube"],
    "professional": ["linkedin", "youtube"],
    "all-social": ["facebook", "instagram", "threads", "tiktok", "youtube"],
    "mobile-only": ["tiktok", "instagram", "threads"],
    "desktop-only": ["youtube", "linkedin", "facebook"]
  }
}
```

Use presets:
```bash
npx ts-node scripts/upload-video.ts \
  --video out/video.mp4 \
  --preset short-form
```

## Remotion Composition Patterns

### Pattern 1: Multi-Platform Composition

Create compositions for each platform:

```typescript
// src/Root.tsx
export const RemotionRoot = () => {
  return (
    <>
      {/* Vertical for TikTok/Instagram */}
      <Composition
        id="MyVideo-Vertical"
        component={MyVideo}
        durationInFrames={900}
        fps={30}
        width={1080}
        height={1920}
      />

      {/* Horizontal for YouTube */}
      <Composition
        id="MyVideo-Horizontal"
        component={MyVideo}
        durationInFrames={900}
        fps={30}
        width={1920}
        height={1080}
      />

      {/* Square for Instagram Feed */}
      <Composition
        id="MyVideo-Square"
        component={MyVideo}
        durationInFrames={900}
        fps={30}
        width={1080}
        height={1080}
      />
    </>
  );
};
```

Render and upload:
```bash
# Vertical
npx remotion render MyVideo-Vertical out/MyVideo-Vertical.mp4
npx ts-node scripts/upload-video.ts \
  --video out/MyVideo-Vertical.mp4 \
  --platforms tiktok,instagram,threads

# Horizontal
npx remotion render MyVideo-Horizontal out/MyVideo-Horizontal.mp4
npx ts-node scripts/upload-video.ts \
  --video out/MyVideo-Horizontal.mp4 \
  --platforms youtube,facebook,linkedin
```

### Pattern 2: Responsive Composition

Create a single responsive composition that adapts to aspect ratio:

```typescript
import { useCurrentFrame, useVideoConfig } from 'remotion';

export const ResponsiveVideo = () => {
  const { width, height } = useVideoConfig();
  const aspectRatio = width / height;

  const isVertical = aspectRatio < 1;
  const isSquare = Math.abs(aspectRatio - 1) < 0.1;

  return (
    <div style={{ width, height, backgroundColor: '#000' }}>
      {isVertical ? (
        <VerticalLayout />
      ) : isSquare ? (
        <SquareLayout />
      ) : (
        <HorizontalLayout />
      )}
    </div>
  );
};
```

## See Also

- [upload.md](upload.md) - Upload API integration
- [authentication.md](authentication.md) - JWT token management
- [status.md](status.md) - Upload status checking
