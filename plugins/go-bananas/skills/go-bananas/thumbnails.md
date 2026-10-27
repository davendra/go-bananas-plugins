# Video Thumbnails

How to make a thumbnail or cover that still reads at the size people see it, with the user's exact words and an approved face. The same steps are in `get_help({ workflow: "viral_thumbnails" })`.

Method adapted from [scenario-labs/skills](https://github.com/scenario-labs/skills) (MIT), rewritten for Go Bananas tools.

---

## 1. Settle the brief

Before any image, know:

- **Where it goes and its size.** A YouTube thumbnail is 16:9, delivered at 1280x720. Other placements are in `formats.md`.
- **The exact words.** The headline in the user's own words. No headline given means no text.
- **Who or what appears.** An approved reference for every real person or saved character.
- **How many versions,** and stick to that number.

Never invent a person, logo, quote, reward or game feature to make the picture more dramatic. A style example the user shares is not permission to use its people or its branding.

## 2. One promise, one subject

Write in one sentence what the viewer should recognise at a glance. Then choose:

- one main subject and one action;
- one quiet area sized for the actual words (two short lines need far less than half the frame);
- a layout that comes from the brief, not a habit:

| Brief | Layout that tends to work | Check before generating |
|---|---|---|
| A reveal (a boss, a product, a result) | The thing itself, large, with one clear action | It looks like the approved reference |
| Explaining how something works | One action and what it causes | The thing shown really exists in the material |
| An update or new feature | The new feature as the main subject | It is released, not planned |
| Before and after | A split image | The two states really differ in the picture |
| A presenter-led video | The presenter and the topic | Who appears, and their reference |

## 3. Faces come from references

```
generate_image({
  prompt: "Video thumbnail. The presenter from the reference on the right, surprised, pointing at a cracked phone on the left. Bright yellow background, strong contrast, the bottom right corner left empty.",
  aspect_ratio: "16:9",
  reference_assets: [{ role: "character", character_id: 5 }]
})
```

- With a character reference and no `model_id`, Go Bananas uses GPT Image 2 when the workspace allows it, has an OpenAI key and the request fits it (a 1K request, a ratio it takes, no Google Search or thinking). That is its face default, chosen after a test where GPT Image 2 kept faces that Pro changed. For a thumbnail with a face, leave `model_id` out unless the user asks for a model.

- For a photo the user uploaded, use `{ role: "character", image_id: 42 }`. Reference pictures must be at least 512x512.
- Typed references send the pictures and the name, not the saved description, so restate the key traits (hair, glasses, outfit) in `prompt`.
- Check the face against the reference first. Adding text never fixes a changed face.
- `edit_image` does not see the reference, so a fix for a drifted face names the feature itself ("thin round gold glasses and a short grey beard; change nothing else"). If that fails, generate again with the reference.

## 4. Getting the words right

| Route | When | How |
|---|---|---|
| Text-free picture, words set in an editor | The words must be exact, or will change later | Leave the text area empty in the prompt; set the headline in the user's editor. A new headline then reuses the same picture |
| Words in the image | The user wants one finished file | Quote the exact words with place, size and colour: `headline "NO MORE LAG" in thick white capitals, top left`. Read the result letter by letter |

One wrong letter on a good picture is an edit, not a new image:

```
edit_image({ image_id: 57, prompt: "change only the headline so it reads NO MORE LAG; keep everything else the same" })
```

## 5. Check it small

Every generated image comes back with a 256 px thumbnail, close to its size in a feed. Look at it and ask:

- Can you tell what the video is about?
- Can you read the headline?
- Is the face (or the main subject) still clear?
- Is the bottom right corner clear? YouTube puts the duration badge there.

A sharp full-size picture can still fail at feed size. Asking for a higher resolution does not pass this check.

## 6. Versions and revisions

- `n` (1 to 4) gives several takes of one idea. A different idea is a separate call, made after the previous one finishes.
- Label each version (A, B, C) so the user's feedback maps to one.
- For a revision, decide whether the note is about the layout, the face, the words or the size, and change only that.
- A review can show whether a thumbnail reads and whether the face holds. It cannot show which version will get more clicks; don't claim it does.
