# Model Comparison: Standard vs Pro

Detailed comparison of Go Bananas! Standard (Gemini Flash) and Pro (Gemini Pro) models.

---

## Quick Reference

| Feature | Standard (Flash) | Pro |
|---------|------------------|-----|
| Model ID | `gemini-flash-image` | `gemini-pro-image` |
| Resolution | 1K (1024px) | 1K, 2K, 4K (up to 4096px) |
| Reference Images | Up to 3 | Up to 14 |
| Character Consistency | Up to 3 people | Up to 5 people |
| Text Rendering | Basic | Advanced (multilingual) |
| Web Search Grounding | No | Yes |
| Thinking Mode | No | Yes |
| Speed | Fast (~3-5 sec) | Quality-optimized (~8-15 sec) |
| Best For | Iteration, volume | Production, print |

---

## Resolution Details

### Standard (Flash)
- Fixed 1K output (~1024x1024 for square)
- Sufficient for web, social media
- Fast generation

### Pro Resolution Options
| Setting | Approximate Size | Use Case |
|---------|------------------|----------|
| 1K | 1024px | Web, quick iteration |
| 2K | 2048px | High-quality web, presentations |
| 4K | 4096px | Print, billboards, large displays |

**Aspect Ratio Pixel Dimensions (4K):**
| Ratio | Dimensions |
|-------|------------|
| 1:1 | 4096 x 4096 |
| 16:9 | 4096 x 2304 |
| 9:16 | 2304 x 4096 |
| 3:2 | 4096 x 2730 |

**4K Texture Generation (Pro):**

Native high-resolution output is ideal for textures and large-format prints:

```json
{
  "prompt": "Harness native high-fidelity output to craft a breathtaking, atmospheric environment of a mossy forest floor. Command complex lighting effects and delicate textures, ensuring every strand of moss and beam of light is rendered in pixel-perfect resolution suitable for a 4K wallpaper.",
  "model_id": "gemini-pro-image",
  "resolution_tier": "4k",
  "aspect_ratio": "16:9"
}
```

**Best for:**
- Desktop/mobile wallpapers
- Print materials (posters, banners)
- Game textures
- Architectural visualization
- Product mockups

---

## Reference Image Limits

### Standard (Flash)
- Maximum 3 reference images
- Good for simple style references
- Single character consistency

### Pro
- Maximum 14 reference images total
- Up to 6 object images with high fidelity
- Up to 5 human images
- Up to 3 style/composition references

**Pro Multi-Image Breakdown:**
```
14 total = 6 objects + 5 people + 3 style references
```

**High Fidelity Mode (Pro):**
- 6 images can be processed with "high fidelity" for maximum detail preservation
- Essential for identity locking in character work
- Use the phrase: "Keep the person's facial features exactly the same as Image 1"

---

## Text Rendering

### Standard (Flash)
- Basic text rendering
- Simple words work best
- May have legibility issues with long text
- English primarily

### Pro
- Advanced text rendering
- Multi-line text support
- Stylized fonts
- Multilingual support
- Better for:
  - Infographics
  - Posters
  - Menus
  - Marketing materials
  - Technical diagrams

**Pro Text Tips:**
1. Put text in quotes
2. Specify font style
3. Indicate position
4. Keep text concise

---

## Web Search Grounding (Pro Only)

Pro can search the web for current information:

**Use Cases:**
- Weather visualizations with real data
- Stock charts with current prices
- Sports scores and statistics
- News event visualizations
- Geographic/map data

**Example:**
```json
{
  "prompt": "Create an infographic showing today's weather forecast for London with current temperature and 5-day outlook",
  "model_id": "gemini-pro-image"
}
```

---

## Thinking Mode (Pro Only)

Pro uses internal reasoning for complex compositions. It generates interim "thought images" (not charged) to refine composition before rendering the final output.

**Benefits:**
- Better handling of complex prompts
- Self-correction for perspective/geometry
- Multi-step composition planning
- More coherent multi-element scenes
- Can solve visual problems (math equations, diagrams)
- Data analysis and visual reasoning

**Best For:**
- Complex infographics
- Multi-character scenes
- Technical diagrams
- Precise layouts
- Problem-solving visualizations
- Sequential storyboarding

**Example - Visual Reasoning:**
```json
{
  "prompt": "Solve log_{x^2+1}(x^4-1)=2 on a whiteboard. Show the steps clearly.",
  "model_id": "gemini-pro-image"
}
```

**Example - Before/After Analysis:**
```json
{
  "prompt": "Analyze this image of a room and generate a 'before' image showing what the room might have looked like during construction.",
  "model_id": "gemini-pro-image",
  "reference_images": ["finished_room.jpg"]
}
```

---

## When to Use Each Model

### Use Standard (Flash) When:
- Rapid iteration on concepts
- High-volume generation
- Quick edits and adjustments
- Budget-conscious workflows
- Speed is critical
- Simple single-subject images
- Web-only output

### Use Pro When:
- Final production assets
- Print materials (4K resolution needed)
- Text-heavy content (infographics, posters)
- Multi-person character consistency
- Complex multi-image compositions
- Data-grounded visualizations
- Marketing/advertising campaigns
- Quality over speed

---

## Cost Considerations

### Standard (Flash)
- Lower cost per image
- Faster turnaround
- Good for iteration

### Pro
- Higher cost per image
- Worth it for:
  - Final deliverables
  - Print resolution
  - Complex compositions

**Recommended Workflow:**
```
1. Iterate with Standard (Flash) - develop concept
2. Refine prompt based on results
3. Generate final with Pro - production quality
```

---

## Feature Availability by Tool

| Tool | Standard | Pro |
|------|----------|-----|
| generate_image | ✅ | ✅ |
| edit_image | ✅ | ✅ |
| continue_editing | ✅ | ✅ |
| generate_with_character | ✅ (3 refs) | ✅ (14 refs) |
| generate_with_multiple_characters | ✅ (3 people) | ✅ (5 people) |
| generate_with_product | ✅ | ✅ |
| 4K resolution | ❌ | ✅ |
| Web grounding | ❌ | ✅ |
| Thinking mode | ❌ | ✅ |
| Advanced text | Limited | ✅ |

---

## Selecting Model in API

### MCP Tool
```json
{
  "prompt": "Your prompt here",
  "model_id": "gemini-pro-image"
}
```

### REST API
```bash
curl -X POST https://gobananasai.com/api/images \
  -H "X-API-Key: sk_live_xxx" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "Your prompt",
    "model_id": "gemini-pro-image"
  }'
```

---

## Upscaling & Restoration (Pro)

### High-Fidelity Upscaling

Pro model works as a powerful upscaling tool. Input images as small as 150x150 pixels can be intelligently enhanced to 4K resolution.

**Simple Upscaling:**
```json
{
  "prompt": "Upscale to 4K",
  "reference_images": ["small_image.png"],
  "model_id": "gemini-pro-image",
  "resolution_tier": "4k"
}
```

**Benefits:**
- Works with very small source images (150x150+)
- Intelligent detail enhancement (not just interpolation)
- Sharp, detailed output
- Perfect for print-ready assets

### Photo Restoration

Pro model can restore damaged, faded, or old photographs:

```json
{
  "prompt": "Restore this old photograph to pristine condition",
  "reference_images": ["damaged_photo.jpg"],
  "model_id": "gemini-pro-image"
}
```

**Restoration Capabilities:**
- Fix tears and scratches
- Repair fading and discoloration
- Remove damage artifacts
- Color correction
- Preserve original character

---

## Visual Reasoning & Puzzle Solving (Pro)

Pro model's thinking mode enables visual reasoning and puzzle-solving capabilities:

### Problem Solving
```json
{
  "prompt": "Return an image of the solved crossword, use green pen",
  "reference_images": ["crossword.png"],
  "model_id": "gemini-pro-image"
}
```

### Image Interpretation
- "What will happen next?"
- "Explain this with an infographic"
- "Overlay informative labels"
- "Create a visual summary"

### Reasoning Tasks
- Crossword and puzzle solving
- Mathematical equations
- Diagram analysis
- Before/after predictions
- Data interpretation

---

## Multi-Purpose Reference Images

Reference images serve multiple purposes beyond character consistency:

| Purpose | Example Prompt |
|---------|----------------|
| Object placement | "Place this product (Image 1) in a modern office" |
| Style transfer | "Create in the artistic style of Image 1" |
| Branding | "Put this logo (Image 1) on the product" |
| Color palette | "Use the color scheme from Image 1" |
| Novel objects | Teaching model about objects it doesn't know |

**Pro Handles Multiple Purposes:**
```json
{
  "prompt": "Logo from Image 1 on device from Image 2, styled like Image 3, colors from Image 4",
  "reference_images": ["logo.png", "device.png", "style.jpg", "colors.jpg"],
  "model_id": "gemini-pro-image"
}
```

---

## Tenant Model Access

Not all tenants have access to both models. Check your tenant profile:

```bash
curl https://gobananasai.com/api/profile \
  -H "X-API-Key: sk_live_xxx"
```

Response includes `allowedModels` array showing available models.
