# Storyboards

How to make a sequence of panels that read as one story. The same steps are in `get_help({ workflow: "storyboarding" })`.

Method adapted from [scenario-labs/skills](https://github.com/scenario-labs/skills) (MIT), rewritten for Go Bananas tools.

---

## 1. Beat table first

Before any image, write one row per panel and agree it with the user:

| # | Beat | Shot | Angle | In frame | Action and direction |
|---|---|---|---|---|---|
| 1 | Henry spots the kite | Wide | Eye level | Henry | Runs left to right across the park |
| 2 | The kite snags | Close-up | Low | Kite, tree | Kite string catches on a branch |
| 3 | Henry climbs | Medium | Low | Henry | Climbs the trunk, facing right |
| 4 | He hands it back | Medium | Eye level | Henry, girl (left) | Henry on the right hands the kite to her |

Changing the story here costs nothing. Changing it after 8 panels costs 8 images.

## 2. The panel is the unit

- **One image per panel.** Don't ask for a grid of panels in one image: panels in a grid copy each other and can't be redone one at a time.
- **Same baseline in every panel.** One paragraph for style, palette and setting, copied word for word. Only the beat sentence changes. Save the style part with `create_style_preset` (see `consistency.md`).
- **Same `aspect_ratio` throughout:** 16:9 for film and animatics, 9:16 for vertical video.

Which tool per panel:

| Panel | Call |
|---|---|
| One saved character | `generate_with_character({ character_name, scene_prompt: "<baseline> <beat>", aspect_ratio })` |
| Saved character plus a style preset | `generate_image({ prompt: "<baseline> <beat>", style_preset_name, reference_assets: [{ role: "character", character_id }], aspect_ratio })`. It sends the pictures and the name but not `base_prompt`, so the baseline must carry the key traits |
| Two or more characters | `generate_with_multiple_characters`, naming each character's side of the frame |
| No saved character | `batch_generate` with the shared `style_preset_name`, `aspect_ratio` and `negative_prompt` in `defaults`, one item per panel, up to 8 per batch. `defaults` holds no prompt, so copy the baseline into each item |

Use the same route for every panel with the same characters: the routes word the character differently, and mixing them makes panels drift.

## 3. Keep the art free of text

- Put `"text, captions, speech bubbles, panel numbers, borders"` in `negative_prompt` (`generate_image`, `batch_generate`) or `override_negative_prompt` (`generate_with_character`; it replaces the character's saved negative prompt, so repeat what that one held). `generate_with_multiple_characters` takes neither: end its `scene_prompt` with "no text or speech bubbles".
- Add dialogue and captions later, outside the image. Text-free panels stay usable when the script changes.
- In your reply, label each panel with its number, shot size and beat, like a shot list.

## 4. Keep screen direction

If Henry runs left to right in panel 1, say "moving left to right" in every later panel until a beat turns him round. Do the same for who stands on which side. A character who flips direction between panels reads as going back.

## 5. Check each panel before the next

Compare the panel with its row: who is in it, the shot size, the direction, the character's face and outfit. Redo a failed panel on its own (see `refine-loop.md`); never regenerate the whole board.
