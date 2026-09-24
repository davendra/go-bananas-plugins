# Go Bananas! REST API Reference

HTTP endpoints for image generation when not using MCP.

---

## Authentication

All requests require an API key in the header:

```bash
-H "X-API-Key: sk_live_your_key_here"
# or
-H "Authorization: Bearer sk_live_your_key_here"
```

---

## Base URL

```
https://gobananasai.com
```

---

## Endpoints

### Profile & Quotas

#### GET /api/profile
Get tenant profile and usage quotas.

```bash
curl https://gobananasai.com/api/profile \
  -H "X-API-Key: sk_live_xxx"
```

**Response:**
```json
{
  "tenant": {
    "tenantId": "acme-corp",
    "tenantName": "Acme Corporation",
    "quotas": {
      "monthly_mb": 1000,
      "used_mb": 125,
      "remaining_mb": 875
    },
    "settings": {
      "defaultAspectRatio": "1:1",
      "maxImagesPerGeneration": 4
    }
  }
}
```

---

### Image Generation

#### POST /api/images
Generate new images.

```bash
curl -X POST https://gobananasai.com/api/images \
  -H "X-API-Key: sk_live_xxx" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "A sunset over mountains with purple clouds",
    "aspect_ratio": "16:9",
    "n": 2
  }'
```

**Request Body:**
| Field | Type | Required | Description |
|-------|------|----------|-------------|
| prompt | string | Yes | Image description |
| n | integer | No | Number of images (1-4) |
| aspect_ratio | string | No | square, portrait, landscape, 16:9, 9:16, 4:3, 3:4 |
| negative_prompt | string | No | What to avoid |
| system_instruction | string | No | Style guidance |
| style_preset_id | integer | No | Apply preset by ID |
| style_preset_name | string | No | Apply preset by name |
| model_id | string | No | gemini-flash-image or gemini-pro-image |

**Response:**
```json
{
  "images": [
    {
      "id": 42,
      "url": "https://pub-xxx.r2.dev/tenant/image.png",
      "thumbnail_url": "https://pub-xxx.r2.dev/tenant/image-thumb.jpg",
      "width": 1820,
      "height": 1024,
      "format": "png",
      "size_bytes": 1234567
    }
  ],
  "session_id": "session-abc123"
}
```

#### POST /api/images/upload
Upload an image file directly for editing. **Recommended for large images** as it bypasses MCP token limits.

```bash
curl -X POST https://gobananasai.com/api/images/upload \
  -H "X-API-Key: sk_live_xxx" \
  -F "file=@/path/to/image.png"
```

**Request:**
- Content-Type: `multipart/form-data`
- Field: `file` - Image file (PNG, JPEG, WebP)
- Max size: 20MB

**Response:**
```json
{
  "image_id": 42,
  "url": "https://pub-xxx.r2.dev/tenant/image.png",
  "thumbnail_url": "https://pub-xxx.r2.dev/tenant/image-thumb.jpg",
  "width": 1920,
  "height": 1080,
  "format": "png",
  "size_bytes": 2345678
}
```

**Usage with MCP:**
After uploading, use the returned `image_id` with the `edit_image` or `edit_uploaded_image` MCP tools:
```
edit_image(image_id=42, prompt="add dramatic clouds")
```

**Why use this endpoint?**
- Base64-encoded images consume ~180k tokens, exceeding MCP conversation limits
- Direct file upload has no token overhead
- Faster for large images

---

### Image Gallery

#### GET /api/images
List images with pagination and search.

```bash
# Basic pagination
curl "https://gobananasai.com/api/images?limit=20&offset=0" \
  -H "X-API-Key: sk_live_xxx"

# With search
curl "https://gobananasai.com/api/images?search=sunset&limit=10" \
  -H "X-API-Key: sk_live_xxx"
```

**Query Parameters:**
| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| limit | integer | 50 | Max results (max 200) |
| offset | integer | 0 | Pagination offset |
| search | string | - | Search in prompts |

**Response:**
```json
{
  "images": [
    {
      "id": 42,
      "url": "https://...",
      "thumbnail_url": "https://...",
      "prompt": "A sunset over mountains",
      "created_at": "2025-01-15T10:30:00Z",
      "operation_type": "generate"
    }
  ],
  "pagination": {
    "total": 150,
    "limit": 20,
    "offset": 0,
    "has_more": true
  }
}
```

#### GET /api/images/:id
Get single image metadata.

```bash
curl https://gobananasai.com/api/images/42 \
  -H "X-API-Key: sk_live_xxx"
```

#### DELETE /api/images/:id
Delete a single image.

```bash
curl -X DELETE https://gobananasai.com/api/images/42 \
  -H "X-API-Key: sk_live_xxx"
```

**Response:** `204 No Content`

#### POST /api/images/delete-bulk
Delete multiple images (max 20).

```bash
curl -X POST https://gobananasai.com/api/images/delete-bulk \
  -H "X-API-Key: sk_live_xxx" \
  -H "Content-Type: application/json" \
  -d '{"ids": [42, 43, 44]}'
```

**Response:**
```json
{
  "deleted": [42, 43],
  "failed": [{"id": 44, "reason": "Not found"}]
}
```

---

### Sessions

#### GET /api/sessions
List all sessions.

```bash
curl https://gobananasai.com/api/sessions \
  -H "X-API-Key: sk_live_xxx"
```

**Response:**
```json
{
  "sessions": [
    {
      "session_id": "session-abc",
      "total_images": 15,
      "total_edits": 8,
      "last_activity_at": "2025-01-15T10:30:00Z"
    }
  ]
}
```

#### GET /api/sessions/:id
Get session details with images.

```bash
curl https://gobananasai.com/api/sessions/session-abc \
  -H "X-API-Key: sk_live_xxx"
```

---

### Usage Analytics

#### GET /api/usage
Get usage statistics.

```bash
# Last 30 days
curl "https://gobananasai.com/api/usage?days=30" \
  -H "X-API-Key: sk_live_xxx"

# Custom date range
curl "https://gobananasai.com/api/usage?start=2025-01-01&end=2025-01-31" \
  -H "X-API-Key: sk_live_xxx"
```

**Response:**
```json
{
  "summary": {
    "total_images": 523,
    "total_edits": 189,
    "total_size_mb": 125.5,
    "period_start": "2025-01-01",
    "period_end": "2025-01-31"
  },
  "daily": [
    {"date": "2025-01-15", "images": 25, "edits": 10, "size_mb": 5.2}
  ]
}
```

---

### Characters

#### GET /api/characters
List characters.

```bash
curl "https://gobananasai.com/api/characters?limit=10" \
  -H "X-API-Key: sk_live_xxx"
```

#### POST /api/characters
Create a character.

```bash
curl -X POST https://gobananasai.com/api/characters \
  -H "X-API-Key: sk_live_xxx" \
  -H "Content-Type: application/json" \
  -d '{
    "character_name": "Hero Henry",
    "base_prompt": "10-year-old boy with brown hair, blue eyes, red cape"
  }'
```

#### DELETE /api/characters/:id
Delete a character.

```bash
curl -X DELETE https://gobananasai.com/api/characters/5 \
  -H "X-API-Key: sk_live_xxx"
```

---

### Style Presets

#### GET /api/style-presets
List all presets.

```bash
curl https://gobananasai.com/api/style-presets \
  -H "X-API-Key: sk_live_xxx"
```

#### POST /api/style-presets
Create a preset.

```bash
curl -X POST https://gobananasai.com/api/style-presets \
  -H "X-API-Key: sk_live_xxx" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Watercolor",
    "prompt": "watercolor painting style,",
    "system_instruction": "Soft artistic watercolor aesthetic"
  }'
```

#### DELETE /api/style-presets/:id
Delete a preset.

```bash
curl -X DELETE https://gobananasai.com/api/style-presets/3 \
  -H "X-API-Key: sk_live_xxx"
```

---

### Image Proxy

#### GET /images/:r2Key
Authenticated image access.

```bash
# View image
curl "https://gobananasai.com/images/tenant/2025-01-15/image.png" \
  -H "X-API-Key: sk_live_xxx"

# Download with filename
curl "https://gobananasai.com/images/tenant/2025-01-15/image.png?download=true" \
  -H "X-API-Key: sk_live_xxx" \
  -o image.png
```

---

## Error Responses

All errors return JSON with `error` field:

```json
{
  "error": "Image not found",
  "code": "NOT_FOUND"
}
```

**HTTP Status Codes:**
| Code | Meaning |
|------|---------|
| 400 | Bad Request - Invalid parameters |
| 401 | Unauthorized - Invalid or missing API key |
| 403 | Forbidden - Tenant access denied |
| 404 | Not Found - Resource doesn't exist |
| 429 | Too Many Requests - Rate limit exceeded |
| 500 | Internal Server Error |

---

## Rate Limits

Rate limits are per-tenant, configured during provisioning. Default:
- 60 requests per minute
- 1000 images per month (varies by plan)

When rate limited, response includes:
```
X-RateLimit-Remaining: 0
X-RateLimit-Reset: 1705320000
```
