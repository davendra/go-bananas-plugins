# Error Handling & Quota Management

Guidelines for handling quota exhaustion, rate limits, and service degradation when using Go Bananas MCP tools.

## Pre-Flight Check

Before starting batch operations (3+ image generations), call `check_quota` to verify:
- Storage quota has sufficient remaining MB
- Rate limit has available requests
- Service health is not degraded (circuit breaker is not open)

```
check_quota → { canGenerate: true/false, reasons: [...], details: {...} }
```

If `canGenerate` is `false`, stop and inform the user with the specific reasons.

## Detecting Persistent Failures

Track consecutive errors. If you encounter **3+ consecutive failures** from generation tools:

1. **Stop retrying immediately** - Do not continue sending requests
2. **Call `check_quota`** to diagnose the root cause
3. **Report findings** to the user with actionable next steps

### Common Failure Patterns

| Pattern | Likely Cause | Action |
|---------|-------------|--------|
| "Circuit breaker is open" | Gemini API is degraded | Wait for `timeUntilRetrySeconds`, then retry once |
| "Rate limit exceeded" | Too many requests/minute | Wait for `resetAt` time, then continue |
| "Quota exceeded" / 402 | Monthly storage full | Stop all generation. Inform user to upgrade or wait for reset |
| "Generation timed out" | Network/API slowness | Retry once with simpler prompt. If fails again, stop |
| Repeated 500 errors | Service outage | Stop and suggest user check back later |

## When to Stop vs. Wait

### Stop Immediately (No Retry)
- Quota exceeded (402) - no amount of waiting helps within the billing period
- Authentication errors (401) - credentials are invalid
- Validation errors (400) - fix the input first

### Wait and Retry (Up to 1 retry)
- Circuit breaker open - wait `timeUntilRetrySeconds`
- Rate limit hit - wait until `resetAt`
- Timeout errors - wait 30 seconds, try once with simpler prompt

### Retry with Backoff (Up to 2 retries)
- Network errors / 503 - exponential backoff: 5s, 15s
- Gemini API intermittent failures

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
2. If false → report reasons, stop
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

The `get_account_summary` tool now includes rate limit and service health information:

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
