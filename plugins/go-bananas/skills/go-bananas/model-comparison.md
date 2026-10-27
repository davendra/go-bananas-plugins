# Model Comparison

How to compare models fairly: one brief, run unchanged on each model, judged against checks written before the first image. The same steps are in `get_help({ workflow: "model_comparison" })`.

Method adapted from [scenario-labs/skills](https://github.com/scenario-labs/skills) (MIT), rewritten for Go Bananas tools.

---

## 1. Ask the user first

Every model is a paid call, and the Gemini and OpenAI models bill different keys. Before anything runs, agree with the user:

- **which models:** `list_models` shows the ones this workspace may use;
- **how many images each;**
- **the estimated cost** of each, from one call per model at the quality you will run:

```
check_quota({ estimated_images: 1, model_id: "openai-gpt-image-2", quality: "medium" })
```

It returns an estimate, not the bill; say so in the report. GPT Image 2 needs a set `quality` for a dollar figure (`auto` gives none), and the GPT Image 2.5 models get no dollar figure at all, only a warning. Put "no estimate" in the table rather than a guess.

## 2. Write the brief and the checks

Write the brief and 3 to 5 checks, each answered pass or fail by looking at the picture ("exactly one key", "the skull is on the bow, not the blade", "no lettering", "plain background"). Writing them first stops a surprising favourite from rewriting the test afterwards. `refine-loop.md` has more on writing checks.

## 3. Hold everything else fixed

| Hold fixed | Why |
|---|---|
| The prompt, word for word | A different prompt measures the prompt, not the model |
| The references | Same pictures, same roles, same order |
| `aspect_ratio` | Different shapes change the composition |
| One image per model (`n: 1`) | Unless every model runs the same count |
| `enhance_prompt` off | It rewrites the prompt on each call, so each model would get a different one |

Only `model_id` changes. Write down every difference a model forces:

- Nano Banana 2 Lite is fixed at 1K.
- `resolution_tier` does nothing on the OpenAI models; they take `size` and `quality` instead.
- Go Bananas takes no seed, so every run differs, even on the same model.

## 4. Run it

**Text-only brief:** one batch, one item per model, the prompt copied into each item (`defaults` holds no prompt). Up to 8 items.

```
batch_generate({
  items: [
    { prompt: "a brass pocket compass with a cracked glass face, watercolour, plain background", model_id: "gemini-nano-banana-2.1" },
    { prompt: "a brass pocket compass with a cracked glass face, watercolour, plain background", model_id: "openai-gpt-image-2" }
  ],
  defaults: { aspect_ratio: "1:1" }
})
```

Batch items take no `quality`, so OpenAI rows run at `auto`; and a batch reports one total time for all its items, not one per model.

**A brief with reference images, an OpenAI quality to hold fixed, or per-model timing:** make one `generate_image` call per model, one after another, never several at once:

```
generate_image({
  prompt: "the bottle from the reference on a wet slate counter, soft window light from the left",
  model_id: "gemini-nano-banana-2.1",
  aspect_ratio: "1:1",
  reference_assets: [{ role: "product", product_id: 3 }]
})
```

Then the same call with the next `model_id`.

**ChatGPT subscription connected?** `generate_with_subscription` can be one more row with the same prompt and an `aspect_ratio` it supports (1:1, 4:5, 5:4, 9:16, 16:9). It takes no references, negative prompt or style preset, so it fits only a text-only brief.

## 5. Measure

For each model, record:

- the `model_id`;
- how long the call took (time separate calls yourself; leave the column out for a batch);
- the width and height it returned (`get_image_info`);
- the cost estimate from step 1;
- every check, pass or fail.

One image per model can mislead on a loose brief, since every run differs. If the user agrees, run 2 or 3 per model and say how many in the table.

## 6. Judge

- Look at each image at full size, not at the thumbnail. Small text, edges and hands hide at small size.
- Mark each check pass or fail exactly as written. A related feature or a near match is a fail.
- A check you cannot settle at that size is unverified, not a pass.

## 7. Deliver

One table, one row per model:

| Model | Checks passed | Time | Size | Estimate | Notes (forced differences, number of runs) |
|---|---|---|---|---|---|

Then say which model you would pick for this brief, and why. The ranking holds for this brief and this workspace only. Never change the workspace's default model or a saved character's `preferred_model` without asking.

Offer to file the images in one project; pass `project` only once the user names it.

## Common mistakes

| Mistake | What happens |
|---|---|
| Changing the prompt, size or count between models | The table measures those differences, not the models |
| Leaving `enhance_prompt` on | Each model gets its own rewritten prompt |
| Writing the checks after looking | The favourite sets the test |
| One run per model on a loose brief | Chance picks the winner |
| Judging from thumbnails | Fine defects pass unnoticed |
| Treating the winner as permanent | The ranking changes with the brief and with new models; run it again then |
