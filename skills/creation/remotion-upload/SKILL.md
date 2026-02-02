---
name: remotion-upload
description: Upload Remotion videos to social media platforms via Upload Posts API
metadata:
  tags: remotion, upload, social-media, facebook, instagram, threads, youtube, tiktok, api
when:
  patterns:
    - upload
    - post
    - publish
    - share
  files:
    - out/*.mp4
  mentions:
    - facebook
    - instagram
    - threads
    - tiktok
    - youtube
    - social media
    - upload posts
---

# Remotion Upload Skill

Upload rendered Remotion videos to multiple social media platforms using the Upload Posts API.

## When to use

Use this skill when:
- Uploading rendered Remotion videos to social media platforms (Facebook, Instagram, Threads, TikTok, YouTube, etc.)
- Managing Upload Posts API authentication and configuration
- Checking upload status or viewing upload history
- Setting up platform-specific metadata for video uploads
- Batch uploading multiple videos from the `/out/` directory

## Quick Start

### First Time Setup

Before uploading videos, you need to configure your Upload Posts API credentials:

```bash
npx ts-node scripts/setup-upload.ts
```

This will:
1. Prompt for your JWT token
2. Set up your user profile (team@ktalk.live)
3. Configure default platforms (facebook, instagram, threads)
4. Create `.upload-config.json` and add it to `.gitignore`
5. Validate the token with the Upload Posts API

### Upload a Video

After setup, upload a rendered video:

```bash
npx ts-node scripts/upload-video.ts --video out/CoFoundry.mp4
```

Or use Claude Code to help:

```
User: "Upload the CoFoundry video to Facebook and Instagram"
```

The skill will:
1. Prompt for platforms (or use your defaults)
2. Suggest platforms based on aspect ratio
3. Prompt for title and description
4. Upload the video
5. Show upload status and URLs

## Commands

The skill provides several ways to interact with the Upload Posts API:

### Setup
```bash
npx ts-node scripts/setup-upload.ts
```
Interactive configuration wizard

### Upload Single Video
```bash
npx ts-node scripts/upload-video.ts --video <path>
```
Upload a specific video with interactive prompts

### Upload with Options
```bash
npx ts-node scripts/upload-video.ts \
  --video out/video.mp4 \
  --platforms facebook,instagram,threads \
  --title "My Video Title" \
  --description "Video description"
```
Upload with all parameters specified

### Batch Upload
```bash
npx ts-node scripts/upload-video.ts --batch
```
Upload all videos in the `/out/` directory

### Check Status
```bash
npx ts-node scripts/upload-video.ts --status
```
View recent upload history and status

### Check Specific Upload
```bash
npx ts-node scripts/upload-video.ts --status <upload_id>
```
Check status of a specific upload

## Features

### Smart Platform Suggestions

The skill automatically suggests appropriate platforms based on your video's aspect ratio:

- **9:16 (Vertical)** - 1080x1920
  - Suggested: TikTok, Instagram Reels, YouTube Shorts, Threads
  - Best for: Short-form mobile content

- **16:9 (Horizontal)** - 1920x1080
  - Suggested: YouTube, Facebook, LinkedIn
  - Best for: Long-form desktop content

- **1:1 (Square)** - 1080x1080
  - Suggested: Instagram Feed, Facebook, LinkedIn
  - Best for: Feed posts

### Metadata Templates

Save time with reusable metadata templates in `.upload-config.json`:

```json
{
  "metadata_templates": {
    "educational": {
      "description_template": "Learn {topic} with K Talk Live! #education #learning"
    },
    "marketing": {
      "description_template": "Check out {product}! Visit {website}"
    }
  }
}
```

### Composition Awareness

The skill can detect Remotion composition names and suggest metadata:

- `CoFoundry.mp4` → Title: "CoFoundry - Where Ideas Become Companies"
- `KoreanCounting.mp4` → Title: "Learn Korean Numbers 1-10"

### Batch Operations

Upload multiple videos at once with consistent or per-video metadata:

```bash
# Upload all videos with same metadata
npx ts-node scripts/upload-video.ts \
  --batch \
  --platforms facebook,instagram

# Or use interactive mode to customize each video
npx ts-node scripts/upload-video.ts --batch
```

## Supported Platforms

The Upload Posts API supports the following platforms:

- **Facebook** - Posts, Reels
- **Instagram** - Feed, Reels, Stories
- **Threads** - Video posts
- **TikTok** - Short-form videos
- **YouTube** - Regular videos, Shorts
- **LinkedIn** - Professional content
- **X (Twitter)** - Video posts
- **Pinterest** - Video pins
- **Bluesky** - Video posts

Your default platforms: **facebook, instagram, threads**

## Workflow Integration

### After Rendering

```bash
# Render your composition
npx remotion render CoFoundry out/CoFoundry.mp4

# Upload immediately
npx ts-node scripts/upload-video.ts --video out/CoFoundry.mp4
```

### Combined Workflow

```bash
# Render and upload in one command
npx remotion render CoFoundry out/CoFoundry.mp4 && \
  npx ts-node scripts/upload-video.ts \
    --video out/CoFoundry.mp4 \
    --platforms facebook,instagram,threads \
    --title "CoFoundry - Where Ideas Become Companies"
```

## Authentication & Security

### JWT Token Storage

Your JWT token is securely stored in `.upload-config.json` at the project root:

```json
{
  "jwt_token": "eyJhbGci...",
  "user_profile": "team@ktalk.live",
  "default_platforms": ["facebook", "instagram", "threads"]
}
```

**IMPORTANT**: This file is automatically added to `.gitignore` to prevent accidental token exposure.

### Security Best Practices

1. **Never commit `.upload-config.json`** - Contains sensitive JWT token
2. **Token is masked in output** - Only first 10 and last 5 characters shown
3. **Validate before storing** - Setup script tests token before saving
4. **Restricted permissions** - Config file is created with mode 600

### Token Validation

The skill validates your token before operations:
- Tests connection to Upload Posts API
- Shows clear error messages if token is invalid
- Prompts for new token if expired

## Detailed Documentation

For detailed information about specific features, see the rules documentation:

- **[rules/upload.md](rules/upload.md)** - Core upload functionality, API integration, error handling
- **[rules/authentication.md](rules/authentication.md)** - JWT token management, security best practices
- **[rules/platforms.md](rules/platforms.md)** - Platform-specific requirements, aspect ratio guidelines
- **[rules/status.md](rules/status.md)** - Status checking, upload history, retry logic

## Examples

### Example 1: Quick Upload

```bash
# Render video
npx remotion render KoreanCounting out/KoreanCounting.mp4

# Upload with interactive prompts
npx ts-node scripts/upload-video.ts --video out/KoreanCounting.mp4

# The skill will:
# 1. Detect aspect ratio (9:16 vertical)
# 2. Suggest: TikTok, Instagram, Threads
# 3. Prompt for title: "Learn Korean Numbers 1-10"
# 4. Prompt for description
# 5. Upload to selected platforms
```

### Example 2: Batch Upload

```bash
# Upload all videos in out/ directory
npx ts-node scripts/upload-video.ts --batch

# The skill will:
# 1. List all .mp4 files in /out/
# 2. Show video details (name, size, dimensions)
# 3. Prompt for platforms (can use defaults)
# 4. Prompt for metadata (bulk or individual)
# 5. Upload each video sequentially
# 6. Show summary report
```

### Example 3: Status Check

```bash
# View recent uploads
npx ts-node scripts/upload-video.ts --status

# Output:
# Upload History (Last 10)
# -------------------------
# ID: abc123
#   Video: CoFoundry.mp4
#   Platforms: facebook, instagram, threads
#   Status: completed
#   Uploaded: 2026-02-02 10:30:15
#   URLs:
#     - Facebook: https://facebook.com/...
#     - Instagram: https://instagram.com/...
#     - Threads: https://threads.net/...
```

### Example 4: Using with Claude Code

Simply ask Claude Code to upload videos:

```
User: "Upload the CoFoundry video to Facebook and Instagram with the title 'Where Ideas Become Companies'"
```

Claude Code will:
1. Load this skill automatically
2. Find the video in /out/
3. Upload with specified platforms and title
4. Show upload status and URLs

## Troubleshooting

### Token Invalid or Expired

```
Error: 401 Unauthorized - Token is invalid or expired
```

**Solution**: Run setup again to update your token:
```bash
npx ts-node scripts/setup-upload.ts
```

### Video Not Found

```
Error: Video file not found: out/video.mp4
```

**Solution**: Ensure you've rendered the video first:
```bash
npx remotion render <CompositionId> out/video.mp4
```

### Platform Not Supported

```
Error: Platform 'invalid' is not supported
```

**Solution**: Use one of the supported platforms:
- facebook, instagram, threads, tiktok, youtube, linkedin, x, pinterest, bluesky

### Upload Failed

```
Error: Upload failed - Network error
```

**Solution**: Check your internet connection and retry:
```bash
npx ts-node scripts/upload-video.ts --video <path>
```

## API Reference

The skill uses the Upload Posts API:

- **Base URL**: https://api.upload-post.com/api
- **Upload Endpoint**: POST /upload_videos
- **Status Endpoint**: GET /uploadposts/status
- **History Endpoint**: GET /uploadposts/history
- **Authentication**: Bearer token in Authorization header

For full API documentation, visit: https://docs.upload-post.com/

## Contributing

To extend this skill:

1. **Add new platforms**: Update `rules/platforms.md` with platform requirements
2. **Add metadata templates**: Edit `.upload-config.json` templates section
3. **Enhance scripts**: Modify `scripts/upload-video.ts` for new features
4. **Update documentation**: Keep rule files in sync with changes
