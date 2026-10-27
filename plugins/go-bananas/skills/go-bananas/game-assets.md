# Game Assets

How to make game art that matches as a set: icons, props, sprites, level layers and textures. Fix the set's rules first, then change only the item from call to call. The same steps are in `get_help({ workflow: "game_assets" })`.

Method adapted from [scenario-labs/skills](https://github.com/scenario-labs/skills) (MIT), rewritten for Go Bananas tools.

Go Bananas makes the images. Background removal, pixel cleanup, cutting sprite sheets, animation and engine import happen outside it.

---

## 1. Fix the set's rules

Before the first image, write down:

- **The view:** side view facing right, top-down, or 2:1 isometric. Choose the grid before the style.
- **The light:** its direction and the shadow side, the same on every asset.
- **The palette** as hex values, and the outline weight.
- **The finish:** painted or pixel art.
- **The size** each asset will have in the engine.

Save the wording as a style preset and pass `style_preset_name` on every call:

```
create_style_preset({
  name: "Armoury Icons",
  prompt: "game inventory icon, hand-painted, bold dark outline, light from the top left, palette #2B2D42 #EF233C #8D99AE #EDF2F4",
  negative_prompt: "text, frame, scene, cast shadow on the ground"
})
```

## 2. One object, one wording, only the item changes

One object per image, centered on a plain flat background, with no scene. Keep every word the same between calls except the item ("wooden shield" becomes "iron sword").

Decide on cut-outs (section 3) before choosing a batch: batch items take no `background` or `output_format`, so a batch always comes back opaque. An opaque set of up to 8 in one batch:

```
batch_generate({
  items: [
    { prompt: "round wooden shield with an iron rim, centered, single object, plain flat background" },
    { prompt: "short iron sword with a leather grip, centered, single object, plain flat background" }
  ],
  defaults: { style_preset_name: "Armoury Icons", aspect_ratio: "1:1" }
})
```

`n` (1 to 4) on `generate_image` gives several takes of one item. Lettered assets (a sign, a button label) vary most between takes: make several and check the words letter by letter.

## 3. Cut-outs

| Model | How to get a cut-out |
|---|---|
| GPT Image 2.5 Flare and Sunburst (`openai-gpt-image-2.5-flare`, `openai-gpt-image-2.5-sunburst`) | `background: "transparent"` with `output_format` `"png"` or `"webp"` |
| Every other model | An opaque picture: ask for a flat pure magenta (#FF00FF) background and remove that colour outside Go Bananas |

```
generate_image({
  prompt: "round wooden shield with an iron rim, centered, single object",
  model_id: "openai-gpt-image-2.5-flare",
  background: "transparent",
  output_format: "png",
  aspect_ratio: "1:1",
  style_preset_name: "Armoury Icons"
})
```

- Moving to one of those two models changes the model and the cost, so ask the user first.
- A transparent set is one `generate_image` per item, one after another.
- Never write "transparent background" in the prompt. A model that can't make one may paint a checkerboard pattern instead.
- With a magenta key, keep pink, purple and magenta off the object ("no pink, purple or magenta anywhere on the character"), or those parts are removed with the background. Reject takes that show any.
- Check the edge of every cut-out on a coloured background: a soft glow or a light fringe shows as a halo in the game. Check see-through parts too (empty glass): one item can come out clear and the next solid.

## 4. Matching a style picture

Pass one approved asset as the style reference:

```
generate_image({
  prompt: "iron helmet with a nose guard, centered, single object, plain flat background",
  style_preset_name: "Armoury Icons",
  reference_assets: [{ role: "style", image_id: 57 }]
})
```

Prepare the reference first:

- **Crop it to the object** on a plain background, keeping it at least 512x512 (smaller references are refused). Empty padding reads as composition, so the size and position drift from run to run.
- **Remove baked effects** (drop shadow, outer glow, bevel). They read as part of the shape and come back thicker or doubled; add them in the engine instead.
- **One object per reference picture.** Several objects get merged into one.
- **Say which parts must stay fixed.** A nine-slice panel needs fixed corners and stretchable middles; the model doesn't know that unless the prompt says it, so check that it held.

Measure the set: put the outputs side by side at the same scale and compare their outlines and sizes, not only how they look.

## 5. Side-scroller characters

- A pure side profile facing right. Make one facing; the engine mirrors it for left.
- Feet on one line, with room above the head for a raised weapon or a jump.
- Two poses on one image with a wide gap between them (standing ready, mid-jump), so they can be cut apart.
- A pose sheet: four poses in a row (fall, wall cling, dash, attack), wide gaps, the same design and palette. Cut it apart by shape, not by equal columns, since poses overlap sideways.
- Animation cycles (run, walk, attack) need a video tool and frame extraction, which are outside this workflow.

## 6. Level layers

Parallax backgrounds are a stack of planes that scroll at different speeds.

- **One plane per call.** Never write "layers" or "parallax" in one prompt: that returns one picture of stacked layers.
- Keep the camera, horizon line, light direction and palette words the same in every plane, and change only what the plane holds.
- Say what each plane must not contain ("no ground, no sky"), so planes don't repeat each other.
- The far plane (sky) is opaque; nearer planes are cut-outs (section 3).
- Make planes wider than the screen so they can scroll: 21:9 on every model, 4:1 or 8:1 on Nano Banana 2.1.
- Per level, five pieces usually cover it: background, midground, a wall texture, a platform strip and props spaced well apart.
- **Platforms and ledges:** a left end, a middle piece that repeats and a right end, so every length keeps its carved ends. Never stretch one picture.
- Put the first level in the game with the hero on it before making the rest. Retro-size paintings that look fine alone can fail in play.

## 7. Textures

- Square (1:1), flat even light, no perspective and no baked shadows. For a larger square, `resolution_tier: "2k"` or `"4k"` on Nano Banana 2.1 and Pro.
- "Seamless" in a prompt is no guarantee. Tile the download 2x2 and look for seams before using it; compare the left edge with the right and the top with the bottom.
- A seam that shows: blend the opposite edges in an image editor, or generate again with flatter light.
- Go Bananas makes the colour map only, not normal, roughness or height maps.

## 8. Isometric sets

- Use one projection (2:1 diamond is common), one anchor point, one light direction and one shadow side for every tile.
- Tall things (trees, towers) stand on one tile but reach above it and can cover part of the tile behind. Leave them their full height rather than trimming them to the tile's diamond.
- Build a small map from the first few tiles and check the joins, paths and overlaps before making the rest.

## 9. Pixel art

Generated pixel art drifts off the grid and adds stray colours. Snap it to the grid and reduce it to the set palette in a pixel editor. Keep sprite files lossless (PNG); lossy formats shift the palette.

## Common mistakes

| Mistake | What happens |
|---|---|
| "Transparent background" in the prompt | An opaque picture, sometimes with a painted checkerboard |
| A new wording for each item | The set stops matching |
| A reference with a drop shadow | The shadow comes back as part of the object |
| All the layers in one prompt | One picture of layers, not separate planes |
| Trusting "seamless" | The texture shows a grid of seams in the game |
| Stretching a platform | The carved ends distort |
| Making every level before testing one | A problem is found after the whole set is paid for |
