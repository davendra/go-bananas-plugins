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
| model_id | string | No | gemini-flash-lite-image (default), gemini-nano-banana-2.1, gemini-pro-image, openai-gpt-image-2, openai-gpt-image-2.5-flare or openai-gpt-image-2.5-sunburst |
| project | string | No | Project to file the new images in (created if new; `none` for no project; blank counts as not given) |
| tags | string[] | No | Up to 10 tags for the new images |

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
- Field: `project` (optional) - Project to file the image in (`none` for no project; blank counts as not given)
- Field: `tags` (optional) - Tags, as repeated fields or one comma-separated value (up to 10)
- Header: `X-Go-Bananas-Project` (optional) - `encodeURIComponent(name)` of the connector's folder, used when there is no `project` field
- Max size: 20MB

The response also carries `project` (`{ "id", "name", "key" }` or `null`), and `warnings` if the
image was saved but its project and tags could not be applied.

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
| project | string | - | Project key (or name), or `none` for images without a project |
| tags | string | - | Tag keys, comma-separated or as repeated `tags` parameters; an image must have every one (up to 10) |

Each image also carries `project` (`{ "id", "name", "key" }` or `null`) and `tags`
(`[{ "name", "key" }]`), and so does `GET /api/images/:id`.

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

#### GET /api/images/ids
The ids of every image the list would show for the same filters (`search`, `project`, `tags`,
`sort` and the rest), in the same order, capped at 1,000. Used to select every matching image.

```bash
curl "https://gobananasai.com/api/images/ids?project=brand-refresh&tags=hero" \
  -H "X-API-Key: sk_live_xxx"
```

**Response:**
```json
{ "data": { "ids": [44, 43, 40], "total": 3, "truncated": false } }
```

#### POST /api/images/organize
Move up to 100 images to a project, or out of theirs, and add or remove tags. At least one of
`project`, `addTags` and `removeTags` is required.

```bash
curl -X POST https://gobananasai.com/api/images/organize \
  -H "X-API-Key: sk_live_xxx" \
  -H "Content-Type: application/json" \
  -d '{"imageIds": [42, 43], "project": {"name": "Brand Refresh"}, "addTags": ["hero"]}'
```

**Request Body:**
| Field | Type | Required | Description |
|-------|------|----------|-------------|
| imageIds | integer[] | Yes | 1-100 image ids |
| project | object or null | No | `{"name": "..."}` (found or created), `{"id": 7}`, or `null` to take the images out of their project |
| addTags | string[] | No | Up to 10 tags to add (an image holds at most 20) |
| removeTags | string[] | No | Up to 10 tags to remove |

**Response:**
```json
{
  "data": {
    "updated": [42, 43],
    "notFound": [],
    "project": { "id": 7, "name": "Brand Refresh", "key": "brand-refresh" },
    "addedTags": [{ "name": "hero", "key": "hero" }]
  }
}
```

Ids that do not exist, and other workspaces' ids, come back in `notFound`.

---

### Projects and Tags

Names match loosely by key: "Brand Refresh", "brand_refresh" and "BRAND-REFRESH" are one project
(key `brand-refresh`). Project names are up to 100 characters and tags up to 50; `none` is not a
project name. Changes need an owner or manager. OAuth clients can list projects and tags
(`images:read`). They create them by organizing images (`POST /api/images/organize`,
`images:edit`), or by filing new images with `project` or `tags` on generate (`POST /api/images`,
`images:generate`) and upload (`POST /api/images/upload`, `images:edit`). Renaming, merging and
deleting are not available to them.

#### GET /api/projects
Projects, most recently used first, and how many images have no project.

```json
{
  "data": {
    "projects": [
      { "id": 7, "name": "Brand Refresh", "key": "brand-refresh", "imageCount": 12, "lastImageAt": "2026-10-09 14:02:11" }
    ],
    "unassignedCount": 30
  }
}
```

#### POST /api/projects
Find or create a project by name: `{"name": "Brand Refresh"}`. Answers 201 when it was created and
200 when it already existed, with `{ "data": { "id", "name", "key", "created" } }`.

#### PATCH /api/projects/:id
Rename: `{"name": "Brand Refresh 2026"}`. The old name keeps finding the project. When another
project already uses the name, it answers 409:

```json
{ "error": "Another project already uses this name", "code": "project_name_taken", "conflictProjectId": 9 }
```

#### POST /api/projects/:id/merge
Merge into another project: `{"intoProjectId": 9}`. The images and old names move to the target.
Returns `{ "data": { "project": { "id", "name", "key" }, "movedImages": 12 } }`.

#### DELETE /api/projects/:id
Deletes the project; its images stay, without a project.
Returns `{ "data": { "deleted": true, "unassignedImages": 12 } }`.

#### GET /api/tags
`{ "data": { "tags": [{ "id": 3, "name": "hero", "key": "hero", "imageCount": 8 }] } }`, the most
used first.

#### DELETE /api/tags/:id
Removes the tag from every image. Returns `{ "data": { "deleted": true } }`.

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

All errors return JSON with `error` field. Most requests that can never succeed (400, 404, 409) also carry a `code`; a few older checks, such as `Invalid JSON payload` and the `PATCH /api/products/:id` field check, do not:

```json
{
  "error": "Image not found",
  "code": "image_not_found"
}
```

**HTTP Status Codes:**
| Code | Meaning |
|------|---------|
| 400 | Bad Request - Invalid parameters (`invalid_request`, `unknown_model`, `model_not_allowed`, `invalid_reference_assets`). Fix the request; do not retry |
| 401 | Unauthorized - Invalid or missing API key |
| 402 | Payment Required - Monthly storage quota exceeded |
| 403 | Forbidden - Tenant access denied |
| 404 | Not Found - Resource doesn't exist in this workspace (`image_not_found`, `character_not_found`, ...) |
| 409 | Conflict - Name already in use (`character_name_taken`, `product_name_taken`, ...) |
| 422 | Unprocessable Content - The image provider rejected the workspace's API key (`provider_credentials_rejected`). Fix the key in workspace settings |
| 429 | Too Many Requests - The workspace's rate limit exceeded |
| 500 | Internal Server Error - The provider's own message (timeout, outage, per-minute rate limit, billing, daily quota), or a generic message with an `errorId` |

See error-handling.md for which errors to retry.

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
