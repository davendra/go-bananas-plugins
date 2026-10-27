# Consistent Sets

How to keep a character, product or look the same across many images. The same steps are in `get_help({ workflow: "consistent_characters" })`.

Method adapted from [scenario-labs/skills](https://github.com/scenario-labs/skills) (MIT), rewritten for Go Bananas tools.

---

## Baseline plus one change

Every image in a set gets the same baseline text, byte for byte. Only one final clause changes per image.

```
Baseline:  "Flat vector illustration, thick black outlines, palette of teal, coral and cream,
            plain cream background. Hero Henry, 10-year-old boy, short black hair, red cape,
            blue mask with white trim."
Image 1:   baseline + " He waves from a rooftop."
Image 2:   baseline + " He reads a map on a park bench."
Image 3:   baseline + " He catches a falling kite."
```

Rewording the baseline between images ("thick outlines" in one, "bold lines" in the next) is the most common cause of a set that drifts. Copy it; don't retype it.

Where the baseline lives:

- **Saved character:** `base_prompt` on `create_character` holds the face, hair, build, outfit and markings. Put no pose, setting or light in it. Each call changes only `scene_prompt`.
- **Style:** save the style part with `create_style_preset` and pass `style_preset_name` on every call (`generate_image`, `batch_generate`).
- **Text-only sets:** `batch_generate`, up to 8 items, with `style_preset_name`, `aspect_ratio` and `negative_prompt` in `defaults`. `defaults` holds no prompt, so copy the baseline into each item's `prompt`. Batch items take no reference pictures.

## Anchor to the approved picture

- Approve one picture first, and check the face, hair, outfit and markings closely. Every later image copies it, flaws included.
- Every image in the set uses that same approved picture as its reference. Never feed the last result back in as the reference for the next: each step copies the one before it with a small error, and the errors add up.
- To add a picture, check it as closely as the first, then send `update_character` the full list (`reference_image_ids: [42, 57]`): it replaces the old list. Only the lowest ids are sent (4 on standard models, 5 on Pro and the OpenAI models), so a picture past the cap is never used; drop one to make room.
- The reference holds the face, not a fixed seed (Go Bananas takes none). For another take of one shot, run the same call again after the first has finished; identical calls still running are merged into one image.

## Reference limits

- A saved character sends up to 4 reference pictures on standard models and 5 on Pro and the OpenAI models. In a multi-character scene all characters share those 4 or 5, at most 3 each, in list order: put first the character whose face matters most.
- One request takes up to 14 reference pictures on Gemini and 16 on OpenAI.
- Character pictures and `image_id` or `image_url` references must be at least 512x512. Product photos are not checked, but use one at least that size.

## When it drifts

| Symptom | Fix |
|---|---|
| One image is off (wrong scar side, wrong eye colour) | `edit_image` on that image, naming the feature itself, since it sees only that image and not the references: "move the scar to above the left eyebrow; change nothing else" |
| Every image is slightly off in the same way | Replace the references with a clearer picture (`update_character` with the new full list), or a front-on face plate (`reference_sheet: "face-plate"`) |
| The character's old background keeps appearing | Either `base_prompt` names a setting (remove it with `update_character`) or the reference picture shows one (replace it with a picture on a plain background, such as a face plate). Then describe the new setting in full in `scene_prompt` |
| Two characters swap features | `generate_with_multiple_characters`, naming each character with their place in the frame ("Hero Henry on the left") |
| The style changes across the set | The baseline was reworded. Copy it again exactly, or move it into a style preset |

Do not keep rewording `base_prompt` to fix drift. Change the reference first.
