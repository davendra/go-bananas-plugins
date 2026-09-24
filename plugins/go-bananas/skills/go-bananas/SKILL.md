---
name: go-bananas
description: "AI image generation using Go Bananas MCP tools. Prefer the signed-in user's connected ChatGPT/Codex subscription by calling get_subscription_status, then generate_with_subscription; never silently fall back to provider APIs. Use for requests to generate, create, or edit images/pictures/photos/art using AI — NOT for code that handles images. Also covers provider-backed Gemini and OpenAI generation when explicitly requested, conversational editing, reusable characters/products, batches, style presets, quota, and history."
---

# Go Bananas! Image Generation

AI-powered image generation with Gemini Flash Lite, Gemini Flash, Gemini Pro, and OpenAI GPT Image 2 models, deployed on Cloudflare Workers.

## Subscription-first routing

For a normal Go Bananas image-creation request, use the connected ChatGPT/Codex subscription lane first:

1. Call `get_subscription_status`.
2. When `connected: true`, call `generate_with_subscription` with the prompt and supported aspect ratio.
3. Return the saved image ID, URL, `source: "chatgpt_subscription"`, and remaining allowance.
4. When it is not connected, tell the user to connect at `https://gobananasai.com` and stop. Do not call a provider-backed generation tool without explicit approval.

This lane requires Go Bananas user OAuth. An API-key-only MCP client has tenant identity but cannot access an individual user's subscription. The subscription runner does not use the tenant's Gemini or OpenAI API keys and never exposes ChatGPT credentials.

Use `generate_image`, `edit_image`, `continue_editing`, and other provider-backed tools only when the user explicitly requests a Go Bananas provider/API model or approves fallback after the subscription lane cannot run. `openai-gpt-image-2` is an OpenAI API model billed through configured provider credentials; it is not the ChatGPT subscription lane.

Portable subscription editing is not currently exposed. Explain that limitation and obtain explicit approval before using a provider-backed editing tool.

For the human CLI, `gb generate` uses the configured `auto` lane. Use `--subscription` to require subscription generation or `--provider` to explicitly use configured Gemini/OpenAI credentials. Always report the returned `source` and `providerApiUsed` provenance fields.

## Quick Start

### Option 1: MCP Server (Recommended)

If you have Go Bananas! MCP server configured, use the tools directly:

```
get_subscription_status - Check the connected user subscription first
generate_with_subscription - Generate one image without provider API billing
generate_image - Create new images from text prompts
continue_editing - Edit the last generated image conversationally
generate_with_character - Create scenes with saved characters
generate_with_product - Marketing images with product references
```

### Uploading Local Images (NEW)

Use `file_path` parameter to upload images directly from your machine:

```
upload_image_for_editing({ file_path: "/path/to/image.jpg" })
```

The STDIO proxy reads the file locally and uploads directly — no imgur or third-party services needed.
Supports: PNG, JPEG, WebP, GIF. Max 20MB.

### Option 2: REST API

If using HTTP endpoints:

```bash
# Generate image
curl -X POST https://gobananasai.com/api/images \
  -H "X-API-Key: sk_live_xxx" \
  -H "Content-Type: application/json" \
  -d '{"prompt": "A sunset over mountains", "aspect_ratio": "16:9"}'
```

## Provider-backed model selection

This section applies only after the user explicitly chooses or approves provider-backed generation. Before large batches, OpenAI high-quality generations, or 2K/4K work, call:

```
list_models()
check_quota({ estimated_images: 1, model_id: "gemini-flash-image" })
```

`list_models` shows tenant-enabled models and capability limits. `check_quota` returns storage quota, rate limit, service health, and an approximate provider-cost estimate. Quote the estimate to the user before expensive runs.

## Models

| Feature          | Lite (Flash Lite) | Standard (Flash) | Pro               | OpenAI GPT Image 2      |
| ---------------- | ----------------- | ---------------- | ----------------- | ----------------------- |
| Resolution       | 1K                | 0.5K, 1K, 2K, 4K | 1K, 2K, 4K        | Size/quality controlled |
| Reference images | Up to 3           | Up to 14         | Up to 14          | Up to 16                |
| Text rendering   | Basic             | Good             | Advanced          | Strong                  |
| Web grounding    | No                | Yes              | Yes               | No                      |
| Speed            | Fastest           | Fast             | Quality-optimized | Quality/size dependent  |

**Default provider model: Nano Banana 2 Lite (Flash Lite)**
Within the explicitly selected provider-backed lane, use `model_id: "gemini-flash-lite-image"` unless the user requests another model or the task clearly needs a capability Lite does not provide. Use Nano Banana 2 (`gemini-flash-image`) for broader aspect ratios, 2K/4K output, search grounding, or stronger multi-reference workflows. Use Pro (`gemini-pro-image`) when specifically needed for premium production quality, advanced text rendering, grounding, or 4K output.

**When to use:**

- **Lite (NB2 Lite)**: Default for provider-backed generation — fastest, lowest-cost 1K generation/editing
- **Standard (NB2)**: Use for broader ratios, 2K/4K output, grounding, and stronger multi-reference workflows
- **Pro**: Use for production print materials, infographics, text-heavy content, grounding, thinking mode, or 4K output
- **OpenAI GPT Image 2**: Use when OpenAI output controls, quality tiers, or format options are specifically useful

## Golden Rules of Prompting

_From Google DeepMind's official Gemini Pro guide_

| Rule                       | Description                                                         |
| -------------------------- | ------------------------------------------------------------------- |
| **1. Edit, Don't Re-roll** | If 80% correct, use `continue_editing` instead of regenerating      |
| **2. Natural Language**    | Write full sentences like briefing an artist, not tag soups         |
| **3. Materiality**         | Describe textures: "brushed steel", "soft velvet", "crumpled paper" |
| **4. Context**             | Add "for whom": "for a luxury cookbook" changes everything          |
| **5. Identity Locking**    | "Keep facial features exactly the same as Image 1"                  |

**Bad**: `dog, park, 4k, realistic`
**Good**: "A golden retriever playing fetch in a sunny park, captured in photorealistic detail"

## Advanced Techniques

| Technique            | Description                                                                            |
| -------------------- | -------------------------------------------------------------------------------------- |
| **Negative Prompts** | Tell model what NOT to include: "no date stamp", "no text", "not rustic"               |
| **JSON Prompting**   | Use structured JSON for complex scenes with precise control                            |
| **Prompt Evolution** | Start simple, iterate: "fashion photo" → "high-end fashion" → "winter fashion, daring" |
| **Upscaling**        | Images as small as 150x150 → 4K with Pro model                                         |
| **Multi-Reference**  | Use images for style, branding, colors, object placement                               |
| **360 Turnaround**   | Generate multiple angles from single reference                                         |

## Core Capabilities

### 1. Image Generation

Generate images with prompts, style presets, and reference images.

### 2. Conversational Editing

Edit images naturally: "add clouds", "make it brighter", "change to night scene"

### 3. Character Consistency

Save characters once, generate unlimited consistent scenes across sessions.

### 4. Product Marketing

Save product images, generate unlimited marketing scenes.

### 5. Style Presets

Save and reuse prompt templates for consistent branding.

### 6. Pro Model Features (Advanced)

- **Text & Infographics**: SOTA text rendering, data visualization
- **Viral Thumbnails**: Identity + text + graphics in one pass
- **Storyboarding**: Multi-scene sequential art with consistent characters
- **Structural Control**: Sketches/wireframes to polished designs
- **2D↔3D Translation**: Floor plans to renders, 2D art to 3D
- **4K Textures**: Print-quality output for wallpapers and materials

## Reference Documentation

For detailed information, see:

- **mcp-tools.md** - All 53 MCP tools with parameters and examples
- **rest-api.md** - REST endpoints with curl examples
- **prompt-patterns.md** - Best practices for writing effective prompts
- **models.md** - Detailed Flash vs Pro feature comparison
- **use-cases.md** - Templates for common scenarios (marketing, infographics, characters)
- **error-handling.md** - Quota management, rate limits, and graceful failure patterns

## Example Workflows

### Creating a Character Campaign

```
1. generate_image - Create initial character design
2. create_character - Save character with reference images
3. generate_with_character - Generate multiple scenes
```

### Product Marketing

```
1. create_product_reference - Save product from URL
2. generate_with_product - Generate marketing scenes
3. continue_editing - Refine results
```

### Consistent Branding

```
1. create_style_preset - Save brand style
2. generate_image with style_preset_name - Apply to all generations
3. get_style_preset - Review and verify preset settings
```

### Character Reference Sheet Design

```
1. generate_image with style_preset_name "character reference sheet" - Generate multi-panel sheet
2. continue_editing - Fix any inconsistencies ("make all panels show the same hair color")
3. create_character - Save with the reference sheet as reference image
4. generate_with_character - Test consistency in new scenes
```

## Workflow Guides

### Character Design Workflow

The generate → edit → audit → save cycle for reliable character consistency:

**Step 1: Initial Generation**
Generate a character reference sheet using the `character reference sheet` style preset. This produces an 8-panel turnaround with front, side, back, and expression views.

```
generate_image({
  prompt: "A female warrior with silver braided hair, blue eyes, leather armor with gold trim",
  style_preset_name: "character reference sheet",
  aspect_ratio: "16:9"
})
```

**Step 2: Iterative Refinement**
Use `continue_editing` to fix inconsistencies across panels. Common fixes:

- "Make hair color consistent across all panels"
- "Ensure armor details match between front and back views"
- "Fix the 3/4 view to show the same scar"

**Step 3: Save as Character**
Once the reference sheet is satisfactory, save it:

```
create_character({
  character_name: "Aria",
  base_prompt: "Female warrior, silver braided hair, blue eyes, leather armor with gold trim, athletic build",
  reference_image_ids: [<sheet_image_id>]
})
```

**Step 4: Test Consistency**
Generate 2-3 test scenes to verify the character looks right:

```
generate_with_character({ character_name: "Aria", scene_prompt: "standing in a medieval marketplace" })
generate_with_character({ character_name: "Aria", scene_prompt: "sitting by a campfire at night" })
```

### Style Preset Mastery

Style presets store reusable prompt templates, negative prompts, system instructions, and aspect ratios.

**Inspecting Presets**
Use `get_style_preset` to review any preset before using it:

```
get_style_preset({ name: "character reference sheet" })
```

**Creating Effective Presets**

- **Prompt** (max 4096 chars): The template applied to every generation. Use `{prompt}` or similar placeholders for the user's input.
- **Negative prompt**: Things to exclude — be specific: "no watermarks, no text overlays, no blurry edges"
- **System instruction** (max 1024 chars): High-level direction for the model's behavior
- **Aspect ratio**: Lock a ratio for consistency (e.g., "16:9" for all thumbnails)

**Testing Presets**
After creating or updating a preset, test with 3 different prompts to verify it works broadly:

```
generate_image({ prompt: "a forest scene", style_preset_name: "my-preset" })
generate_image({ prompt: "a portrait", style_preset_name: "my-preset" })
generate_image({ prompt: "a product on a table", style_preset_name: "my-preset" })
```

### Identity Locking Best Practices

When generating scenes with saved characters, use these techniques for maximum consistency:

**Use `character_ids` over `character_names`**
IDs are unambiguous and avoid partial name matching issues.

**Add Identity Locking Language to Scene Prompts**
Include explicit instructions in your scene prompt:

- "Keep the character's facial features exactly as shown in the reference"
- "Maintain identical clothing, hairstyle, and body proportions from the reference"
- "The character's eye color, skin tone, and facial structure must match the reference precisely"

**Multi-Character Scenes**
When using `generate_with_multiple_characters`, specify each character's role clearly:

```
generate_with_multiple_characters({
  character_ids: [24, 27],
  scene_prompt: "Character 1 (Priya) and Character 2 (Ram) walking hand in hand through a garden. Keep all facial features and clothing exactly matching their reference images.",
  aspect_ratio: "16:9"
})
```

**Troubleshooting Poor Consistency**

- Use `continue_editing` to fix specific features: "make the character's hair match the reference image"
- Provide more reference images when creating the character (up to 3 for Standard, 14 for Pro)
- Add specific physical descriptions to the character's `base_prompt`

### Reference Library Management

Keep your character and product libraries organized for efficient reuse.

**Characters**: Use `list_characters` to review all saved characters. Update descriptions with `update_character` when designs evolve.

**Products**: Use `list_product_references` to review products. Update metadata (name, description, tags) with `update_product_reference`.

**Reference Groups**: Bundle related reference images into groups for complex scenes requiring multiple visual references.

**Scenes**: Save frequently-used scene configurations for quick reuse across sessions.

## Related Skills

- **Workflow Diagrams** - Transform Mermaid syntax or natural language into professional flowcharts, infographics, timelines, and org charts using Pro model. Use `/diagram` command or ask to "create a flowchart".

- **MCP Creative Agents** - Spawn specialized agents for creative testing: Children's Book Illustrator, Product Marketing Creative, Fantasy World Builder, and Edge Case Tester. Use when you want to stress test MCP tools or generate complex multi-asset campaigns.
