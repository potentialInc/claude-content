---
name: upload
description: Core video upload functionality and API integration
metadata:
  tags: upload, api, multipart, video
---

# Video Upload

This document describes the core upload functionality for uploading Remotion videos to social media platforms using the Upload Posts API.

## Upload Posts API

### Base URL
```
https://api.upload-post.com/api
```

### Upload Endpoint
```
POST /upload_videos
```

Uploads a video to one or more social media platforms.

### Authentication

All API requests require authentication using a JWT Bearer token:

```
Authorization: Bearer eyJhbGci...
```

The token is stored in `.upload-config.json` and automatically included in requests.

## Request Format

The upload endpoint accepts `multipart/form-data` requests with the following fields:

### Required Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `user` | string | User profile name (e.g., "team@ktalk.live") |
| `platform[]` | string[] | Target platforms (can specify multiple) |
| `title` | string | Video title |
| `media` | file | Video file to upload |

### Optional Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `description` | string | Video description (required for some platforms) |

### Platform Values

Supported platform values for `platform[]`:

- `facebook` - Facebook posts and reels
- `instagram` - Instagram feed, reels, stories
- `threads` - Threads video posts
- `tiktok` - TikTok short-form videos
- `youtube` - YouTube videos and shorts
- `linkedin` - LinkedIn professional content
- `x` - X (Twitter) video posts
- `pinterest` - Pinterest video pins
- `bluesky` - Bluesky video posts

**Note**: You can specify multiple platforms by including multiple `platform[]` fields in the form data.

## Example Request

### Using cURL

```bash
curl -X POST https://api.upload-post.com/api/upload_videos \
  -H "Authorization: Bearer eyJhbGci..." \
  -F "user=team@ktalk.live" \
  -F "platform[]=facebook" \
  -F "platform[]=instagram" \
  -F "platform[]=threads" \
  -F "title=CoFoundry - Where Ideas Become Companies" \
  -F "description=Learn about CoFoundry's startup accelerator. Visit getcofoundry.com" \
  -F "media=@out/CoFoundry.mp4"
```

### Using TypeScript (Node.js)

```typescript
import FormData from 'form-data';
import fs from 'fs';
import fetch from 'node-fetch';

async function uploadVideo(
  videoPath: string,
  platforms: string[],
  title: string,
  description?: string
) {
  // Read configuration
  const config = JSON.parse(
    fs.readFileSync('.upload-config.json', 'utf-8')
  );

  // Prepare form data
  const formData = new FormData();
  formData.append('user', config.user_profile);

  // Add each platform
  platforms.forEach(platform => {
    formData.append('platform[]', platform);
  });

  formData.append('title', title);

  if (description) {
    formData.append('description', description);
  }

  // Add video file
  formData.append('media', fs.createReadStream(videoPath));

  // Make request
  const response = await fetch(
    'https://api.upload-post.com/api/upload_videos',
    {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${config.jwt_token}`,
        ...formData.getHeaders()
      },
      body: formData
    }
  );

  if (!response.ok) {
    throw new Error(`Upload failed: ${response.status} ${response.statusText}`);
  }

  return await response.json();
}

// Example usage
await uploadVideo(
  'out/CoFoundry.mp4',
  ['facebook', 'instagram', 'threads'],
  'CoFoundry - Where Ideas Become Companies',
  'Learn about CoFoundry startup accelerator'
);
```

## Response Format

### Success Response

```json
{
  "success": true,
  "upload_id": "abc123def456",
  "status": "processing",
  "platforms": {
    "facebook": {
      "status": "queued",
      "url": null
    },
    "instagram": {
      "status": "queued",
      "url": null
    },
    "threads": {
      "status": "queued",
      "url": null
    }
  },
  "created_at": "2026-02-02T10:30:15Z"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | Whether upload was accepted |
| `upload_id` | string | Unique upload identifier |
| `status` | string | Overall upload status (processing, completed, failed) |
| `platforms` | object | Per-platform status and URLs |
| `created_at` | string | Upload timestamp (ISO 8601) |

### Error Response

```json
{
  "success": false,
  "error": "Invalid token",
  "code": 401
}
```

## Upload Process Flow

1. **Read Configuration**
   - Load JWT token from `.upload-config.json`
   - Load user profile and default platforms

2. **Validate Input**
   - Check video file exists
   - Verify file is .mp4 format
   - Check file size (warn if > 100MB)
   - Validate platform names

3. **Detect Video Properties**
   - Extract dimensions (width x height)
   - Calculate aspect ratio
   - Determine duration
   - Suggest appropriate platforms

4. **Prepare Upload**
   - Create multipart form data
   - Add user profile
   - Add platforms
   - Add title and description
   - Attach video file

5. **Execute Upload**
   - Make POST request to `/upload_videos`
   - Include Authorization header with Bearer token
   - Send multipart form data

6. **Handle Response**
   - Check for success/error
   - Extract upload ID
   - Display platform status
   - Show any warnings

7. **Poll Status** (optional)
   - Query `/uploadposts/status` endpoint
   - Check upload progress
   - Display platform URLs when available

## Error Handling

### HTTP Status Codes

| Code | Meaning | Action |
|------|---------|--------|
| 200 | Success | Process response |
| 400 | Bad Request | Validate parameters |
| 401 | Unauthorized | Prompt for new token |
| 413 | Payload Too Large | Check file size |
| 429 | Too Many Requests | Implement backoff |
| 500 | Server Error | Retry with backoff |

### Common Errors

#### 401 Unauthorized

**Cause**: Invalid or expired JWT token

**Solution**:
```bash
# Re-run setup to get new token
npx ts-node scripts/setup-upload.ts
```

#### 400 Bad Request

**Cause**: Missing or invalid parameters

**Check**:
- Video file exists and is readable
- Title is not empty
- Platforms are valid
- User profile is configured

#### 413 Payload Too Large

**Cause**: Video file exceeds platform limits

**Solution**:
- Compress video
- Reduce resolution
- Shorten duration
- Use different export settings in Remotion

#### 429 Too Many Requests

**Cause**: Rate limit exceeded

**Solution**:
- Implement exponential backoff
- Add delays between batch uploads
- Check API rate limits

### Error Recovery

When an upload fails, the script should:

1. **Log detailed error** - Include response status and message
2. **Preserve state** - Save upload attempt details
3. **Offer retry** - Prompt user to retry with same parameters
4. **Suggest fixes** - Provide actionable error messages

Example error handling:

```typescript
try {
  const result = await uploadVideo(videoPath, platforms, title, description);
  console.log(`✓ Upload successful! ID: ${result.upload_id}`);
} catch (error) {
  if (error.status === 401) {
    console.error('✗ Authentication failed. Please run setup:');
    console.error('  npx ts-node scripts/setup-upload.ts');
  } else if (error.status === 413) {
    console.error('✗ Video file is too large.');
    console.error('  Try reducing resolution or duration.');
  } else if (error.status === 429) {
    console.error('✗ Rate limit exceeded. Please wait and try again.');
  } else {
    console.error(`✗ Upload failed: ${error.message}`);
    console.error('  Please check your internet connection and try again.');
  }
}
```

## File Size Considerations

### Recommendations

- **Under 100MB**: Upload should complete quickly
- **100-500MB**: May take several minutes
- **Over 500MB**: Consider compression or chunked upload

### File Size Check

Before uploading, check file size:

```typescript
import fs from 'fs';

function checkFileSize(filePath: string): void {
  const stats = fs.statSync(filePath);
  const sizeMB = stats.size / (1024 * 1024);

  if (sizeMB > 500) {
    console.warn(`⚠ Warning: File is ${sizeMB.toFixed(1)}MB`);
    console.warn('  Large files may take several minutes to upload.');
  }

  console.log(`File size: ${sizeMB.toFixed(1)}MB`);
}
```

## Progress Indication

For large uploads, show progress:

```typescript
import fs from 'fs';

function uploadWithProgress(filePath: string): void {
  const fileSize = fs.statSync(filePath).size;
  let uploaded = 0;

  const stream = fs.createReadStream(filePath);

  stream.on('data', (chunk) => {
    uploaded += chunk.length;
    const percent = ((uploaded / fileSize) * 100).toFixed(1);
    process.stdout.write(`\rUploading: ${percent}%`);
  });

  stream.on('end', () => {
    console.log('\nUpload complete!');
  });
}
```

## Platform-Specific Validation

Before uploading, validate video meets platform requirements:

### TikTok
- Aspect ratio: 9:16 (vertical)
- Max duration: 10 minutes
- Optimal: 15-60 seconds

### Instagram Reels
- Aspect ratio: 9:16 (vertical)
- Duration: 15-90 seconds
- Max file size: 4GB

### YouTube
- Aspect ratio: 16:9 (horizontal) for regular, 9:16 for Shorts
- Max duration: Unlimited (Shorts: 60 seconds)
- Max file size: 256GB

### Facebook
- Aspect ratio: Any (16:9, 9:16, 1:1)
- Max duration: 240 minutes
- Max file size: 10GB

See [platforms.md](platforms.md) for complete requirements.

## Batch Upload

For uploading multiple videos:

```typescript
async function batchUpload(
  videoPaths: string[],
  platforms: string[],
  titles: string[]
): Promise<void> {
  console.log(`Uploading ${videoPaths.length} videos...`);

  for (let i = 0; i < videoPaths.length; i++) {
    console.log(`\n[${i + 1}/${videoPaths.length}] ${videoPaths[i]}`);

    try {
      const result = await uploadVideo(
        videoPaths[i],
        platforms,
        titles[i]
      );

      console.log(`✓ Uploaded successfully! ID: ${result.upload_id}`);

      // Add delay to avoid rate limiting
      if (i < videoPaths.length - 1) {
        console.log('Waiting 2 seconds...');
        await new Promise(resolve => setTimeout(resolve, 2000));
      }
    } catch (error) {
      console.error(`✗ Failed: ${error.message}`);
      // Continue with next video
    }
  }

  console.log('\nBatch upload complete!');
}
```

## Best Practices

1. **Validate before upload**
   - Check file exists
   - Verify format is supported
   - Ensure title is not empty
   - Validate platform names

2. **Handle errors gracefully**
   - Provide clear error messages
   - Offer retry options
   - Log failures for debugging

3. **Respect rate limits**
   - Add delays between uploads
   - Implement exponential backoff on 429 errors
   - Monitor API usage

4. **Show progress**
   - Display upload progress for large files
   - Show platform-by-platform status
   - Provide estimated time remaining

5. **Secure credentials**
   - Never log JWT token
   - Mask token in output
   - Store in gitignored config file

6. **Test uploads**
   - Start with small test video
   - Verify upload to each platform
   - Check returned URLs work

## Integration with Remotion

### Render then Upload

```bash
# Render composition
npx remotion render CoFoundry out/CoFoundry.mp4

# Upload to platforms
npx ts-node scripts/upload-video.ts \
  --video out/CoFoundry.mp4 \
  --platforms facebook,instagram,threads \
  --title "CoFoundry - Where Ideas Become Companies"
```

### Combined Command

```bash
npx remotion render CoFoundry out/CoFoundry.mp4 && \
  npx ts-node scripts/upload-video.ts --video out/CoFoundry.mp4
```

### Auto-upload Hook

Create a post-render hook in `remotion.config.ts`:

```typescript
export default {
  // ... other config
  onRenderComplete: async ({ outputPath }) => {
    console.log('Render complete! Uploading...');
    // Call upload script
    execSync(
      `npx ts-node scripts/upload-video.ts --video ${outputPath}`,
      { stdio: 'inherit' }
    );
  }
};
```

## See Also

- [authentication.md](authentication.md) - JWT token management
- [platforms.md](platforms.md) - Platform-specific requirements
- [status.md](status.md) - Status checking and history
