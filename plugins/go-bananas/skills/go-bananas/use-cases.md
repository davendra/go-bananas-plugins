# Use Case Templates

Ready-to-use prompt templates for common scenarios.

---

## Marketing & Advertising

### Social Media Post
```json
{
  "prompt": "Instagram-ready product shot of [PRODUCT] on clean white background,
soft studio lighting with subtle shadows, minimalist composition,
professional e-commerce photography style",
  "aspect_ratio": "square",
  "negative_prompt": "cluttered, busy background, harsh shadows"
}
```

### Instagram Story
```json
{
  "prompt": "[PRODUCT/SERVICE] promotional graphic with bold headline '[TEXT]',
vibrant gradient background in brand colors [COLORS],
modern minimalist design, space for text overlay",
  "aspect_ratio": "9:16",
  "system_instruction": "Clean social media design aesthetic"
}
```

### YouTube Thumbnail
```json
{
  "prompt": "Eye-catching YouTube thumbnail for video about [TOPIC],
bold expressive face showing [EMOTION], bright saturated colors,
dramatic lighting, text-friendly composition with space on right",
  "aspect_ratio": "16:9",
  "negative_prompt": "small details, cluttered, dark"
}
```

### Billboard Advertisement
```json
{
  "prompt": "4K billboard advertisement for [BRAND/PRODUCT],
hero product shot left of center, dramatic lighting,
large text area on right for headline '[TEXT]',
[BRAND COLORS] color scheme, premium luxury feel",
  "aspect_ratio": "16:9",
  "model_id": "gemini-pro-image"
}
```

### Viral Video Thumbnail (Pro Model)

*Identity locking + text + graphics in a single pass*

```json
{
  "prompt": "Design a viral video thumbnail using the person from Image 1.
Face Consistency: Keep the person's facial features exactly the same as Image 1, but change their expression to look excited and surprised.
Action: Pose the person on the left side, pointing their finger towards the right side of the frame.
Subject: On the right side, place a high-quality image of [PRODUCT/TOPIC].
Graphics: Add a bold yellow arrow connecting the person's finger to the subject.
Text: Overlay massive, pop-style text in the middle: '[HEADLINE]'. Use a thick white outline and drop shadow.
Background: A blurred, bright [SETTING] background. High saturation and contrast.",
  "aspect_ratio": "16:9",
  "model_id": "gemini-pro-image",
  "reference_images": ["[person_reference.jpg]"]
}
```

**Key technique**: The phrase "Keep the person's facial features exactly the same as Image 1" is critical for identity locking.

---

## Product Photography

### E-commerce Product Shot
```json
{
  "prompt": "Professional product photography of [PRODUCT],
45-degree angle on seamless white background,
soft even lighting, subtle reflection on surface,
sharp focus, clean commercial style",
  "aspect_ratio": "square",
  "negative_prompt": "shadows, color cast, reflections"
}
```

### Lifestyle Product Shot
```json
{
  "prompt": "[PRODUCT] in natural lifestyle setting,
[ENVIRONMENT: kitchen/office/bedroom/outdoors],
warm natural light from window, shallow depth of field,
product in focus, lifestyle photography style",
  "aspect_ratio": "4:3"
}
```

### Hero Product Shot
```json
{
  "prompt": "Premium hero shot of [PRODUCT] floating against
[dark/gradient/abstract] background, dramatic rim lighting,
reflections and highlights emphasizing form,
advertising campaign quality, luxury brand aesthetic",
  "aspect_ratio": "16:9",
  "model_id": "gemini-pro-image"
}
```

---

## Infographics (Pro Model Recommended)

### Data Infographic
```json
{
  "prompt": "Modern infographic about [TOPIC]:
Title '[TITLE]' in bold sans-serif at top,
3 main sections with icons: [SECTION1], [SECTION2], [SECTION3],
key statistics: [STAT1], [STAT2], [STAT3],
color palette: [PRIMARY], [SECONDARY], [ACCENT],
clean minimalist design, easy to scan",
  "aspect_ratio": "9:16",
  "model_id": "gemini-pro-image"
}
```

### Process/Workflow Diagram
```json
{
  "prompt": "Step-by-step workflow diagram showing [PROCESS]:
5 connected steps flowing left to right,
Step 1: [STEP], Step 2: [STEP], Step 3: [STEP], Step 4: [STEP], Step 5: [STEP],
clean icons for each step, numbered circles,
[COLOR] and white color scheme, professional business style",
  "aspect_ratio": "16:9",
  "model_id": "gemini-pro-image"
}
```

### Comparison Chart
```json
{
  "prompt": "Side-by-side comparison infographic:
[ITEM1] vs [ITEM2],
Feature rows: [FEATURE1], [FEATURE2], [FEATURE3], [FEATURE4],
checkmarks for positive, X for negative,
clean professional layout, easy to read",
  "aspect_ratio": "4:3",
  "model_id": "gemini-pro-image"
}
```

### Retro Infographic
```json
{
  "prompt": "Make a retro, 1950s-style infographic about the history of [TOPIC].
Include distinct sections for '[SECTION1],' '[SECTION2],' and '[SECTION3].'
Ensure all text is legible and stylized to match the period.
Use vintage color palette with cream, teal, and coral accents.",
  "aspect_ratio": "9:16",
  "model_id": "gemini-pro-image"
}
```

### Technical Blueprint
```json
{
  "prompt": "Create an orthographic blueprint that describes this [OBJECT/BUILDING]
in plan, elevation, and section views.
Label the '[VIEW1]' and '[VIEW2]' clearly in technical architectural font.
Use blue line on white paper aesthetic, precise technical drawing style.",
  "aspect_ratio": "16:9",
  "model_id": "gemini-pro-image"
}
```

### Whiteboard Summary (Educational)
```json
{
  "prompt": "Summarize the concept of '[TECHNICAL CONCEPT]' as a hand-drawn
whiteboard diagram suitable for a university lecture.
Use different colored markers for [COMPONENT1] and [COMPONENT2] blocks,
and include legible labels for key terms.
Authentic whiteboard aesthetic with marker texture.",
  "aspect_ratio": "16:9",
  "model_id": "gemini-pro-image"
}
```

---

## Character Design

### Character Design Sheet
```json
{
  "prompt": "Character design reference sheet for [CHARACTER NAME]:
Main full-body pose center, face close-up top right,
action pose bottom right, color palette swatches in corner,
[ART STYLE] style, detailed costume design,
Character: [DETAILED DESCRIPTION]",
  "aspect_ratio": "16:9"
}
```

### Character Portrait
```json
{
  "prompt": "Portrait of [CHARACTER DESCRIPTION],
[EXPRESSION] expression, looking [DIRECTION],
[LIGHTING: dramatic/soft/rim] lighting,
[ART STYLE: anime/realistic/cartoon/painterly] style,
detailed face and eyes, [BACKGROUND]",
  "aspect_ratio": "portrait"
}
```

### Multi-Character Scene (with saved characters)
```json
{
  "character_names": ["Character1", "Character2"],
  "scene_prompt": "[ACTION/INTERACTION] in [SETTING],
[MOOD] atmosphere, [TIME OF DAY],
both characters clearly visible and recognizable",
  "aspect_ratio": "landscape"
}
```

---

## Children's Content

### Children's Book Illustration
```json
{
  "prompt": "Children's book illustration:
[SCENE DESCRIPTION] in [SETTING],
[CHARACTER] doing [ACTION],
[ART STYLE: watercolor/digital/collage] style,
bright cheerful colors, friendly welcoming mood,
age-appropriate for [AGE] years",
  "aspect_ratio": "4:3"
}
```

### Educational Illustration
```json
{
  "prompt": "Educational illustration teaching [CONCEPT]:
Clear visual explanation of [TOPIC],
labeled diagram style, friendly cartoon characters,
bright colors, engaging for children,
simple and easy to understand",
  "aspect_ratio": "16:9"
}
```

---

## Architecture & Real Estate

### Architectural Visualization
```json
{
  "prompt": "Photorealistic architectural rendering of [BUILDING TYPE]:
[STYLE: modern/traditional/industrial] design,
[VIEW: exterior front/aerial/interior],
[TIME: morning/sunset/night] lighting,
surrounding landscape, people for scale,
professional architectural visualization quality",
  "aspect_ratio": "16:9",
  "model_id": "gemini-pro-image"
}
```

### Interior Design
```json
{
  "prompt": "[ROOM TYPE: living room/bedroom/kitchen] interior design:
[STYLE: minimalist/scandinavian/industrial/luxury] aesthetic,
[MATERIALS: wood/marble/concrete] surfaces,
natural light from large windows, plants for warmth,
professional interior photography, inviting atmosphere",
  "aspect_ratio": "16:9"
}
```

---

## Food & Restaurant

### Food Photography
```json
{
  "prompt": "Professional food photography of [DISH]:
[PLATING STYLE] presentation on [PLATE/SURFACE],
[LIGHTING: natural/studio] light from [DIRECTION],
shallow depth of field, steam/condensation if appropriate,
appetizing and delicious looking, editorial quality",
  "aspect_ratio": "square"
}
```

### Menu Design
```json
{
  "prompt": "Restaurant menu design for [RESTAURANT NAME]:
[CUISINE TYPE] menu layout,
'[DISH NAME]' with price in elegant typography,
food photography style images,
[COLOR SCHEME] colors, [STYLE] design aesthetic",
  "aspect_ratio": "3:4",
  "model_id": "gemini-pro-image"
}
```

---

## UI/UX Design

### App Screen Mockup
```json
{
  "prompt": "Mobile app UI mockup for [APP TYPE]:
[SCREEN: login/dashboard/profile/feed] screen,
[STYLE: iOS/Material/custom] design system,
[COLOR] primary color, clean modern typography,
intuitive layout, [DEVICE: iPhone/Android] frame",
  "aspect_ratio": "9:16"
}
```

### Website Hero Section
```json
{
  "prompt": "Website hero section design:
Bold headline area, clear call-to-action button,
[BACKGROUND: gradient/image/abstract] background,
modern SaaS/startup aesthetic,
clean whitespace, professional web design",
  "aspect_ratio": "16:9"
}
```

---

## Editing Workflows

### Photo Enhancement
```json
{
  "image_id": [ID],
  "prompt": "Enhance this image: improve lighting,
add subtle color grading for [MOOD] mood,
sharpen details, keep natural looking"
}
```

### Background Change
```json
{
  "image_id": [ID],
  "prompt": "Replace the background with [NEW BACKGROUND],
maintain subject lighting consistency,
seamless natural blend"
}
```

### Style Transfer
```json
{
  "image_id": [ID],
  "prompt": "Transform this image into [STYLE] style,
maintain subject recognizability,
[STYLE DETAILS]"
}
```

---

## Batch Generation Tips

### Consistent Series
For a series of images with consistent style:

1. **Create a style preset first:**
```json
{
  "name": "Campaign Style",
  "prompt": "professional studio lighting, brand colors, clean composition",
  "system_instruction": "Consistent corporate branding",
  "aspect_ratio": "square"
}
```

2. **Generate with preset:**
```json
{
  "prompt": "[Subject-specific prompt]",
  "style_preset_name": "Campaign Style"
}
```

### A/B Testing Variations
Generate multiple options:
```json
{
  "prompt": "[Your prompt]",
  "n": 4
}
```
Then use `continue_editing` on the best result.

---

## Storyboarding & Sequential Art (Pro Model)

### Multi-Scene Story
```json
{
  "prompt": "Create an addictively intriguing 9-part story with 9 images
featuring [CHARACTER DESCRIPTION] in an award-winning [GENRE] commercial.
The story should have emotional highs and lows, ending on [ENDING DESCRIPTION].
The identity of the character and their attire must stay consistent throughout
but they can and should be seen from different angles and distances.
Please generate images one at a time.
Make sure every image is in a 16:9 landscape format.",
  "aspect_ratio": "16:9",
  "model_id": "gemini-pro-image"
}
```

### Character Consistency Series (with saved character)
```json
{
  "character_name": "[SAVED_CHARACTER]",
  "scene_prompt": "Create a 10-part story with [CHARACTER] going on [ADVENTURE].
The story is thrilling throughout with emotional highs and lows
and ends in a happy moment.
Keep the attire and identity consistent, but vary expressions and angles
throughout all 10 images.
Generate one scene at a time.",
  "aspect_ratio": "16:9"
}
```

### Movie Concept Art
```json
{
  "prompt": "Cinematic concept art for a [GENRE] film:
[SCENE DESCRIPTION]
Style: Award-winning Hollywood pre-production art
Mood: [MOOD]
Lighting: Dramatic cinematic lighting with [SPECIFIC LIGHTING]
Include subtle environmental storytelling details.",
  "aspect_ratio": "21:9",
  "model_id": "gemini-pro-image"
}
```

---

## Structural Control & Layout Guidance (Pro Model)

*Use reference images to control composition precisely*

### Sketch to Final Ad
```json
{
  "prompt": "Create an advertisement for [PRODUCT] following this sketch exactly.
Transform the rough layout into a polished, professional design.
Maintain the exact positioning of elements from the reference.
Use [BRAND COLORS] color scheme, premium luxury feel.",
  "aspect_ratio": "16:9",
  "model_id": "gemini-pro-image",
  "reference_images": ["[sketch.jpg]"]
}
```

### UI Mockup from Wireframe
```json
{
  "prompt": "Create a high-fidelity mobile app mockup following these wireframe guidelines.
Transform the wireframe into a polished [STYLE] design.
Primary color: [COLOR], modern typography.
Add realistic content and polish while maintaining the exact layout.",
  "aspect_ratio": "9:16",
  "model_id": "gemini-pro-image",
  "reference_images": ["[wireframe.png]"]
}
```

### Pixel Art / Sprite Sheet
```json
{
  "prompt": "Generate a pixel art sprite of [CHARACTER] that fits perfectly
into this 64x64 grid image. Use high contrast colors.
Follow the grid structure exactly for easy extraction.",
  "aspect_ratio": "square",
  "reference_images": ["[grid.png]"]
}
```

### Animation Sprite Sheet
```json
{
  "prompt": "Sprite sheet of [CHARACTER] doing [ACTION], 3x3 grid,
sequence, frame by frame animation, square aspect ratio.
Follow the structure of the attached reference image exactly.
Each cell shows one animation frame, consistent character design throughout.",
  "aspect_ratio": "square",
  "reference_images": ["[grid_template.png]"]
}
```

---

## 2D ↔ 3D Translation (Pro Model)

### Floor Plan to 3D Interior Design Board
```json
{
  "prompt": "Based on the uploaded 2D floor plan, generate a professional
interior design presentation board in a single image.
Layout: A collage with one large main image at the top (wide-angle perspective
of the living area), and three smaller images below (Master Bedroom, Home Office,
and a 3D top-down floor plan).
Style: Apply a [DESIGN STYLE] style with [MATERIALS] across ALL images.
Quality: Photorealistic rendering, soft natural lighting.",
  "aspect_ratio": "3:4",
  "model_id": "gemini-pro-image",
  "reference_images": ["[floor_plan.png]"]
}
```

### 2D Art to 3D Render
```json
{
  "prompt": "Turn this 2D [ART/MEME/ILLUSTRATION] into a photorealistic 3D render.
Keep the composition identical but make all elements three-dimensional.
[SPECIFIC ELEMENT] should look like [MATERIAL].
Maintain the original humor/emotion while adding dimensional depth.",
  "aspect_ratio": "square",
  "model_id": "gemini-pro-image",
  "reference_images": ["[2d_source.png]"]
}
```

### Technical Drawing to 3D Visualization
```json
{
  "prompt": "Transform this technical 2D drawing into a photorealistic 3D product visualization.
Maintain exact proportions and dimensions from the schematic.
Add realistic materials: [MATERIALS]
Professional product photography lighting, studio environment.",
  "aspect_ratio": "16:9",
  "model_id": "gemini-pro-image",
  "reference_images": ["[technical_drawing.png]"]
}
```

---

## Advanced Editing Techniques

### Object Removal & In-painting
```json
{
  "image_id": [ID],
  "prompt": "Remove the [UNWANTED ELEMENT] from the background of this photo
and fill the space with logical textures that match the surrounding environment.
Seamless integration, no visible editing artifacts."
}
```

### Colorization (Manga/B&W Photos)
```json
{
  "image_id": [ID],
  "prompt": "Colorize this [manga panel/black and white photo].
Use a vibrant [STYLE] palette.
Ensure the lighting effects on [ELEMENTS] are [COLOR DESCRIPTION].
Maintain consistency with [REFERENCE if applicable]."
}
```

### Localization (Text Translation + Cultural Adaptation)
```json
{
  "image_id": [ID],
  "prompt": "Take this concept and localize it to a [TARGET LOCALE] setting,
including translating the tagline into [LANGUAGE].
Change the background to [NEW SETTING].
Adapt cultural elements appropriately while maintaining brand consistency."
}
```

### Seasonal/Lighting Control
```json
{
  "image_id": [ID],
  "prompt": "Turn this scene into [SEASON/TIME OF DAY].
Keep the [MAIN SUBJECT] exactly the same,
but add [SEASONAL ELEMENTS] and change the lighting to [LIGHTING DESCRIPTION]."
}
```

---

## Upscaling & Restoration (Pro Model)

### High-Fidelity Upscaling
*Works with images as small as 150x150 → outputs up to 4K*

```json
{
  "prompt": "Upscale to 4K",
  "reference_images": ["[small_image.png]"],
  "model_id": "gemini-pro-image",
  "notes": "Set resolution parameter to 2K or 4K. Model intelligently enhances details."
}
```

**Tips for upscaling:**
- Keep prompt simple: "Upscale to 4K" is often enough
- Add "sharp, detailed" for extra clarity
- Works best with clean source images

### Photo Restoration
*Repair damaged, faded, or old photographs*

```json
{
  "prompt": "Restore this old photograph to pristine condition.
Fix any tears, scratches, fading, or damage.
Preserve the original character and era of the photograph.
Color correct if needed, but maintain authenticity.",
  "reference_images": ["[damaged_photo.jpg]"],
  "model_id": "gemini-pro-image"
}
```

### Colorization + Restoration
```json
{
  "prompt": "Restore and colorize this vintage black and white photograph.
Fix any damage, then add realistic, historically accurate colors.
Maintain the era-appropriate atmosphere and details.",
  "reference_images": ["[vintage_bw.jpg]"],
  "model_id": "gemini-pro-image"
}
```

---

## 360 Turnaround & Multiple Angles

### Character 360 Turnaround
*Generate multiple angles from a single reference*

```json
{
  "prompt": "A 360 turnaround view in 4 different angles, full body pose",
  "reference_images": ["[single_character.png]"],
  "aspect_ratio": "3:2",
  "notes": "Great for building character references from a single image."
}
```

### Profile View from Front
```json
{
  "prompt": "A closeup photo of her looking right in profile view",
  "reference_images": ["[front_facing.png]"],
  "aspect_ratio": "square"
}
```

### Character Reference Sheet
```json
{
  "prompt": "Character reference sheet showing front, side, and back views.
Professional character design layout.
Consistent lighting across all views.
Clean background, easy to cut out.",
  "reference_images": ["[character.png]"],
  "aspect_ratio": "16:9"
}
```

---

## Visual Puzzle Solving & Reasoning

### Crossword/Puzzle Solving
*Model can solve visual puzzles*

```json
{
  "prompt": "Return an image of the solved crossword, use green pen",
  "reference_images": ["[crossword.png]"],
  "notes": "Model attempts to solve and fill in the puzzle."
}
```

### Image Interpretation with Overlay
```json
{
  "prompt": "Analyze this image and overlay informative labels
explaining the key elements. Add arrows pointing to important features.
Educational infographic style.",
  "reference_images": ["[complex_image.jpg]"],
  "model_id": "gemini-pro-image"
}
```

### What Happens Next
```json
{
  "prompt": "Based on this image, show what will happen next.
Keep the scene consistent but advance the action/story.
Same characters, same setting, next moment in time.",
  "reference_images": ["[scene.jpg]"]
}
```

---

## Multi-Purpose Reference Images

*Reference images can do more than character consistency*

### Object Placement
```json
{
  "prompt": "Place this product (Image 1) into a modern home office scene.
Position it prominently on the desk.
Natural lighting, lifestyle photography style.",
  "reference_images": ["[product.png]"],
  "notes": "Model places novel objects the model doesn't otherwise know about."
}
```

### Style Transfer
```json
{
  "prompt": "Create a new portrait in the exact artistic style of Image 1.
Match the brushwork, color palette, and technique precisely.
Subject: [NEW SUBJECT DESCRIPTION]",
  "reference_images": ["[style_reference.jpg]"]
}
```

### Branding Integration
```json
{
  "prompt": "Put this logo (Image 1) on a high-end ad for [PRODUCT].
The logo should be perfectly integrated into the design.
Embossed on the product surface, premium look.",
  "reference_images": ["[logo.png]"],
  "notes": "Great for brand consistency across marketing materials."
}
```

### Color Palette Extraction
```json
{
  "prompt": "Create a [SCENE DESCRIPTION] using the color scheme from Image 1.
Match the exact color palette, tones, and mood.
Apply these colors naturally throughout the new scene.",
  "reference_images": ["[color_reference.jpg]"]
}
```

### Combined References
```json
{
  "prompt": "Put the logo from Image 1 onto the device in Image 2.
Place this combination in a scene styled like Image 3.
Use the color palette from Image 4.
Overall: a professional ad for the product.",
  "reference_images": ["[logo.png]", "[device.png]", "[ad_style.jpg]", "[colors.jpg]"],
  "model_id": "gemini-pro-image",
  "notes": "Pro model handles up to 14 references (6 with high fidelity)."
}
