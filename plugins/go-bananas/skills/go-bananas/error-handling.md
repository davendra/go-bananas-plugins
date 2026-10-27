# Error Handling & Quota Management

Guidelines for handling errors, quota exhaustion, rate limits, and service degradation when using Go Bananas MCP tools.

## Pre-Flight Check

Before starting batch operations (3+ image generations), call `check_quota` to verify:
- Storage quota has sufficient remaining MB
- Rate limit has available requests
- Service health is not degraded (circuit breaker is not open)
- Image storage passes the server's self-test (`details.storageHealth`)
- No provider has rejected a recent request for billing or quota reasons (`details.providerStatus`)

```
check_quota → { canGenerate: true/false, reasons: [...], warnings: [...], details: {...} }
```

If `canGenerate` is `false`, do not generate. Tell the user the specific reasons.

## Reading an MCP Error

A failed tool call comes back as a tool result with `isError: true`. Most failures read `MCP error <code>: <message>`, and two codes matter. The checks made before a tool runs (the workspace's rate limit, its monthly storage quota, a missing OAuth scope) read `Error: <message>` instead, with no code.

### -32602: the request can never succeed

Fix the arguments. Do not retry the same call. It covers:
- Invalid parameters: `Invalid input parameters: <field>: <reason>`, or `Input validation error: Invalid arguments for tool …` when the MCP library itself rejects them
- An unknown `model_id`, or a model (or the workspace default) that is not on the workspace allowlist. Call `list_models`
- An option the model does not support, or a prompt over the model's limit once presets, scenes, a camera look or a reference sheet are added
- An ID or name that is not in this workspace (image, session, character, product, scene, style preset, reference group, project), or `continue_editing` in a session with no image yet
- A name another saved item already uses, or a revert with nothing archived
- A rejected `reference_assets` entry, `Image generation failed: reference_assets[N]: …` (or `Image generation failed: Too many reference …`): two sources in one item, a reserved role, `scope` on a role other than `character`, more images than the model takes, an image the workspace cannot use, or one under 512x512. Fix that entry. Over REST it is 400 `invalid_reference_assets`
- `generate_image` with `reference_sheet` and `camera_look`, `enhance_prompt` or `image_to_edit_id`, or with `camera_look` and `enhance_prompt` or `image_to_edit_id`; a `batch_generate` item with both `reference_sheet` and `camera_look`
- A provider content block: the text ends `(code: content_blocked). Rephrase the prompt or change the references.` See [Blocked Generations](#blocked-generations-rewrite-once-then-ask)

The MCP error carries only the message, no `code` field. REST answers the same cases with 400, 404 or 409 and a `code`: `unknown_model`, `model_not_allowed`, `invalid_request`, `*_not_found` (for example `image_not_found`, `character_not_found`) or `*_name_taken` (for example `character_name_taken`). `start_generation` returns `{ "error": { "status": 400, "code": "...", "message": "..." } }` in an `isError` result instead.

### -32603: everything else

Read the message before you retry. Many `-32603` errors are not temporary.
- `An unexpected error occurred. Please try again. (errorId: <uuid>)` is a database or network failure. Retry with backoff (5s, then 15s). If it still fails, report it to the user with the errorId.
- `Image generation failed: reference_assets[N] (<role>): …` is a stored reference image the server could not read. Retry with backoff.
- Any other text is the provider's or the workspace's own message (rejected key, rate limit, billing, quota, timeout, outage). Handle it as the sections below say.

## Rejected Credentials: Do Not Retry

| Message | Action |
|---------|--------|
| `Your image provider rejected its API key. Check the provider API key in workspace settings, then try again.` | The workspace's Gemini or OpenAI key is invalid or expired. Tell the user to replace it in workspace settings. REST answers 422 `provider_credentials_rejected`; in a REST batch or bulk edit, the item carries the same text as its `error` and `code: "provider_credentials_rejected"`, and a failed job the same `errorCode` |
| `The Higgsfield credentials were rejected. Replace provider_api_keys.higgsfield (api_key:api_key_secret) in the workspace settings.` | From `request_higgsfield_video` or `get_higgsfield_video_status`. Tell the user to replace the Higgsfield key |
| `No active Gemini API keys are configured for tenant ...` (or OpenAI) | The workspace has no key for that provider. Tell the user to add one in workspace settings |

## Blocked Generations: Rewrite Once, Then Ask

The provider refused the prompt or the references. Sending the same request again is refused again, and Go Bananas never retries it for you.

**How to recognise one**
- MCP: a `-32602` whose text ends `(code: content_blocked). Rephrase the prompt or change the references.` The text before it gives the provider's reason, such as `Reason: IMAGE_SAFETY`, `Reason: moderation_blocked`, or "the output matched existing content" (Gemini recitation).
- `batch_generate`: the item's line ends `(code: content_blocked)`. The other items are unaffected.
- `get_generation_status`: `status: "failed"` with `error_code: "content_blocked"`.
- REST: 422 `content_blocked`; `errorCode: "content_blocked"` on a failed async job; `code` on a batch or bulk-edit item.
- Subscription lane: "ChatGPT declined this prompt…", with no code. Handle it the same way.

**Why an ordinary request gets blocked.** Filters judge the whole request, so several mild signals add up:
- A real person's name, or a reference photo of a real person, together with anything intimate, violent or political
- Intensity words that add no information ("brutal", "bloody", "sexy", "massive") and exaggerated body descriptions
- A named character, franchise, logo or artwork: Gemini's recitation check stops images that copy existing work
- A reference image can cause the block on its own, even when the text is mild

**One honest rewrite**
1. Read the reason. For recitation, describe the design in your own words instead of naming it.
2. Drop the words that add intensity but not information. Describe size by proportion ("about twice the height of the door") rather than superlatives.
3. Carry a person's identity in a reference (`create_character`, `reference_assets`) rather than a real person's name.
4. Keep the subject the user asked for. A rewrite clarifies the request; it does not hide it from the filter. If the request is something the provider does not allow, say so instead.
5. Send the rewrite once, and tell the user what you changed.

If the rewrite is blocked too, stop and ask the user. Do not keep rewording to find a phrasing that passes. Do not switch `model_id` or provider just to get past a filter: another model is fine only when the user chooses it. After a ChatGPT decline, never move to the workspace's provider keys.

*Adapted from the blocked-prompt guidance in [scenario-labs/skills](https://github.com/scenario-labs/skills) (MIT).*

## Rate Limits and Quotas

Several messages contain "Rate limit exceeded", often after `Image generation failed:`. Tell them apart by the words that follow it.

| Message | Meaning | Action |
|---------|---------|--------|
| `Rate limit exceeded: N requests per minute. Try again in S seconds.` | The workspace's own limit | Wait S seconds, then continue |
| `Rate limit exceeded for <operation>. Please wait a moment and try again.` | Gemini's per-minute limit | Temporary. Wait a minute, then retry |
| `OpenAI image API key rate limit exceeded: ...` | OpenAI's per-minute limit | Temporary. Wait a minute, then retry |
| `Rate limit exceeded for <operation>: Gemini daily quota exhausted (...)` | The Gemini key's daily quota for that model | Not temporary. Do not retry. Call `check_quota` |
| `Rate limit exceeded for <operation>: Gemini billing problem reported for this API key.` | Gemini billing | Not temporary. Do not retry. Call `check_quota` |
| `OpenAI billing limit reached for the selected API key: ...` | OpenAI billing | Not temporary. Do not retry. Call `check_quota` |
| `Monthly quota exceeded: ...` or `Monthly storage quota cannot accommodate this upload.` | The workspace's monthly storage is full | Stop all generation. REST answers 402 |

After a billing or daily-quota rejection, `check_quota` shows it in `details.providerStatus` for 15 minutes, or until the next success clears it. A Gemini daily quota covers only the model that hit it, so another allowed model may still work. OpenAI billing covers every OpenAI model. If the selected model is limited, `canGenerate` is `false`; other limited models appear in `warnings`. Tell the user what was hit and which models are still usable.

## Service Problems

| Message | Action |
|---------|--------|
| `Circuit breaker is open - service temporarily unavailable. Please try again in N seconds.` | The provider has failed repeatedly. Wait N seconds, then retry once |
| `Generation queue is full (3 pending requests, ...)` | Wait for the current requests to finish. Check the gallery for recent images |
| `Request timed out after waiting Ns in queue.` or `Generation timed out after Ns execution` | Check the gallery (`get_session_history`, `search_images`) for images that finished. Then retry once with a simpler prompt. If it fails again, stop sending |
| Repeated `errorId` failures or provider outages | Stop and suggest the user check back later |

## Async Jobs: Failed or Uncertain

"Uncertain" is not a status value. An uncertain job keeps `status: "running"` and says so in `current_step`. It is never sent to the provider again.

- **`start_generation` / `get_generation_status`**:
  - `failed` means no image was saved. Fix the cause, then start a new job with a new `idempotency_key`; reusing the old key answers 409. With `error_code: "content_blocked"`, see [Blocked Generations](#blocked-generations-rewrite-once-then-ask).
  - `current_step` "Outcome uncertain — settling from saved images at the job deadline" means the job stopped after it was sent to the provider, so an image may exist. Keep polling; do not start a new job. The job settles about 14 minutes after it started: `completed` with its images if any were saved, otherwise `failed` with "Generation outcome uncertain: no image was saved before the job deadline. It will not run again."
- **Async edit** (REST `POST /api/edit-image` with `Prefer: respond-async`, polled at `/api/executions/<id>`):
  - `failed` means the edit failed before its image started saving, so nothing was saved. Its `Idempotency-Key` stays reserved (409), so a retry needs a new key. After a timeout the provider may still have charged.
  - `current_step` "Outcome uncertain — inspect before any new request" means saving had begun. Do not resend. Check the gallery and `check_quota` first.
- **Synchronous `generate_image`**: "The provider made the image, but we could not confirm it was saved to your gallery (execution ...)" is the same case. Do what the message says: with a `client_request_id`, repeat the call with the same one to check again (it will not make another image); without one, check the execution it names later. A new request makes a new image.

## Detecting Persistent Failures

Track consecutive errors. If you encounter **3+ consecutive failures** from generation tools:

1. **Stop retrying immediately** - Do not continue sending requests
2. **Call `check_quota`** to diagnose the root cause
3. **Report findings** to the user with actionable next steps

## When to Stop vs. Wait

### Stop Immediately (No Retry)
- Any `-32602` error - fix the arguments first
- Rejected provider or Higgsfield credentials, or no provider key configured
- Provider billing or daily quota
- Monthly storage quota exceeded (402) - no amount of waiting helps within the billing period
- Authentication errors (401) - credentials are invalid
- A prompt ChatGPT declined on the subscription lane, or a `content_blocked` refusal: at most one rewrite, then ask the user (see [Blocked Generations](#blocked-generations-rewrite-once-then-ask))

### Wait and Retry (Up to 1 retry)
- Workspace rate limit - wait the seconds the message gives
- Provider per-minute rate limit - wait a minute
- Circuit breaker open - wait the seconds the message gives
- Timeout errors - check the gallery first, then try once with a simpler prompt

### Retry with Backoff (Up to 2 retries)
- `-32603` with an `errorId`, network errors, 503 - exponential backoff: 5s, 15s
- Provider outages ("Please try again in a moment")

## Graceful Exit Pattern

When stopping a batch operation due to errors:

1. **Summarize what was completed** - "Generated 12 of 20 requested images"
2. **State the reason for stopping** - "Stopped due to storage quota exhaustion (98% used)"
3. **Provide the completed work** - "Your completed images are available in the gallery"
4. **Suggest next steps** - "Consider upgrading your plan or deleting unused images to free storage"

Example:
```
I've completed 12 of the 20 product marketing shots.

Stopping because: Storage quota is at 98% (9.8 GB of 10 GB used).
The 12 completed images are in your gallery under today's session.

To continue, you can:
- Delete unused images to free storage
- Contact your admin to increase the monthly quota
- Wait for the next billing cycle for quota reset
```

## Using check_quota in Workflows

### Before Batch Generation
```
1. check_quota → verify canGenerate
2. If false → report reasons, do not generate
3. If true → proceed with generation
4. After every 5 images → check_quota again (storage may fill up)
```

### During Long Sessions
```
1. After any generation failure → check_quota
2. If circuit breaker open → wait, then check_quota
3. If rate limited → wait for reset, then continue
4. If quota exhausted → graceful exit with summary
```

## Rate Limit Awareness

The `get_account_summary` tool includes rate limit and service health information:

```
rateLimit: {
  limitPerMinute: 60,
  remaining: 45,
  resetAt: "2025-01-28T12:01:00.000Z"
}
serviceHealth: {
  status: "healthy" | "degraded" | "unavailable",
  circuitState: "closed" | "open" | "half_open",
  timeUntilRetrySeconds: 25  (only when circuit is open)
}
```

Use this to pace requests and avoid hitting rate limits during intensive workflows.
