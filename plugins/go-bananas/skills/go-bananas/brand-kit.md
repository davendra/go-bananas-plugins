# Brand Kit

How to build a consistent visual identity with Go Bananas: decide the brand first, make one logo, then reuse that logo in every placement instead of redrawing it. The same steps are in `get_help({ workflow: "style_branding" })`.

Method adapted from [scenario-labs/skills](https://github.com/scenario-labs/skills) (MIT), rewritten for Go Bananas tools.

Go Bananas makes raster images (PNG and, on the OpenAI models, JPEG or WebP). It does not make vector files: if the user needs an SVG logo, a designer redraws the approved one.

---

## 1. Write the spec first

Agree it with the user before any image. Every prompt and every check comes from it.

| Part | What to write |
|---|---|
| Palette | 4 to 6 hex values, each with a role: primary, ink, paper, accent. The accent appears on every asset; a second accent dilutes the first |
| Typefaces | Two families the user can actually use (display and body), with weights. A generated typeface is only a picture of one, so real text is set outside Go Bananas |
| Voice | One short, specific line, not a slogan |
| Logo rules | Clear space and smallest size |
| Never rules | Three things nobody may do with the logo, for example stretch it, recolour it outside the palette, or retype the name in another font |

Prompts quote the hex values exactly. A colour described only as "warm blue" drifts from image to image.

## 2. Save the look as a style preset

```
create_style_preset({
  name: "Northwind Brand",
  prompt: "flat graphic illustration, clean closed shapes, palette #0B3D91 primary, #F4F1EA paper, #1B1B1B ink, #FF6B35 accent",
  negative_prompt: "gradients, glitter, lens flare, extra colours"
})
```

Pass `style_preset_name: "Northwind Brand"` on every call. The preset text goes in front of the prompt, and its `negative_prompt` applies when the call sends none. Change the preset to change every later image.

## 3. Make the logo once

1. Make three candidates, each built a different way (pick three of these), so the user chooses between ideas rather than between three versions of one:
   - the initial fused with what the company does;
   - the company's main verb as a symbol;
   - a shape whose empty space holds a second idea;
   - simple geometry on a stated grid.
2. Flat closed shapes, 2 or 3 palette colours, readable at app-icon size. Name what to leave out (gradients, sparkles, a globe, a lightning bolt), or the model adds them.
3. One call per candidate, then let the user pick:

```
generate_image({
  prompt: "Logo mark for Northwind, a cold brew company: a capital N whose right stroke becomes a falling drop. Flat closed shapes in #0B3D91 and #FF6B35 on #F4F1EA, centered, no gradients, no sparkles, no other text.",
  aspect_ratio: "1:1",
  model_id: "gemini-nano-banana-2.1"
})
```

## 4. Check the logo

- The name letter by letter.
- Every shape closed and centered.
- No likeness to a well-known logo.

One wrong letter on a good logo is an edit:

```
edit_image({ image_id: 88, prompt: "change only the second letter so the name reads NORTHWIND; keep everything else the same" })
```

A logo that is wrong in several places gets a new call with a tighter prompt (see `refine-loop.md` for the cheapest fix and the 3-round limit).

## 5. Use the one logo everywhere

```
generate_image({
  prompt: "the logo from the reference, unchanged, printed on a navy canvas tote bag hanging on a wooden hook, soft daylight",
  style_preset_name: "Northwind Brand",
  reference_assets: [{ role: "logo", image_id: 88 }]
})
```

- The `logo` role tells the model to reproduce the picture exactly. Never ask it to draw the logo again from words.
- One placement per call, one after another: avatar, banner, tote bag, business card, signage.
- No other words or made-up app screens in the frame. A placement shows the logo in place, not the product working.
- Always reference the approved logo, never an earlier placement: a copy of a copy drifts.

## 6. Check every placement against the spec

- The logo's shape and colours match the approved file.
- Only palette colours, with the accent present.
- None of the three never rules broken.

A placement where the logo came out changed: run the same call again naming the fault ("the top bar of the N is missing"), with the approved logo still as the reference.

## 7. Words and colour versions

- **Taglines, handles, web addresses:** leave room for them and set them in the brand typeface in a design tool. If they must be in the image, quote them exactly and check them letter by letter.
- **One-colour, reversed and dark versions of the logo:** recolour the approved file in an editor. `edit_image` can do it, but it redraws the whole image, so lay the result over the original and compare the outline before keeping it.

## 8. Keep the kit together

Offer to file the logo, its versions and every placement in one project. Pass `project` only once the user picks the name (`list_projects` shows the existing ones), and keep passing the same name for the rest of the work. Deliver the spec from step 1 as the kit's usage page.

## Common mistakes

| Mistake | What happens |
|---|---|
| Making placements before the spec exists | Nothing to check against, so the kit drifts apart one image at a time |
| Asking for the logo again in each placement's prompt | Ten near-copies and no identity |
| Building placements from earlier placements | Each copy drifts further from the approved logo |
| Naming colours by mood instead of hex | The palette shifts warm or cool from image to image |
| Passing a logo that only looks right small | Letter shapes drift where a small view hides them; read it letter by letter |
