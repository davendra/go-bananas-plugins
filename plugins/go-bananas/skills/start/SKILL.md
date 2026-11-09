---
name: start
description: First run with Go Bananas. Use when someone has just installed or connected the plugin, asks what can Go Bananas do, asks how to get started, or wants their first image, including the /go-bananas:start command. Not for ordinary requests once they are up and running; the go-bananas skill owns those.
---

# Start: a first image in one pick

Two turns from "what can you do" to a saved image. Read `gettingStarted`, offer, make. Never ask a second question before the image exists.

1. **Read the account.** Call `get_account_summary` and read `gettingStarted` (also in `structuredContent`). If the tool is refused for a missing scope, say so and point at https://gobananasai.com/connect to reconnect with the `analytics:read` scope.

2. **Two lines, real numbers.** Line 1: Go Bananas makes images, keeps characters and products consistent across scenes, and saves everything to a gallery. Line 2: quote `allowance.oneLine` word for word. If `canMakeFirstImage` is false, show `nextStep` and stop.

3. **Three starters, one pick.** Offer `suggestedUserPrompts` by `label` and `userLine`, numbered, plus "or describe yours in one line", as one question (a multiple-choice prompt when the host has one). If the block is absent (an older server), offer these defaults and send them with `generate_image`: a wide website hero with room for a headline (`aspect_ratio` "16:9"); an app icon, one simple mark on a solid colour ("1:1"); a portrait social post, a product on a pastel background ("4:5").

4. **Make it now.** Call the starter's `tool` with its `params` exactly as given (or the brief as `prompt`). Add a fresh UUID `client_request_id` only where `idempotencyParam` names it; the other tools refuse unknown keys. Ask nothing else first. A timeout never means the image failed: call `get_session_history` and look for it before any retry, and never start a second generation for the same request. If the Nana starter answers `character_not_found`, offer the social post instead.

5. **Deliver.** Show the image link, say it is saved to the gallery, and give `links.gallery` the first time.

6. **One next step.** Offer a variation with `continue_editing` ("add clouds", "warmer light") or saving the subject as a reusable character with `create_character`. From here the go-bananas skill takes over.
