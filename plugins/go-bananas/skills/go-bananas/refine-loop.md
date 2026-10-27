# Refine Loop

How to get from a first image to an accepted one without endless re-rolls. The same steps are in `get_help({ workflow: "prompt_iteration" })`.

Method adapted from [scenario-labs/skills](https://github.com/scenario-labs/skills) (MIT), rewritten for Go Bananas tools.

---

## 1. Write the checks before generating

Write 3 to 6 checks the image must pass. Each one must be answerable yes or no by looking at the picture.

| Vague (can't be checked) | Checkable |
|---|---|
| "looks premium" | "the bottle is on dark slate, lit from the left, with one soft shadow" |
| "the logo is right" | "the label reads NORTHWIND, letter for letter" |
| "a family at dinner" | "exactly four people: two adults, two children" |
| "she holds a mug" | "the mug is in her left hand, handle facing the camera" |

Show the checks to the user when the brief is theirs. They often add the one that matters most.

## 2. Judge every image against every check

For each check: pass or fail, and one reason. Never give a score out of 10; a score hides which check failed.

Always run the structural check, because a picture can look right at a glance and still fail it:

- **Counts:** people, hands, fingers, legs, wheels, objects asked for.
- **Attachment:** what holds what, what touches what, which hand, which side.
- **Text:** read every word letter by letter, including small print.
- **References:** the character's face and markings match; nothing from a style picture leaked in (see `multi-reference.md`).

## 3. Pick the cheapest fix

| Failure | Fix |
|---|---|
| Small local defect on a good image (misspelt word, extra finger, stray object) | `edit_image({ image_id, prompt: "change only …; keep everything else the same" })` on the keeper. `continue_editing` does the same but always edits the last image of the session. GPT Image 2 also takes `mask_image_id` (a mask the same size and format as the image) |
| Wrong content, layout or count | Generate again with one change to the prompt |
| A reference copied too closely or ignored | Change that entry's `role`, `notes` or `weight` in `reference_assets` |
| The same check fails after two fixes | Suggest another `model_id` (see `list_models`) and ask the user before switching |
| The provider blocked the image | Tell the user it was blocked. Rewrite once, and only to remove an accidental trigger: describe size by proportion instead of intensity words ("twice his height", not "monstrous"). If the block is about the content itself (a real public figure, violence, nudity), stop and leave the choice to the user. Never keep rewording to get past a filter |

Editing a good image is one call with a known result. A new roll can lose what was already right.

## 4. One change per round

Change one thing per round and keep the rest of the prompt word for word. If two things change and the image improves, you don't know which one helped, and the next round can undo it.

## 5. Stop after 3 rounds

After 3 rounds, stop. Show the best image, list the checks that still fail, and ask the user how to go on: accept it, loosen a check, try another model, or change the brief.

## Exploring an open brief

When there is no fixed brief, start short (`"A fashion photo"`), see what the model picks by default, then add one element per run (`"A high-end fashion photo"`, then the setting, then the light). Switch to the loop above as soon as the user says what they want.
