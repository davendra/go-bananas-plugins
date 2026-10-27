# Multiple References

How to use several reference pictures in one image so each one does its own job. The same steps are in `get_help({ workflow: "reference_mastery" })`.

Method adapted from [scenario-labs/skills](https://github.com/scenario-labs/skills) (MIT), rewritten for Go Bananas tools.

---

## One job per picture

Give every picture a role in `reference_assets` on `generate_image` (also `start_generation` and `POST /api/images`):

| Role | The picture gives |
|---|---|
| `character` | A person or creature to keep. Takes `scope`: `face`, `wardrobe` or `full` (default) |
| `product` | An object to keep exactly: design, colours, materials, proportions |
| `logo` | A mark to place on something |
| `style` | The look: medium, texture, palette, light |
| `composition` | The layout and framing, not the subject |
| `template` | A layout to follow closely, changing only what the prompt asks |

```
generate_image({
  prompt: "a can of sparkling water on a striped beach towel, midday sun",
  reference_assets: [
    { role: "logo", image_id: 88, notes: "gives the wave mark printed on the can" },
    { role: "product", product_id: 3 },
    { role: "style", image_id: 91, notes: "gives the gouache texture and the colour palette only" }
  ]
})
```

The server writes one line per entry ("Image 2 is a logo: …", "Images 3-5 show the character …"), so the prompt itself needs only the scene. A `character` or `product` entry sends its pictures and its name, not the saved `base_prompt` or `product_description`: put the key traits and any label text in the prompt.

## Notes: say what the picture gives

`notes` (up to 300 characters) narrows a role. Say what the picture gives, not what to avoid: "gives the brush texture and the palette only" rather than "don't copy the house". Image models follow a stated source more reliably than a prohibition. The server's own `scope` wording works the same way.

## Order and weight

- List first the picture the model is most likely to ignore: usually a small logo or the product. Check that one first.
- `weight` (0 to 1) changes only the wording: 0.75 or more asks for a strong match, 0.35 or less a light one. Lower a style picture's weight when it takes over the image.

## Leak check after every image

A leak is when a picture gives more than its job:

| Leak | Fix (one per run) |
|---|---|
| The style picture's subject appears (its house, its person) | Narrower `notes`, or lower its `weight` |
| The composition picture's people appear | Use a plainer composition picture (people small or absent), or lower its `weight` |
| The style picture's colours repaint the product | `notes` on the style: "gives the texture only"; the product keeps its own colours |
| The logo is ignored | Move it to the top of the list; describe where it goes in the prompt |
| Two characters merge | One `character` entry per person, named by side of the frame in the prompt |

Change one thing per run so you can see which change fixed the leak (see `refine-loop.md`).

## Limits

- Up to 14 reference pictures per request on Gemini and 16 on OpenAI. A `reference_assets` request over the limit is refused, never trimmed.
- Every `image_id`, `image_url` or `r2_key` picture must be at least 512x512.
- A saved character sends up to 4 pictures on standard models and 5 on Pro and the OpenAI models.
- `reference_assets` always runs on the workspace's own keys, never the ChatGPT subscription.

## Teaching a new object

To show the model an object it doesn't know, save it as a product (`create_product_reference`) or pass it with `role: "product"` and a note saying what it is: `notes: "a folding bike trailer; the hitch attaches at the rear axle"`.
