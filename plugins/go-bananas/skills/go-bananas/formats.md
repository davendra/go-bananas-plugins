# Formats

How to turn one approved image into every size a campaign needs. Each format is derived from the approved master; prompting the idea again for each size gives several different pictures, not one picture in several sizes. The same steps are in `get_help({ workflow: "multi_format" })`.

Method adapted from [scenario-labs/skills](https://github.com/scenario-labs/skills) (MIT), rewritten for Go Bananas tools.

---

## 1. Approve one master

- Text-free, so every format can take its own words later.
- At the largest size available: `resolution_tier: "4k"` on Nano Banana 2.1 or Pro (Nano Banana 2 Lite is fixed at 1K; the OpenAI models take `size` instead).
- Every format comes from this master's `image_id`, never from another format. Each generative step redraws the picture, and a copy of a copy drifts.

## 2. Take the first step that fits

| The format needs | Step | In Go Bananas |
|---|---|---|
| The same ratio at other pixels | Resize | Outside Go Bananas: resize the download |
| A slightly tighter ratio, and the edges can go | Crop | Outside Go Bananas, in an editor, only if the subject clears the cut |
| A nearby ratio that needs more canvas | Extend | `edit_image` on the master with the new `aspect_ratio` |
| A far ratio (16:9 to 9:16, a wide banner) or a new layout | Recompose | `generate_image` with the master as a `style` reference |

Go Bananas has no resize, crop, upscale or text-overlay tool. Say so rather than pretending a generative step is one.

### Extend

```
edit_image({
  image_id: 42,
  prompt: "extend the scene outward to fill the new frame; keep the subject, its size and position, and everything already in the picture unchanged",
  aspect_ratio: "4:5"
})
```

- If the model returns another shape, Go Bananas center-crops it to the ratio. Check that the subject's head, hands and any product survived.
- `edit_image` takes no `resolution_tier`, so on the Gemini models an extended format comes back at about 1K, whatever the master's size. For a large format, recompose it instead.
- Use `edit_image` with the master's id, never `continue_editing`: it edits the last result of the session, which may be another format.

### Recompose

```
generate_image({
  prompt: "the same lighthouse keeper in a yellow coat on the rocks at dusk, composed for a tall frame with her in the middle band",
  aspect_ratio: "9:16",
  model_id: "gemini-nano-banana-2.1",
  resolution_tier: "2k",
  reference_assets: [{ role: "style", image_id: 42 }]
})
```

- The `style` role carries the palette, light and rendering but not the content, so restate the subject in `prompt`.
- When the subject must match exactly, add it as its own reference: `{ role: "character", character_id: 5 }` or `{ role: "product", product_id: 3 }`. Keep `model_id` in the call when you add a character reference: without it, a 1K request with a face can move to GPT Image 2, and a 2K or 4K one stays on the workspace default model, which may be Nano Banana 2 Lite (1K only, so the call is refused).
- Don't use the `composition` or `template` roles here: they copy the master's layout, which is what a new ratio has to change.
- Extending a wide picture into a tall one usually leaves the subject small or cut off. Recompose it.

## 3. Ratios and exact sizes

- 1:1, 2:3, 3:2, 3:4, 4:3, 4:5, 5:4, 9:16, 16:9 and 21:9 work on every model.
- 1:4, 4:1, 1:8 and 8:1 work only on Nano Banana 2.1 (`gemini-nano-banana-2.1`).
- For exact pixels, the OpenAI models take `size: "1280x720"`, but only when both sides are multiples of 16 and the total stays within the limits in the parameter's description. Most platform sizes miss that (1080x1920 does: 1080 is not a multiple of 16), so the usual route is to generate at the ratio in the "Generate at" column below and resize or crop the download.

## 4. Common placements

Delivery sizes as of 2026. Platforms change them: confirm any contractual size with the user. "Generate at" is the nearest ratio Go Bananas takes and, where 1K output is smaller than the delivery size, the `resolution_tier` to ask for (2k and 4k need Nano Banana 2.1 or Pro). Then crop or shrink the download to the final pixels; Go Bananas has no upscaler, so never generate smaller than the delivery size.

| Placement | Deliver | Generate at | Watch for |
|---|---|---|---|
| Instagram feed | 1080x1350 (4:5) or 1080x1080 | 4:5 or 1:1, 2k | The profile grid shows a square crop: keep the subject in the middle |
| Story, reel, TikTok | 1080x1920 (9:16) | 9:16, 2k | The safe zone below |
| YouTube thumbnail | 1280x720 (16:9) | 16:9 | Viewed small; the video length sits in the bottom right corner |
| YouTube channel banner | 2560x1440 (16:9) | 16:9, 2k | Phones show only a strip about 1546x423 across the middle |
| X post | 1200x675 (16:9) | 16:9 | |
| X header | 1500x500 (3:1) | 21:9, then crop | The profile picture sits over the lower left |
| LinkedIn post, link preview | 1200x630 (about 1.91:1) | 16:9, then crop | Shown small in feeds: large text only |
| Pinterest pin | 1000x1500 (2:3) | 2:3, 2k | Taller than 2:3 gets cut off in the feed |
| Avatar, server icon | 400x400 or more (1:1) | 1:1 | Displayed in a circle: keep the mark inside it |
| App icon | 1024x1024 (1:1) | 1:1 | Corners are rounded by the phone: keep the mark in the middle 80% |
| Podcast or music cover | 3000x3000 (1:1) | 1:1 at `resolution_tier: "4k"`, then resize | The title must still read when tiny |
| Website hero | Often 1920 wide, 500-800 tall | 21:9 or 4:1 (4:1 on Nano Banana 2.1), 2k, then crop | Measure the real slot; small screens crop the sides |
| Marketplace main image | 1600x1600 or more (1:1) | 1:1, 2k | Most marketplaces want the product alone on pure white, filling most of the frame, with no text |
| A4 print at 300 dpi | 2480x3508 | 3:4 or 2:3 at `resolution_tier: "4k"` (larger than A4 at 300 dpi), then crop | Add 3 mm bleed on each edge; keep text 5 mm inside the trim |

**9:16 safe zone:** stories, reels and TikTok draw their buttons and captions over the edges. Keep the subject, all text and any logo out of roughly the top 14%, the bottom 35% and 6-13% of each side; a picture that fits inside that band works on all three.

## 5. Words last

Every generative step redraws text, so text on the master would come back changed in each format. Add the words to each final format at its own size: in an editor, or quoted in that format's prompt and checked letter by letter.

## 6. Measure before delivering

```
get_image_info({ image_id: 57 })
```

Compare the returned width and height with the size asked for, and look at each format to see that the subject survived. Offer to file the set in one project; pass `project` only once the user names it.

## Common mistakes

| Mistake | What happens |
|---|---|
| Prompting the idea again for each format | Several different pictures, not one picture in several sizes |
| Deriving a format from another format | Each step adds drift |
| Extending 16:9 to 9:16 | The subject ends up small in a tall frame, or cut; recompose instead |
| Keeping text on the master | Every format redraws it differently |
| Trusting the ratio without measuring | A center-crop or a near-miss size goes out unnoticed |
