---
name: authentication
description: JWT token management and secure configuration
metadata:
  tags: authentication, jwt, security, token, config
---

# Authentication

This document describes how to manage JWT tokens and secure configuration for the Upload Posts API.

## Overview

The Upload Posts API uses **JWT (JSON Web Token)** authentication. All API requests must include a valid JWT token in the `Authorization` header.

## Configuration File

### Location

`.upload-config.json` in the project root directory.

**CRITICAL**: This file MUST be added to `.gitignore` to prevent accidental token exposure.

### Structure

```json
{
  "jwt_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJlbWFpbCI6InRlYW1Aa3RhbGsubGl2ZSIsImV4cCI6NDkyMzYxMTQ0MiwianRpIjoiODI0MzZmOWQtNGQ1Ni00OTEzLTgwMGUtYTJhZmJiMGY3MWU5In0.AmIMF7kmYHogyo4a-MdI6ESur6_1Ay6r0lZFaubfMnU",
  "user_profile": "team@ktalk.live",
  "default_platforms": ["facebook", "instagram", "threads"],
  "metadata_templates": {
    "default": {
      "description_template": "Created with K Talk Live"
    },
    "educational": {
      "description_template": "Learn {topic} with K Talk Live! #education #learning"
    },
    "marketing": {
      "description_template": "Check out {product}! Visit {website}"
    }
  }
}
```

### Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `jwt_token` | string | Yes | JWT Bearer token for API authentication |
| `user_profile` | string | Yes | User profile name for uploads (e.g., email) |
| `default_platforms` | string[] | Yes | Default platforms for uploads |
| `metadata_templates` | object | No | Reusable metadata templates |

## JWT Token

### What is the JWT Token?

The user has provided this JWT token:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJlbWFpbCI6InRlYW1Aa3RhbGsubGl2ZSIsImV4cCI6NDkyMzYxMTQ0MiwianRpIjoiODI0MzZmOWQtNGQ1Ni00OTEzLTgwMGUtYTJhZmJiMGY3MWU5In0.AmIMF7kmYHogyo4a-MdI6ESur6_1Ay6r0lZFaubfMnU
```

This token contains:
- Email: team@ktalk.live
- Expiration: Unix timestamp 4923611442 (approximately year 2125)
- JTI (unique identifier): 824367f9d-4d56-4913-800e-a2afbb0f71e9

### Token Format

JWT tokens have three parts separated by dots (`.`):

```
[header].[payload].[signature]
```

Example:
```
eyJhbGci...  .  eyJlbWFpb...  .  AmIMF7k...
  ^header       ^payload           ^signature
```

### Using the Token

Include the token in the `Authorization` header with `Bearer` scheme:

```
Authorization: Bearer eyJhbGci...
```

Example cURL:
```bash
curl -H "Authorization: Bearer eyJhbGci..." \
  https://api.upload-post.com/api/uploadposts/status
```

Example TypeScript:
```typescript
const response = await fetch('https://api.upload-post.com/api/uploadposts/status', {
  headers: {
    'Authorization': `Bearer ${config.jwt_token}`
  }
});
```

## First-Time Setup

### Setup Wizard

Run the setup wizard to configure authentication:

```bash
npx ts-node scripts/setup-upload.ts
```

The wizard will:

1. **Check for existing configuration**
   - If `.upload-config.json` exists, ask to reconfigure or keep

2. **Prompt for JWT token**
   - Ask user to paste their JWT token
   - Validate token format (3 parts separated by dots)
   - Parse token payload to extract email

3. **Prompt for user profile**
   - Suggest email from token (team@ktalk.live)
   - Allow custom profile name

4. **Prompt for default platforms**
   - Show list of supported platforms
   - Allow multi-select
   - Default: facebook, instagram, threads

5. **Validate token**
   - Make test API call to `/uploadposts/status`
   - Check for 401 (invalid) or 200 (valid)

6. **Save configuration**
   - Write `.upload-config.json`
   - Set file permissions to 600 (owner read/write only)

7. **Update .gitignore**
   - Check if `.upload-config.json` is in `.gitignore`
   - Add if not present
   - Create `.gitignore` if it doesn't exist

8. **Confirm setup**
   - Show masked token (first 10 + last 5 chars)
   - Show user profile
   - Show default platforms

### Example Setup Flow

```
$ npx ts-node scripts/setup-upload.ts

Upload Posts API Setup
======================

No configuration found. Let's set up your Upload Posts API credentials.

Enter your JWT token:
> eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJlbWFpbCI6InRlYW1Aa3RhbGsubGl2ZSIsImV4cCI6NDkyMzYxMTQ0MiwianRpIjoiODI0MzZmOWQtNGQ1Ni00OTEzLTgwMGUtYTJhZmJiMGY3MWU5In0.AmIMF7kmYHogyo4a-MdI6ESur6_1Ay6r0lZFaubfMnU

✓ Token format valid
✓ Detected email: team@ktalk.live

Enter user profile name (default: team@ktalk.live):
> [Enter]

✓ Using profile: team@ktalk.live

Select default platforms (space to select, enter to confirm):
  [x] facebook
  [x] instagram
  [x] threads
  [ ] tiktok
  [ ] youtube
  [ ] linkedin
  [ ] x
  [ ] pinterest
  [ ] bluesky

✓ Selected platforms: facebook, instagram, threads

Validating token...
✓ Token is valid!

Saving configuration to .upload-config.json...
✓ Configuration saved
✓ Added .upload-config.json to .gitignore

Setup complete!
==============

Token: eyJhbGci...MnU (masked)
Profile: team@ktalk.live
Platforms: facebook, instagram, threads

You can now upload videos with:
  npx ts-node scripts/upload-video.ts --video out/video.mp4
```

## Token Validation

### Validate on Startup

Before making API requests, validate the token:

```typescript
async function validateToken(token: string): Promise<boolean> {
  try {
    const response = await fetch(
      'https://api.upload-post.com/api/uploadposts/status',
      {
        headers: {
          'Authorization': `Bearer ${token}`
        }
      }
    );

    return response.ok;
  } catch (error) {
    return false;
  }
}
```

### Handle Invalid Token

If token is invalid (401 Unauthorized):

```typescript
if (!response.ok && response.status === 401) {
  console.error('✗ Token is invalid or expired.');
  console.error('Please run setup to update your token:');
  console.error('  npx ts-node scripts/setup-upload.ts');
  process.exit(1);
}
```

### Token Expiration

The provided token expires at Unix timestamp `4923611442` (approximately year 2125), so expiration is not a concern for the foreseeable future.

However, if you receive 401 errors, the token may have been revoked. Re-run setup to get a new token.

## Security Best Practices

### 1. Never Commit Tokens

**CRITICAL**: Always add `.upload-config.json` to `.gitignore`

```bash
# .gitignore
.upload-config.json
.remotion-upload/
```

Check if file is tracked:
```bash
git status | grep .upload-config.json
```

If tracked, remove from git:
```bash
git rm --cached .upload-config.json
echo ".upload-config.json" >> .gitignore
git add .gitignore
git commit -m "Remove and ignore upload config file"
```

### 2. Mask Token in Output

Never display the full token. Always mask:

```typescript
function maskToken(token: string): string {
  if (token.length < 20) return '***';
  return `${token.substring(0, 10)}...${token.substring(token.length - 5)}`;
}

// Usage
console.log(`Token: ${maskToken(config.jwt_token)}`);
// Output: Token: eyJhbGci...MnU
```

### 3. Restrict File Permissions

Set configuration file to owner-only read/write:

```typescript
import fs from 'fs';

// After writing config file
fs.chmodSync('.upload-config.json', 0o600);
// Now only the owner can read/write
```

Linux/macOS permissions:
```
-rw------- 1 user user 512 Feb 02 10:30 .upload-config.json
```

### 4. Validate Before Storing

Always validate token format before saving:

```typescript
function isValidJWT(token: string): boolean {
  // JWT format: xxx.yyy.zzz
  const parts = token.split('.');

  if (parts.length !== 3) {
    return false;
  }

  // Each part should be base64url
  const base64urlRegex = /^[A-Za-z0-9_-]+$/;

  return parts.every(part => base64urlRegex.test(part));
}

if (!isValidJWT(userInput)) {
  console.error('✗ Invalid JWT token format');
  console.error('Expected format: xxx.yyy.zzz (3 parts separated by dots)');
  process.exit(1);
}
```

### 5. Environment Variables (Alternative)

For CI/CD or shared environments, use environment variables:

```bash
export UPLOAD_POST_JWT="eyJhbGci..."
export UPLOAD_POST_USER="team@ktalk.live"
```

Load in script:
```typescript
const config = {
  jwt_token: process.env.UPLOAD_POST_JWT || readConfig().jwt_token,
  user_profile: process.env.UPLOAD_POST_USER || readConfig().user_profile,
  // ...
};
```

### 6. Never Log Tokens

Ensure tokens are never logged:

```typescript
// BAD - logs full token
console.log('Config:', config);

// GOOD - mask sensitive fields
console.log('Config:', {
  ...config,
  jwt_token: maskToken(config.jwt_token)
});
```

### 7. Secure Error Messages

Don't expose tokens in error messages:

```typescript
// BAD
throw new Error(`Authentication failed with token: ${token}`);

// GOOD
throw new Error('Authentication failed. Please check your token.');
```

## Reading Configuration

### Load Configuration

```typescript
import fs from 'fs';

interface UploadConfig {
  jwt_token: string;
  user_profile: string;
  default_platforms: string[];
  metadata_templates?: Record<string, any>;
}

function readConfig(): UploadConfig {
  const configPath = '.upload-config.json';

  if (!fs.existsSync(configPath)) {
    console.error('✗ Configuration not found.');
    console.error('Please run setup first:');
    console.error('  npx ts-node scripts/setup-upload.ts');
    process.exit(1);
  }

  try {
    const content = fs.readFileSync(configPath, 'utf-8');
    return JSON.parse(content);
  } catch (error) {
    console.error('✗ Failed to read configuration:', error.message);
    console.error('Please run setup to recreate:');
    console.error('  npx ts-node scripts/setup-upload.ts');
    process.exit(1);
  }
}

// Usage
const config = readConfig();
console.log(`Using profile: ${config.user_profile}`);
console.log(`Token: ${maskToken(config.jwt_token)}`);
```

### Validate Configuration

```typescript
function validateConfig(config: UploadConfig): boolean {
  const errors: string[] = [];

  if (!config.jwt_token) {
    errors.push('Missing jwt_token');
  } else if (!isValidJWT(config.jwt_token)) {
    errors.push('Invalid jwt_token format');
  }

  if (!config.user_profile) {
    errors.push('Missing user_profile');
  }

  if (!config.default_platforms || config.default_platforms.length === 0) {
    errors.push('Missing default_platforms');
  }

  if (errors.length > 0) {
    console.error('✗ Invalid configuration:');
    errors.forEach(err => console.error(`  - ${err}`));
    console.error('\nPlease run setup:');
    console.error('  npx ts-node scripts/setup-upload.ts');
    return false;
  }

  return true;
}
```

## Updating Configuration

### Reconfigure

To update configuration, re-run setup:

```bash
npx ts-node scripts/setup-upload.ts
```

Setup will detect existing config and ask:
```
Configuration already exists.
What would you like to do?
  1. Update token only
  2. Update all settings
  3. Cancel
>
```

### Programmatic Update

Update specific fields:

```typescript
function updateConfig(updates: Partial<UploadConfig>): void {
  const config = readConfig();
  const newConfig = { ...config, ...updates };

  fs.writeFileSync(
    '.upload-config.json',
    JSON.stringify(newConfig, null, 2)
  );

  fs.chmodSync('.upload-config.json', 0o600);

  console.log('✓ Configuration updated');
}

// Example: Update default platforms
updateConfig({
  default_platforms: ['tiktok', 'instagram', 'youtube']
});
```

## Troubleshooting

### Configuration Not Found

```
✗ Configuration not found.
Please run setup first:
  npx ts-node scripts/setup-upload.ts
```

**Solution**: Run the setup wizard.

### Invalid Token Format

```
✗ Invalid JWT token format
Expected format: xxx.yyy.zzz (3 parts separated by dots)
```

**Solution**: Ensure you copied the complete token. JWT tokens have 3 parts separated by dots.

### Authentication Failed (401)

```
✗ Authentication failed: 401 Unauthorized
Token is invalid or expired.
```

**Solution**: Token may be revoked. Contact Upload Posts support or get a new token. Then re-run setup.

### Permission Denied

```
Error: EACCES: permission denied, open '.upload-config.json'
```

**Solution**: Check file permissions:
```bash
ls -la .upload-config.json
chmod 600 .upload-config.json
```

## See Also

- [upload.md](upload.md) - Using the token for uploads
- [status.md](status.md) - Checking upload status with authentication
- [platforms.md](platforms.md) - Platform requirements
