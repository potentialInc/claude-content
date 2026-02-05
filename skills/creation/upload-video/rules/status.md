---
name: status
description: Upload status checking and history management
metadata:
  tags: status, history, monitoring, tracking
---

# Upload Status and History

This document describes how to check upload status, view upload history, and manage completed uploads.

## Status Endpoint

### GET /uploadposts/status

Check the status of recent uploads or a specific upload.

**URL**: `https://api.upload-post.com/api/uploadposts/status`

**Method**: GET

**Authentication**: Required (Bearer token)

**Parameters**:
- `request_id` (optional) - Specific upload ID to check
- `job_id` (optional) - Alternative ID for checking specific upload

### Example Request

Check all recent uploads:
```bash
curl -H "Authorization: Bearer eyJhbGci..." \
  https://api.upload-post.com/api/uploadposts/status
```

Check specific upload:
```bash
curl -H "Authorization: Bearer eyJhbGci..." \
  "https://api.upload-post.com/api/uploadposts/status?request_id=abc123"
```

### Response Format

```json
{
  "uploads": [
    {
      "id": "abc123def456",
      "video_filename": "CoFoundry.mp4",
      "title": "CoFoundry - Where Ideas Become Companies",
      "status": "completed",
      "created_at": "2026-02-02T10:30:15Z",
      "updated_at": "2026-02-02T10:35:42Z",
      "platforms": {
        "facebook": {
          "status": "completed",
          "url": "https://facebook.com/watch/123456789",
          "uploaded_at": "2026-02-02T10:32:10Z"
        },
        "instagram": {
          "status": "completed",
          "url": "https://instagram.com/p/ABC123DEF",
          "uploaded_at": "2026-02-02T10:34:25Z"
        },
        "threads": {
          "status": "completed",
          "url": "https://threads.net/@user/post/XYZ789",
          "uploaded_at": "2026-02-02T10:35:42Z"
        }
      }
    }
  ]
}
```

### Status Values

| Status | Description |
|--------|-------------|
| `queued` | Upload accepted, waiting to process |
| `processing` | Currently uploading to platforms |
| `completed` | Successfully uploaded to all platforms |
| `partial` | Uploaded to some platforms, failed on others |
| `failed` | Upload failed completely |

## History Endpoint

### GET /uploadposts/history

View historical uploads with pagination.

**URL**: `https://api.upload-post.com/api/uploadposts/history`

**Method**: GET

**Authentication**: Required (Bearer token)

**Parameters**:
- `page` (optional) - Page number (default: 1)
- `limit` (optional) - Results per page (default: 10)

### Example Request

```bash
curl -H "Authorization: Bearer eyJhbGci..." \
  "https://api.upload-post.com/api/uploadposts/history?page=1&limit=10"
```

### Response Format

```json
{
  "uploads": [
    {
      "id": "abc123",
      "title": "CoFoundry - Where Ideas Become Companies",
      "video_filename": "CoFoundry.mp4",
      "status": "completed",
      "platforms": ["facebook", "instagram", "threads"],
      "created_at": "2026-02-02T10:30:15Z"
    },
    {
      "id": "def456",
      "title": "Learn Korean Numbers 1-10",
      "video_filename": "KoreanCounting.mp4",
      "status": "completed",
      "platforms": ["tiktok", "instagram", "youtube"],
      "created_at": "2026-02-01T15:20:30Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 10,
    "total": 25,
    "pages": 3
  }
}
```

## Checking Status

### Check Recent Uploads

```typescript
import fetch from 'node-fetch';

async function checkStatus(config: UploadConfig): Promise<void> {
  const response = await fetch(
    'https://api.upload-post.com/api/uploadposts/status',
    {
      headers: {
        'Authorization': `Bearer ${config.jwt_token}`
      }
    }
  );

  if (!response.ok) {
    throw new Error(`Failed to check status: ${response.statusText}`);
  }

  const data = await response.json();

  console.log('\nRecent Uploads\n' + '='.repeat(50));

  data.uploads.forEach((upload: any) => {
    console.log(`\nID: ${upload.id}`);
    console.log(`  Video: ${upload.video_filename}`);
    console.log(`  Title: ${upload.title}`);
    console.log(`  Status: ${upload.status}`);
    console.log(`  Created: ${new Date(upload.created_at).toLocaleString()}`);

    if (upload.platforms) {
      console.log(`  Platforms:`);
      Object.entries(upload.platforms).forEach(([platform, info]: [string, any]) => {
        const icon = info.status === 'completed' ? '✓' :
                     info.status === 'failed' ? '✗' : '⏳';
        console.log(`    ${icon} ${platform}: ${info.status}`);
        if (info.url) {
          console.log(`       ${info.url}`);
        }
      });
    }
  });
}
```

### Check Specific Upload

```typescript
async function checkUploadById(
  uploadId: string,
  config: UploadConfig
): Promise<void> {
  const response = await fetch(
    `https://api.upload-post.com/api/uploadposts/status?request_id=${uploadId}`,
    {
      headers: {
        'Authorization': `Bearer ${config.jwt_token}`
      }
    }
  );

  if (!response.ok) {
    throw new Error(`Failed to check status: ${response.statusText}`);
  }

  const data = await response.json();

  if (data.uploads.length === 0) {
    console.log(`No upload found with ID: ${uploadId}`);
    return;
  }

  const upload = data.uploads[0];

  console.log('\nUpload Details\n' + '='.repeat(50));
  console.log(`ID: ${upload.id}`);
  console.log(`Video: ${upload.video_filename}`);
  console.log(`Title: ${upload.title}`);
  console.log(`Status: ${upload.status}`);
  console.log(`Created: ${new Date(upload.created_at).toLocaleString()}`);
  console.log(`Updated: ${new Date(upload.updated_at).toLocaleString()}`);

  console.log('\nPlatform Status:');
  Object.entries(upload.platforms).forEach(([platform, info]: [string, any]) => {
    const icon = info.status === 'completed' ? '✓' :
                 info.status === 'failed' ? '✗' : '⏳';
    console.log(`  ${icon} ${platform.toUpperCase()}`);
    console.log(`     Status: ${info.status}`);
    if (info.url) {
      console.log(`     URL: ${info.url}`);
    }
    if (info.uploaded_at) {
      console.log(`     Uploaded: ${new Date(info.uploaded_at).toLocaleString()}`);
    }
    if (info.error) {
      console.log(`     Error: ${info.error}`);
    }
  });
}
```

## Viewing History

### Display Upload History

```typescript
async function viewHistory(
  config: UploadConfig,
  page: number = 1,
  limit: number = 10
): Promise<void> {
  const response = await fetch(
    `https://api.upload-post.com/api/uploadposts/history?page=${page}&limit=${limit}`,
    {
      headers: {
        'Authorization': `Bearer ${config.jwt_token}`
      }
    }
  );

  if (!response.ok) {
    throw new Error(`Failed to fetch history: ${response.statusText}`);
  }

  const data = await response.json();

  console.log('\nUpload History\n' + '='.repeat(50));
  console.log(`Page ${data.pagination.page} of ${data.pagination.pages}`);
  console.log(`Showing ${data.uploads.length} of ${data.pagination.total} total uploads\n`);

  data.uploads.forEach((upload: any, index: number) => {
    const icon = upload.status === 'completed' ? '✓' :
                 upload.status === 'failed' ? '✗' :
                 upload.status === 'partial' ? '⚠' : '⏳';

    console.log(`${index + 1}. ${icon} ${upload.title}`);
    console.log(`   ID: ${upload.id}`);
    console.log(`   File: ${upload.video_filename}`);
    console.log(`   Platforms: ${upload.platforms.join(', ')}`);
    console.log(`   Status: ${upload.status}`);
    console.log(`   Created: ${new Date(upload.created_at).toLocaleString()}`);
    console.log('');
  });

  if (data.pagination.pages > 1) {
    console.log('To view more, use:');
    if (page < data.pagination.pages) {
      console.log(`  Next page: --page ${page + 1}`);
    }
    if (page > 1) {
      console.log(`  Previous page: --page ${page - 1}`);
    }
  }
}
```

## Status Polling

For large uploads, poll the status endpoint until completion:

```typescript
async function pollUploadStatus(
  uploadId: string,
  config: UploadConfig,
  intervalMs: number = 5000,
  maxAttempts: number = 60
): Promise<void> {
  console.log(`Polling status for upload ${uploadId}...`);

  for (let attempt = 0; attempt < maxAttempts; attempt++) {
    const response = await fetch(
      `https://api.upload-post.com/api/uploadposts/status?request_id=${uploadId}`,
      {
        headers: {
          'Authorization': `Bearer ${config.jwt_token}`
        }
      }
    );

    if (!response.ok) {
      console.error(`Failed to check status: ${response.statusText}`);
      return;
    }

    const data = await response.json();
    const upload = data.uploads[0];

    if (!upload) {
      console.error('Upload not found');
      return;
    }

    // Check overall status
    if (upload.status === 'completed') {
      console.log('\n✓ Upload completed successfully!');
      console.log('\nPlatform URLs:');
      Object.entries(upload.platforms).forEach(([platform, info]: [string, any]) => {
        if (info.url) {
          console.log(`  ${platform}: ${info.url}`);
        }
      });
      return;
    }

    if (upload.status === 'failed') {
      console.log('\n✗ Upload failed');
      Object.entries(upload.platforms).forEach(([platform, info]: [string, any]) => {
        if (info.error) {
          console.log(`  ${platform}: ${info.error}`);
        }
      });
      return;
    }

    // Show progress
    const completed = Object.values(upload.platforms).filter(
      (p: any) => p.status === 'completed'
    ).length;
    const total = Object.keys(upload.platforms).length;

    process.stdout.write(
      `\r⏳ Processing... ${completed}/${total} platforms completed`
    );

    // Wait before next poll
    await new Promise(resolve => setTimeout(resolve, intervalMs));
  }

  console.log('\n⚠ Polling timeout. Check status manually:');
  console.log(`  npx ts-node scripts/upload-video.ts --status ${uploadId}`);
}
```

## Retry Failed Uploads

If an upload fails or only partially succeeds, retry:

```typescript
async function retryFailedUpload(
  uploadId: string,
  config: UploadConfig
): Promise<void> {
  // Get upload details
  const response = await fetch(
    `https://api.upload-post.com/api/uploadposts/status?request_id=${uploadId}`,
    {
      headers: {
        'Authorization': `Bearer ${config.jwt_token}`
      }
    }
  );

  const data = await response.json();
  const upload = data.uploads[0];

  if (!upload) {
    console.error('Upload not found');
    return;
  }

  // Find failed platforms
  const failedPlatforms = Object.entries(upload.platforms)
    .filter(([_, info]: [string, any]) => info.status === 'failed')
    .map(([platform]) => platform);

  if (failedPlatforms.length === 0) {
    console.log('No failed platforms to retry');
    return;
  }

  console.log(`Retrying upload to: ${failedPlatforms.join(', ')}`);

  // Re-upload to failed platforms
  // Note: You'll need the original video file path
  console.log('⚠ Retry requires original video file');
  console.log('Please re-upload manually:');
  console.log(
    `  npx ts-node scripts/upload-video.ts ` +
    `--video out/${upload.video_filename} ` +
    `--platforms ${failedPlatforms.join(',')}`
  );
}
```

## Command Line Interface

### Check Status Command

```bash
# View recent uploads
npx ts-node scripts/upload-video.ts --status

# Check specific upload
npx ts-node scripts/upload-video.ts --status abc123
```

### View History Command

```bash
# View first page (10 results)
npx ts-node scripts/upload-video.ts --history

# View specific page
npx ts-node scripts/upload-video.ts --history --page 2

# Custom page size
npx ts-node scripts/upload-video.ts --history --page 1 --limit 20
```

## Output Formatting

### Compact Format

For quick status checks:

```
Recent Uploads (Last 5)
==================================================
✓ CoFoundry.mp4 (completed) - facebook, instagram, threads
✓ KoreanCounting.mp4 (completed) - tiktok, instagram, youtube
⏳ TestVideo.mp4 (processing) - youtube
✗ Failed.mp4 (failed) - tiktok
⚠ Partial.mp4 (partial) - facebook (✓), instagram (✗)
```

### Detailed Format

For thorough inspection:

```
Upload Details
==================================================
ID: abc123def456
Video: CoFoundry.mp4
Title: CoFoundry - Where Ideas Become Companies
Status: completed
Created: 2026-02-02 10:30:15
Updated: 2026-02-02 10:35:42

Platform Status:
  ✓ FACEBOOK
     Status: completed
     URL: https://facebook.com/watch/123456789
     Uploaded: 2026-02-02 10:32:10

  ✓ INSTAGRAM
     Status: completed
     URL: https://instagram.com/p/ABC123DEF
     Uploaded: 2026-02-02 10:34:25

  ✓ THREADS
     Status: completed
     URL: https://threads.net/@user/post/XYZ789
     Uploaded: 2026-02-02 10:35:42
```

## Integration with Upload Workflow

### Post-Upload Status Check

After uploading, automatically poll for completion:

```typescript
async function uploadAndWait(
  videoPath: string,
  platforms: string[],
  title: string,
  config: UploadConfig
): Promise<void> {
  // Upload video
  console.log('Uploading video...');
  const result = await uploadVideo(videoPath, platforms, title, config);

  console.log(`✓ Upload initiated! ID: ${result.upload_id}`);

  // Poll for completion
  await pollUploadStatus(result.upload_id, config);
}
```

## Error Handling

### Status Check Errors

```typescript
try {
  await checkStatus(config);
} catch (error) {
  if (error.status === 401) {
    console.error('✗ Authentication failed');
    console.error('Please run setup to update your token:');
    console.error('  npx ts-node scripts/setup-upload.ts');
  } else {
    console.error(`✗ Failed to check status: ${error.message}`);
  }
}
```

### No Uploads Found

```typescript
if (data.uploads.length === 0) {
  console.log('No uploads found.');
  console.log('Upload a video first:');
  console.log('  npx ts-node scripts/upload-video.ts --video out/video.mp4');
}
```

## Best Practices

1. **Check status after upload** - Verify upload succeeded
2. **Poll for large files** - Monitor progress for videos > 100MB
3. **Save upload IDs** - Keep track of important uploads
4. **Retry failed uploads** - Don't lose work due to temporary failures
5. **Monitor regularly** - Check upload history periodically

## See Also

- [upload.md](upload.md) - Upload API integration
- [authentication.md](authentication.md) - JWT token management
- [platforms.md](platforms.md) - Platform requirements
