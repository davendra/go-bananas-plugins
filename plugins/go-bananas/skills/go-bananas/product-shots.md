# Product Shots

How to put a real product into new scenes without the model redrawing it. The same steps are in `get_help({ workflow: "product_marketing" })`.

Method adapted from [scenario-labs/skills](https://github.com/scenario-labs/skills) (MIT), rewritten for Go Bananas tools.

---

## Never generate the product

Start from a real photo of the product: front-on, label readable, plain background if possible, at least 512x512. A generated product has invented details (wrong cap, wrong label layout), and every later image copies them.

If the user has no photo, say so and ask for one. Don't generate a stand-in and treat it as the product.

## Save it with the label quoted

```
create_product_reference({
  product_name: "Northwind Cold Brew",
  product_url: "https://example.com/northwind-bottle.jpg",
  product_description: "a 500 ml amber glass bottle with a white label reading \"NORTHWIND\" over \"COLD BREW\" in black capitals, black screw cap"
})
```

`generate_with_product` writes the description into every prompt ("Using the provided reference image showing …") and adds its own instruction to keep the design, colours, materials and proportions exact. So:

- Quote all label text exactly, with its layout ("over", "beside", "in black capitals").
- Keep the description to the product. No setting, no light.
- Fix a wrong description with `update_product_reference`, not by adding corrections to each scene.

## One scene per call

```
generate_with_product({
  product_name: "Northwind Cold Brew",
  scene_prompt: "the bottle standing on a wet slate counter beside a halved lemon, soft morning window light from the left",
  additional_details: "a soft contact shadow under the bottle, falling to the right",
  aspect_ratio: "4:5"
})
```

- `scene_prompt` needs only the setting, the light and the camera. The tool already handles fidelity.
- Ask for a **contact shadow** and light from the same side as the scene. A product with no shadow looks pasted in.
- One scene per call. For several takes of the same scene, use `number_of_images` (1 to 4).
- For variations, change one thing per run (setting, light or angle) and keep the rest of `scene_prompt` word for word.

## Check every result

Compare each image with the product photo:

1. Label text, letter by letter, including small print.
2. Logo shape and position.
3. Colours, cap, material finish.
4. Proportions (a bottle that got taller or slimmer).

| Failure | Fix |
|---|---|
| One wrong letter or a smudged logo on a good image | `edit_image({ image_id, prompt: "change only the label text so it reads NORTHWIND; keep everything else the same" })` |
| Wrong shape or proportions | Generate again. Edits rarely fix shape |
| The scene took over the product (recoloured to match) | Shorten the scene's colour words, or say "the bottle keeps its own amber colour" in `additional_details` |

## Product with a character or a logo

`generate_with_product` takes only the product. To combine it with a saved character or a separate logo picture, use `generate_image` with typed references. They send the pictures and the name, not the saved `product_description` or `base_prompt`, so quote the label text and the character's key traits in `prompt`:

```
generate_image({
  prompt: "Hero Henry, a 10-year-old boy in a red cape and blue mask, holds the amber Northwind bottle up to the camera at a picnic table, its white label reading NORTHWIND over COLD BREW",
  reference_assets: [
    { role: "product", product_id: 3 },
    { role: "character", character_id: 5 }
  ]
})
```

See `multi-reference.md` for how to give each picture one job.
