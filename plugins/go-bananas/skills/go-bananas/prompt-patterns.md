# Prompt Engineering Patterns

Best practices for writing effective image generation prompts.

---

## Golden Rules of Pro Prompting

*From Google DeepMind's official Gemini Pro guide*

Gemini Pro is a "Thinking" model. It doesn't just match keywords; it understands intent, physics, and composition. Stop using "tag soups" and start acting like a **Creative Director**.

### 1. Edit, Don't Re-roll

If an image is 80% correct, **do not generate a new one from scratch**. Use `continue_editing` to request specific changes.

```
"That's great, but change the lighting to sunset and make the text neon blue."
"Add more dramatic clouds in the background."
"Make her expression more confident."
```

### 2. Use Natural Language & Full Sentences

Talk to the model as if briefing a human artist. Use proper grammar and descriptive adjectives.

| Bad (Tag Soup) | Good (Natural Language) |
|----------------|-------------------------|
| `dog, park, 4k, realistic` | "A golden retriever playing fetch in a sunny park, captured in photorealistic detail" |
| `cool car, neon, city, night, 8k` | "A cinematic wide shot of a futuristic sports car speeding through a rainy Tokyo street at night. The neon signs reflect off the wet pavement and the car's metallic chassis." |
| `woman, portrait, professional` | "A sophisticated elderly woman wearing a vintage Chanel-style suit, photographed with soft natural light" |

### 3. Be Specific About Materiality

Describe textures and materials explicitly. The model understands physical properties.

| Material Term | Effect |
|---------------|--------|
| matte finish | Non-reflective, soft appearance |
| brushed steel | Subtle linear texture, industrial |
| soft velvet | Rich, light-absorbing fabric |
| crumpled paper | Organic texture, handmade feel |
| weathered leather | Aged, character-rich surface |
| polished marble | Reflective, luxurious stone |

### 4. Provide Context (The "Why" or "For Whom")

Because the model "thinks," giving it context helps it make logical artistic decisions.

| Prompt | What the Model Infers |
|--------|----------------------|
| "Create an image of a sandwich for a Brazilian high-end gourmet cookbook" | Professional plating, shallow depth of field, perfect lighting, appetizing presentation |
| "Design a logo for a children's educational app" | Friendly colors, rounded shapes, playful typography |
| "Product shot for a luxury watch brand's print campaign" | High-end lighting, reflections, premium composition |

### 5. Identity Locking (For Character Consistency)

When using reference images, explicitly state the consistency requirement:

```
"Keep the person's facial features exactly the same as Image 1."
"Maintain the character's identity and attire from the reference."
"The subject should be identical to the reference, only change the pose."
```

---

## Prompt Structure

### Basic Formula
```
[Subject] + [Action/Pose] + [Setting] + [Style] + [Technical Details]
```

### Example
```
"A confident business executive (subject)
standing with arms crossed (pose)
in a modern glass office (setting)
professional photography style (style)
shot on 85mm lens, soft natural light (technical)"
```

---

## Subject Description

### Be Specific About:
- **People**: Age, build, hair, clothing, expression
- **Objects**: Material, color, size, condition
- **Animals**: Breed, size, posture, fur/feathers

### Good Examples:
```
"30-year-old woman with curly red hair, wearing a navy blazer"
"Vintage brass telescope with weathered patina, ornate engravings"
"Golden retriever puppy with fluffy coat, sitting attentively"
```

### Avoid:
```
"A person" (too vague)
"Some kind of animal" (unclear)
"A thing on a table" (ambiguous)
```

---

## Composition Terms

### Camera Angles
| Term | Effect |
|------|--------|
| eye level | Natural, neutral |
| low angle / hero shot | Power, drama |
| high angle / bird's eye | Context, vulnerability |
| dutch angle | Tension, unease |
| over-the-shoulder | Conversation POV |
| extreme close-up | Detail, emotion |

### Framing
| Term | Shows |
|------|-------|
| wide shot | Full scene, context |
| medium shot | Waist up |
| close-up | Face/head |
| extreme close-up | Feature detail |

### Examples:
```
"low angle hero shot of a skyscraper against blue sky"
"extreme close-up of an eye reflecting city lights"
"wide establishing shot of a mountain village at dawn"
```

---

## Lighting Patterns

### Natural Light
| Term | Effect |
|------|--------|
| soft natural daylight | Clean, approachable |
| golden hour | Warm, romantic, nostalgic |
| blue hour | Cool, mysterious, twilight |
| overcast | Soft, even, no harsh shadows |
| backlit / silhouette | Drama, mystery |

### Studio Lighting
| Term | Effect |
|------|--------|
| Rembrandt lighting | Classic portrait drama (triangle on cheek) |
| rim lighting | Subject separation, drama |
| split lighting | Half shadow, moody |
| high key | Bright, optimistic, clean |
| low key | Dark, dramatic, cinematic |

### Creative Lighting
| Term | Effect |
|------|--------|
| neon lighting | Cyberpunk, modern |
| candlelight | Warm, intimate |
| volumetric fog | Atmospheric depth |
| lens flare | Cinematic, dreamy |

### Examples:
```
"portrait with Rembrandt lighting, soft shadows"
"neon-lit cyberpunk street scene, rain reflections"
"golden hour beach, warm backlit silhouettes"
```

---

## Lens Simulation

| Lens | Effect | Best For |
|------|--------|----------|
| 24mm wide angle | Expansive, slight distortion | Landscapes, architecture |
| 35mm | Natural, documentary feel | Street, environmental |
| 50mm | Human eye perspective | General purpose |
| 85mm f/1.4 | Flattering, background compression | Portraits |
| 135mm telephoto | Intimate, isolated subject | Portraits, wildlife |
| macro lens | Extreme detail | Small objects, textures |
| tilt-shift | Miniature effect | Architecture, cityscapes |

### Examples:
```
"shot on 85mm f/1.4, shallow depth of field, creamy bokeh"
"wide angle 24mm architectural shot, dramatic perspective"
"macro photography of dewdrops on a leaf, extreme detail"
```

---

## Depth of Field

| Effect | Prompt Language |
|--------|-----------------|
| Shallow | "shallow depth of field", "background blur", "bokeh" |
| Deep | "everything in focus", "deep focus", "sharp throughout" |
| Selective | "focus on [subject], blur background" |
| Bokeh | "creamy bokeh", "circular bokeh lights" |

### Examples:
```
"portrait with shallow depth of field, subject sharp, background soft bokeh"
"landscape photography, deep focus, everything sharp from foreground to horizon"
```

---

## Color & Mood

### Color Grading
| Term | Look |
|------|------|
| cinematic color grading | Film-like, processed |
| teal and orange | Hollywood blockbuster |
| muted pastels | Soft, artistic |
| high contrast black and white | Dramatic monochrome |
| vintage film | Nostalgic, warm |
| desaturated | Moody, serious |

### Mood Words
| Mood | Terms to Use |
|------|-------------|
| Happy | bright, vibrant, colorful, cheerful |
| Mysterious | dark, shadowy, foggy, ethereal |
| Romantic | soft, warm, dreamy, golden |
| Dramatic | high contrast, bold, intense |
| Peaceful | serene, calm, gentle, soft |
| Energetic | dynamic, bold, vivid, electric |

### Examples:
```
"cinematic color grading, teal and orange palette"
"moody atmosphere, desaturated colors, fog"
"vibrant and cheerful, bright saturated colors"
```

---

## Style References

### Photography Styles
```
"professional product photography"
"editorial fashion photography"
"documentary street photography"
"fine art photography"
"lifestyle photography"
```

### Art Styles
```
"watercolor painting style"
"oil painting, impressionist"
"digital illustration"
"concept art"
"comic book style"
"anime style"
"photorealistic 3D render"
```

### Specific Aesthetics
```
"minimalist design"
"maximalist, busy, detailed"
"retro 80s aesthetic"
"Art Deco style"
"Scandinavian design"
```

---

## Text in Images (Pro Model)

### Best Practices:
1. **Quote the text**: Put exact words in quotes
2. **Specify position**: top, bottom, center, left, right
3. **Describe font style**: serif, sans-serif, bold, italic
4. **Keep it simple**: Fewer words = better rendering

### Examples:
```
"Movie poster with bold title 'STELLAR DRIFT' in sans-serif at top center,
tagline 'Beyond the stars' in smaller italic below"

"Coffee shop menu board with 'ESPRESSO $3' in chalk lettering style"

"Infographic with headline 'CLIMATE FACTS' in bold blue,
statistics displayed in clean modern typography"
```

---

## Negative Prompting

Tell the model what NOT to include. Negative prompts are an easy way to stop the model from repeatedly making the same wrong choices.

### Common Negatives:

| Negative | Why It Helps |
|----------|--------------|
| `no date stamp` | Go Bananas! Pro likes to add date stamps to corners |
| `no text` | When you want a clean image without any text |
| `not rustic` | Model tends to age things, make them look weathered |
| `no monkeys` | Specific to banana-themed prompts (model associates them) |
| `no watermarks` | Prevents generated watermarks |
| `not blurry` | Ensures sharp output |

### Usage Examples:
```
"A screenshot of the Google home page with a banana Google doodle. No monkeys."

"A modern minimalist kitchen interior. Not rustic, no wood grain, no farmhouse style."

"Product shot of a smartphone. No text, no date stamp, no reflections on screen."
```

### General Pattern:
- Be direct: "No X" or "Without X"
- Place at end of prompt
- Can combine multiple negatives
- Use when model repeatedly adds unwanted elements

### Common Negative Prompts:
```
"blurry, low quality, distorted"
"text, watermarks, logos"
"extra limbs, deformed"
"oversaturated, unrealistic colors"
"cluttered background"
```

---

## Real-camera realism

To make a picture read as a capture from a real camera, write the prompt camera-first and keep the flaws a real device makes. No photography vocabulary is needed from the user.

1. **Order**: one sentence each, in this order: Camera, subject and action, Lighting, Texture. Open with a kind of device and how it was held (a single-use film camera with its flash, a phone front camera at arm's length, a paused frame from a home camcorder, a fixed security camera high in a corner, a fisheye lens at knee height).
2. **Device flaws**: add the ones that suit the scene: direct-flash falloff, grain or sensor noise, compression, a crooked or off-center frame, motion blur, clipped highlights.
3. **No boosters**: leave out "8K", "4K", "masterpiece", "best quality", "ultra detailed" and "hyperrealistic". They push toward a polished render.
4. **negative_prompt**: studio lighting, shallow depth of field, cinematic color grade, airbrushed skin, on-screen text, timestamps.
5. **Characters**: pass a saved character as `reference_assets: [{ role: "character", character_id: 5 }]` and keep the camera sentence first. The server appends the identity lock at the end of the prompt.
6. **Save the look**: put the camera, lighting and texture sentences in `create_style_preset` (negative as `negative_prompt`), then reuse it with `style_preset_name`.
7. **Contrast ladder**: push an idea one rung at a time with the same camera look: the expected scene, a specific situation, a personality, then a contrast with its opposite (a grandmother, at a skate park, fearless mid-kickflip, in a lace dress and pearls).
8. **Stills for video**: generate at the video's aspect ratio (16:9 or 9:16), then give the video prompt one short camera-movement paragraph (handheld tremor, crash zoom with focus loss, whip pan with overshoot, autofocus hunting) and change only the scene.

```
A snapshot from a single-use film camera held at chest height, the frame slightly crooked. A bike courier in a yellow raincoat laughs mid-jump over a puddle beside a bus shelter. The built-in flash fires straight at them and the wet street behind falls into darkness. Coarse grain, faded colors and soft corners from the plastic lens.
```

```
A paused frame from a home camcorder on a tripod set too low, tilted a few degrees. A toddler in a paper crown reaches for a birthday cake while two relatives lean in from the edges. Warm overhead room light with a blown-out window behind. Soft interlaced smear, muted color and compression blocks in the dark areas.
```

### Built-in camera looks (`camera_look`)

The quickest route: pass `camera_look` to `generate_image` (also `start_generation`, `generate_with_subscription`, and `batch_generate` per item or in `defaults`) and Go Bananas writes the camera sentence before the prompt and the lighting and texture sentences after it. The look's negative prompt and ratio apply only when nothing else sets them. Not with `enhance_prompt` or `image_to_edit_id`.

Describe only the subject, action and setting; the look supplies the camera, lens and light, so leave out your own camera or lighting words such as 85mm, golden hour or cinematic. They compete with the look and can override it.

| Look | Ratio | What it gives |
|------|-------|---------------|
| `disposable-flash` | 1:1 | single-use film camera, direct flash, black background, warm grain |
| `skate-fisheye` | 1:1 | fisheye lens at knee height, curved horizon, on-camera flash |
| `old-phone-selfie` | 9:16 | 2010s phone front camera held at arm's length; best with people, since with an animal or object it adds a person holding the phone |
| `camcorder-still` | 16:9 | paused frame from an early-2000s home video; a good video first frame |
| `point-and-shoot-night` | 16:9 | compact digital camera at night, tilted horizon, flash, JPEG noise |
| `street-candid-35mm` | 16:9 | unposed daylight shot on 35mm film from a few metres away |
| `security-camera` | 16:9 | fixed high-corner CCTV still, near-monochrome |
| `instant-film` | 1:1 | square instant-camera snapshot, soft focus, faded colour |

```json
{ "prompt": "A bike courier in a yellow raincoat laughs mid-jump over a puddle beside a bus shelter.", "camera_look": "disposable-flash" }
```

---

## Complete Prompt Examples

### Professional Headshot
```
Prompt: "Professional corporate headshot of a confident 35-year-old
woman with shoulder-length dark hair, wearing a charcoal blazer,
subtle smile, modern office background with soft blur,
shot on 85mm f/1.4, soft natural window light from the left,
clean professional color grading"

Negative: "harsh shadows, over-edited, unnatural skin"
```

### Product Photography
```
Prompt: "Luxury watch on polished marble surface, dramatic lighting
with subtle reflections, 45-degree angle, shallow depth of field,
dark moody background, professional product photography,
high-end advertising style"

Negative: "busy background, flat lighting, low quality"
```

### Fantasy Scene
```
Prompt: "Epic fantasy castle on a cliff at sunset, golden hour
lighting, volumetric clouds, dramatic sky with purple and orange,
birds flying in distance, detailed stone architecture,
cinematic wide shot, concept art style"

Negative: "modern elements, people, text"
```

---

## JSON Prompting (Structured Prompts)

For complex scenes with multiple elements, JSON-structured prompts give you precise control over composition, style, and technical parameters.

### When to Use JSON Prompting:
- Complex multi-element scenes
- Precise composition control
- Reproducible results
- Technical specifications needed

### Basic JSON Template:
```json
{
  "promptDetails": {
    "description": "A prompt to create a specific scene",
    "styleTags": ["Lifestyle Photography", "Cozy", "Golden Hour"]
  },
  "scene": {
    "background": {
      "setting": "a busy but cozy coffee shop corner on a sunny morning",
      "details": "Soft morning sunlight, wooden table texture, steam rising from a latte"
    },
    "subject": {
      "description": "A person looking relaxed and content",
      "pose": "sitting at a wooden table, holding a ceramic mug",
      "focus": "Subject is clearly defined against the warm, blurred interior"
    }
  },
  "technicalStyle": {
    "aspectRatio": "4:5",
    "photographyStyle": "Lifestyle, High-Key, Commercial",
    "camera": {
      "shotType": "Medium Shot",
      "angle": "Slightly above eye-level",
      "depthOfField": "Moderate, keeping subject sharp, blurring background"
    },
    "lighting": {
      "type": "Natural, Warm, Diffused",
      "description": "Sunlight acts as key light from window side"
    },
    "color": {
      "palette": "Earth tones, warm browns, creams, soft greens"
    }
  }
}
```

### Full Example with Overlay:
```json
{
  "promptDetails": {
    "description": "Create a scene placing a subject into a warm environment with a music interface overlay",
    "styleTags": ["Lifestyle Photography", "Cozy", "Instagram Aesthetic"]
  },
  "scene": {
    "background": {
      "setting": "a busy but cozy coffee shop corner on a sunny morning",
      "details": "Soft morning sunlight streaming through a large glass window, blurred pedestrians outside, wooden table texture, steam rising from a latte."
    },
    "subject": {
      "description": "The person defined by [UPLOADED IMAGE], looking relaxed and content.",
      "pose": "sitting at a wooden table, one hand holding a ceramic mug, looking thoughtfully out the window",
      "focus": "Subject is clearly defined against the warm, slightly blurred interior of the cafe."
    }
  },
  "overlayObject": {
    "type": "Minimalist 'Now Playing' Digital Widget",
    "relationshipToEnvironment": "the UI appears as a clean, flat graphic overlay hovering in the negative space near the window.",
    "transform": "Straight orientation, 2D graphic design style",
    "components": {
      "songTitle": "Sunday Morning",
      "artistName": "Maroon 5",
      "position": "floating in the bottom-right corner of the frame."
    }
  },
  "technicalStyle": {
    "aspectRatio": "4:5",
    "photographyStyle": "Lifestyle, High-Key, Commercial",
    "camera": {
      "shotType": "Medium Shot",
      "depthOfField": "Moderate, keeping the subject and table sharp"
    },
    "lighting": {
      "type": "Natural, Warm, Diffused",
      "description": "Sunlight acts as the key light coming from the window side"
    },
    "color": {
      "palette": "Earth tones, warm browns, creams, and soft greens."
    }
  },
  "moodReinforcement": "The scene feels like a relaxed start to the day, with the music track reflecting the easygoing vibe."
}
```

### Tips for JSON Prompting:
1. Not every field is required—use what you need
2. Model understands the structure and intent
3. Great for reproducible, precise outputs
4. Can be verbose—use for complex scenes only

---

## Prompt Evolution Pattern

Start with a simple prompt and iteratively refine. This helps you understand how the model interprets each addition.

### The Pattern:
```
Simple → Refined → Final
```

### Example Progression:

| Stage | Prompt | What You Learn |
|-------|--------|----------------|
| 1. Initial | "A fashion photo" | Default style, subject, setting |
| 2. Refined | "A high-end fashion photo" | How "high-end" affects quality |
| 3. Final | "A high end fashion photo outdoors, winter fashion shoot, daring and brave" | Full creative direction |

### Workflow:
1. **Start simple**: Generate with minimal prompt
2. **Observe defaults**: See what the model chooses
3. **Add one element**: Quality, setting, or mood
4. **Use continue_editing**: Don't regenerate—refine!
5. **Iterate**: Keep building until satisfied

### Benefits:
- Learn model's interpretation patterns
- Discover unexpected creative directions
- Build complex scenes gradually
- Understand which words have the most impact

### Example Session:
```
1. generate_image({ prompt: "A portrait" })
   → Model shows default style, lighting, subject

2. continue_editing({ prompt: "make it more dramatic" })
   → Adds contrast, shadows, intensity

3. continue_editing({ prompt: "add studio lighting, fashion editorial" })
   → Shifts to professional photography style

4. continue_editing({ prompt: "the subject should be wearing avant-garde fashion" })
   → Final creative direction applied
```

### Image Recreation Exercise

A great way to learn the model's patterns: try to recreate a real photograph using only words.

1. Find a photograph you like
2. Describe it in detail (without looking at it)
3. Generate from your description
4. Compare with original
5. Note what you missed or got wrong
6. Refine your description

This exercise builds intuition for effective prompting.
