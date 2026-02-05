---
name: elevenlabs-voice
description: ElevenLabs TTS integration for generating voice narration
metadata:
  tags: elevenlabs, tts, voice, narration, audio, speech
---

# ElevenLabs Voice Generation

Generate AI voice narration using ElevenLabs Text-to-Speech for your Remotion videos.

## Configuration

- **Model**: `eleven_multilingual_v2` (supports multiple languages)
- **Voice ID**: `mYk0rAapHek2oTw18z8x`
- **Output Format**: `mp3_44100_128` (44.1kHz, 128kbps MP3)

## Environment Setup

Set the API key as an environment variable:

```bash
export ELEVENLABS_API_KEY=your_api_key_here
```

Or use a `.env` file:

```
ELEVENLABS_API_KEY=your_api_key_here
```

## TTS Generation Script

Create a script at `scripts/generate-voice.ts`:

```typescript
import { ElevenLabsClient } from "elevenlabs";
import * as fs from "fs";
import * as path from "path";

// Configuration
const VOICE_ID = "mYk0rAapHek2oTw18z8x";
const MODEL_ID = "eleven_multilingual_v2";
const OUTPUT_FORMAT = "mp3_44100_128";

interface VoiceSegment {
  id: string;
  text: string;
  languageCode?: string; // e.g., "en", "ko", "es"
}

async function generateVoice(segments: VoiceSegment[]) {
  const apiKey = process.env.ELEVENLABS_API_KEY;

  if (!apiKey) {
    console.error("Error: ELEVENLABS_API_KEY environment variable is not set");
    process.exit(1);
  }

  const client = new ElevenLabsClient({ apiKey });

  // Create output directory
  const outputDir = path.join(process.cwd(), "public/audio");
  if (!fs.existsSync(outputDir)) {
    fs.mkdirSync(outputDir, { recursive: true });
  }

  console.log("Generating voice audio files...\n");

  for (const segment of segments) {
    const filename = `${segment.id}.mp3`;
    const filepath = path.join(outputDir, filename);

    // Skip if file already exists
    if (fs.existsSync(filepath)) {
      console.log(`✓ ${segment.id} - already exists`);
      continue;
    }

    try {
      console.log(`Generating: ${segment.id}...`);

      const audio = await client.textToSpeech.convert(VOICE_ID, {
        text: segment.text,
        model_id: MODEL_ID,
        output_format: OUTPUT_FORMAT,
        language_code: segment.languageCode,
      });

      // Convert the readable stream to a buffer and save
      const chunks: Buffer[] = [];
      for await (const chunk of audio) {
        chunks.push(Buffer.from(chunk));
      }
      const buffer = Buffer.concat(chunks);
      fs.writeFileSync(filepath, buffer);

      console.log(`✓ Saved: ${filename}`);

      // Small delay to avoid rate limiting
      await new Promise((resolve) => setTimeout(resolve, 500));
    } catch (error) {
      console.error(`✗ Error generating ${segment.id}:`, error);
    }
  }

  console.log("\n✅ Voice generation complete!");
}

// Example usage
const segments: VoiceSegment[] = [
  { id: "intro", text: "Welcome to our video!", languageCode: "en" },
  { id: "main", text: "Here is the main content.", languageCode: "en" },
  { id: "outro", text: "Thanks for watching!", languageCode: "en" },
];

generateVoice(segments).catch(console.error);
```

## Running the Script

```bash
npx ts-node scripts/generate-voice.ts
```

## Single Audio Generation

For generating a single audio file:

```typescript
import { ElevenLabsClient } from "elevenlabs";
import * as fs from "fs";

async function generateSingleAudio(text: string, outputPath: string) {
  const client = new ElevenLabsClient({
    apiKey: process.env.ELEVENLABS_API_KEY,
  });

  const audio = await client.textToSpeech.convert("mYk0rAapHek2oTw18z8x", {
    text,
    model_id: "eleven_multilingual_v2",
    output_format: "mp3_44100_128",
  });

  const chunks: Buffer[] = [];
  for await (const chunk of audio) {
    chunks.push(Buffer.from(chunk));
  }

  fs.writeFileSync(outputPath, Buffer.concat(chunks));
}

// Usage
generateSingleAudio(
  "Hello, this is a test narration.",
  "public/audio/narration.mp3"
);
```

## Multilingual Support

The `eleven_multilingual_v2` model supports multiple languages. Specify the language code:

```typescript
const audio = await client.textToSpeech.convert(VOICE_ID, {
  text: "Hola, bienvenidos a nuestro video!", // Spanish
  model_id: "eleven_multilingual_v2",
  output_format: "mp3_44100_128",
  language_code: "es", // Spanish
});
```

Supported language codes include:
- `en` - English
- `ko` - Korean
- `es` - Spanish
- `fr` - French
- `de` - German
- `ja` - Japanese
- `zh` - Chinese
- And many more...

## Voice Settings (Optional)

Customize voice characteristics:

```typescript
const audio = await client.textToSpeech.convert(VOICE_ID, {
  text: "Your text here",
  model_id: "eleven_multilingual_v2",
  output_format: "mp3_44100_128",
  voice_settings: {
    stability: 0.5, // 0-1, higher = more consistent
    similarity_boost: 0.75, // 0-1, higher = more similar to original voice
    style: 0.5, // 0-1, style exaggeration
    use_speaker_boost: true, // Enhance voice clarity
  },
});
```

## Error Handling

Handle common errors:

```typescript
try {
  const audio = await client.textToSpeech.convert(VOICE_ID, {
    text: "Your text here",
    model_id: "eleven_multilingual_v2",
    output_format: "mp3_44100_128",
  });
  // Process audio...
} catch (error: any) {
  if (error.status === 401) {
    console.error("Invalid API key");
  } else if (error.status === 429) {
    console.error("Rate limit exceeded. Wait and retry.");
  } else if (error.status === 400) {
    console.error("Invalid request parameters");
  } else {
    console.error("Unexpected error:", error);
  }
}
```

## Using Generated Audio in Remotion

After generating audio, use it in your composition:

```tsx
import { Audio, staticFile } from "remotion";

export const MyVideo: React.FC = () => {
  return (
    <AbsoluteFill>
      <Audio src={staticFile("audio/narration.mp3")} />
      {/* Your video content */}
    </AbsoluteFill>
  );
};
```
