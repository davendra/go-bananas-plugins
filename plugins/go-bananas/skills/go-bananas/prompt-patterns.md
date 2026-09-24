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
cinematic wide shot, concept art style, highly detailed"

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
