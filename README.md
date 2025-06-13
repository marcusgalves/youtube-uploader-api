# YouTube Uploader API

A Node.js API for uploading videos to YouTube using OAuth2 with proxy support.

## Features

- ✅ Video upload via streaming (without loading entire file into memory)
- ✅ OAuth2 authentication with access token
- ✅ HTTP/HTTPS and SOCKS proxy support
- ✅ Complete video metadata configuration
- ✅ Multi-language localizations support
- ✅ Privacy controls and advanced settings
- ✅ Health check endpoint

## Installation

```bash
npm install express googleapis https-proxy-agent socks-proxy-agent dotenv
```

## Configuration

1. Create a `.env` file in the project root:

```env
PORT=3000
```

2. Set up Google OAuth2 credentials:
   - Access the [Google Cloud Console](https://console.cloud.google.com/)
   - Create a project and enable YouTube Data API v3
   - Configure OAuth2 credentials and obtain the access token

## Running

```bash
node server.js
```

The server will be available at `http://localhost:3000`

## Endpoints

### Health Check

```http
GET /health
```

**Response:**
```json
{
  "status": "ok",
  "timestamp": 1672531200000
}
```

### Video Upload

```http
POST /upload
```

**Headers:**
- `Authorization: Bearer YOUR_ACCESS_TOKEN` (required)
- `proxy_url: http://proxy:8080` (optional)
- `Content-Type: application/json`

**Complete JSON Body Example:**

```json
{
  "filePath": "/path/to/video.mp4",
  "title": "My Amazing Video",
  "description": "Detailed description of my video with important information.",
  "tags": ["technology", "tutorial", "nodejs", "youtube"],
  "categoryId": "28",
  "defaultLanguage": "en-US",
  "defaultAudioLanguage": "en-US",
  "localizations": {
    "pt-BR": {
      "title": "Meu Vídeo Incrível",
      "description": "Descrição detalhada do meu vídeo com informações importantes."
    },
    "es": {
      "title": "Mi Video Increíble",
      "description": "Descripción detallada de mi video con información importante."
    }
  },
  "privacyStatus": "private",
  "publishAt": "2024-12-25T10:00:00Z",
  "license": "youtube",
  "embeddable": true,
  "publicStatsViewable": true,
  "madeForKids": false,
  "selfDeclaredMadeForKids": false,
  "containsSyntheticMedia": false,
  "recordingDetails": {
    "recordingDate": "2024-01-15T14:30:00Z",
    "locationDescription": "New York, USA"
  },
  "contentDetails": {
    "caption": "false",
    "definition": "hd",
    "dimension": "2d",
    "licensedContent": false
  }
}
```

**Required Fields:**
- `filePath`: Path to the video file on the server
- `title`: Video title

**Success Response:**
```json
{
  "success": true,
  "id": "VIDEO_ID",
  "url": "https://youtu.be/VIDEO_ID"
}
```

**Error Response:**
```json
{
  "error": "Detailed error message"
}
```

## Detailed Parameters

### Snippet (Video Metadata)

| Field | Type | Description |
|-------|------|-------------|
| `title` | string | Video title (required) |
| `description` | string | Video description |
| `tags` | array | Array of tags/keywords |
| `categoryId` | string | YouTube category ID |
| `defaultLanguage` | string | Default language (e.g., "en-US") |
| `defaultAudioLanguage` | string | Audio language (e.g., "en-US") |

### Localizations (Translations)

Object with translations for different languages:

```json
{
  "localizations": {
    "pt-BR": {
      "title": "Título em Português",
      "description": "Descrição em Português"
    },
    "es": {
      "title": "Título en Español",
      "description": "Descripción en Español"
    }
  }
}
```

### Status (Privacy Settings)

| Field | Type | Description |
|-------|------|-------------|
| `privacyStatus` | string | "private", "public", "unlisted" |
| `publishAt` | string | Publication date/time (ISO 8601) |
| `license` | string | "youtube" or "creativeCommon" |
| `embeddable` | boolean | Allow video embedding |
| `publicStatsViewable` | boolean | Public statistics visible |
| `madeForKids` | boolean | Content made for children |
| `selfDeclaredMadeForKids` | boolean | Self-declared for children |
| `containsSyntheticMedia` | boolean | Contains synthetic media (AI) |

### Recording Details

```json
{
  "recordingDetails": {
    "recordingDate": "2024-01-15T14:30:00Z",
    "locationDescription": "New York, USA"
  }
}
```

### Content Details

```json
{
  "contentDetails": {
    "caption": "false",
    "definition": "hd",
    "dimension": "2d",
    "licensedContent": false
  }
}
```

## YouTube Categories

| ID | Category |
|----|----------|
| 1 | Film & Animation |
| 2 | Autos & Vehicles |
| 10 | Music |
| 15 | Pets & Animals |
| 17 | Sports |
| 19 | Travel & Events |
| 20 | Gaming |
| 22 | People & Blogs |
| 23 | Comedy |
| 24 | Entertainment |
| 25 | News & Politics |
| 26 | Howto & Style |
| 27 | Education |
| 28 | Science & Technology |
| 29 | Nonprofits & Activism |

## Proxy Support

The API supports HTTP/HTTPS and SOCKS proxies. Send the proxy URL in the `proxy_url` header:

```bash
# HTTP/HTTPS Proxy
curl -X POST http://localhost:3000/upload \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "proxy_url: http://proxy.example.com:8080" \
  -H "Content-Type: application/json" \
  -d '{"filePath": "/path/to/video.mp4", "title": "Test Video"}'

# SOCKS Proxy
curl -X POST http://localhost:3000/upload \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "proxy_url: socks5://proxy.example.com:1080" \
  -H "Content-Type: application/json" \
  -d '{"filePath": "/path/to/video.mp4", "title": "Test Video"}'
```

## HTTP Status Codes

- `200` - Success
- `400` - Validation error (file not found, missing required fields, invalid proxy)
- `401` - Missing or invalid authorization token
- `500` - Internal server error

## Usage Examples

### Basic Upload

```bash
curl -X POST http://localhost:3000/upload \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "filePath": "/videos/my-video.mp4",
    "title": "My First Upload"
  }'
```

### Upload with Advanced Settings

```bash
curl -X POST http://localhost:3000/upload \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "filePath": "/videos/tutorial.mp4",
    "title": "Complete Tutorial",
    "description": "A detailed tutorial about the API",
    "tags": ["tutorial", "api", "nodejs"],
    "categoryId": "28",
    "privacyStatus": "public",
    "defaultLanguage": "en-US",
    "madeForKids": false
  }'
```

## Logging and Debugging

The API logs errors to the console. For more detailed debugging, monitor the server logs:

```bash
node server.js
```

## Limitations

- Video file must be accessible on the server's file system
- Requires a valid Google OAuth2 access token
- Must respect YouTube Data API v3 quota limitations
- Very large files may take time to upload

## License

This project is provided as-is, without warranties. Use at your own risk.