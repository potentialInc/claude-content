---
name: remotion-generate-video
description: Generate Remotion videos with AI voice narration in reels format
metadata:
  tags: remotion, video, elevenlabs, tts, reels, voice, generation
---

## When to use

Use this skill when you need to generate a Remotion video composition with:
- AI-generated voice narration (ElevenLabs)
- Reels format (1080x1920 vertical)
- Automated TTS workflow

## Video Format

All generated videos use the **reels format**:
- **Width**: 1080px
- **Height**: 1920px
- **FPS**: 30
- **Aspect Ratio**: 9:16 (vertical)

## Assets

- **Logo**: `Resources/logo.png` - Use with `staticFile("logo.png")` after copying to `public/`
- **Audio Output**: Generated audio files are saved to `public/audio/`

## Voice Configuration

- **Provider**: ElevenLabs
- **Model**: `eleven_multilingual_v2`
- **Voice ID**: `mYk0rAapHek2oTw18z8x`
- **Output Format**: `mp3_44100_128`

## How to use

Read individual rule files for detailed explanations and code examples:

- [rules/workflow.md](rules/workflow.md) - End-to-end workflow for generating videos
- [rules/elevenlabs-voice.md](rules/elevenlabs-voice.md) - ElevenLabs TTS integration and voice generation
- [rules/composition-template.md](rules/composition-template.md) - Reels composition template and patterns

## Environment Setup

Ensure the following environment variable is set:
```bash
export ELEVENLABS_API_KEY=your_api_key_here
```

Or create a `.env` file in the project root:
```
ELEVENLABS_API_KEY=your_api_key_here
```

## Quick Start

1. Write your video script
2. Generate voice audio using ElevenLabs (see [elevenlabs-voice.md](rules/elevenlabs-voice.md))
3. Create the composition using the reels template (see [composition-template.md](rules/composition-template.md))
4. Preview with `npm run dev`
5. Render with `npx remotion render`
