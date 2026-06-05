---
name: opensearch-logging-standard
description: Enforce OpenSearch-compatible logging standards when writing or modifying code. Use when the user asks to write new features, fix bugs, add endpoints, refactor logic, or any task that involves writing or modifying application code. Ensures all logs use state.log with only info/error levels, includes proactive diagnostic logging, and follows the project-specific formatting conventions.
---

# OpenSearch Logging Standard

## Mandatory Rules

When writing or modifying any code, strictly follow these four rules.

### Rule 1: Use `state.log` Only

All logging MUST use `state.log`. Never introduce custom logger instances, `console.log`, `print`, or any other logging mechanism.

**Scope**: Only enforce this in code you are writing or modifying. Do not refactor unrelated legacy logging.

### Rule 2: `info` and `error` Levels Only

OpenSearch only ingests `info` and `error`. Never use `warning`, `warn`, or `debug`.

| Scenario | Level |
|----------|-------|
| Status transitions, data tracking, operation records | `info` |
| Exception catches, business logic blocks, unexpected failures | `error` |
| Situations you'd normally log as `warning` | Downgrade to `info` or upgrade to `error` based on business impact |

### Rule 3: Proactive Diagnostic Logging

When writing complex conditional branches, core state changes, or try-catch blocks, add `state.log` calls with sufficient context for production debugging:

- Include key identifiers (IDs, names, unique references)
- Log state values before and after transitions
- Capture exception stack traces in `error` logs
- Ensure every significant code path has at least one log entry

### Rule 4: Project-Specific Formatting

All new log messages MUST follow the project's established formatting conventions, optimized for OpenSearch keyword search and field extraction.

#### 4.1 Message Structure

```
<Prefix> <ContextFields> <Description> [ExtraKV] [Traceback]
```

- **Prefix** — structured tag at the start, for OpenSearch filtering
- **ContextFields** — `req_id`, `email`, etc. in `key:{value}` format
- **Description** — concise action/result in English
- **ExtraKV** — additional variables relevant to this specific log
- **Traceback** — `traceback:{traceback.format_exc()}` at the very end (error logs only)

#### 4.2 Prefix Tag Styles

Use one of two styles depending on context:

| Style | When to Use | Example |
|-------|-------------|---------|
| `Module::EventName` | Error/exception events within a well-defined module | `ApiService::EmailSendFailed`, `Meeting::ProcessingTimeout` |
| `[Tag]` | Grouping related operations or feature-specific flows | `[RemoveDiscount]`, `[ClearReferralCredit]` |

For `info` logs in general request flows, the prefix can be omitted — start directly with context fields.

#### 4.3 Variable Format: `key:{value}`

Use f-string with `key:{value}` (**no space** after colon). This is the project standard.

```python
# Correct — project standard
state.log.info(f"req_id:{req_id} email:{email} Purchase acknowledged successfully")
state.log.error(f"ApiService::TouchUsageFailed key_id:{key_id} client_ip:{client_ip} traceback:{traceback.format_exc()}")

# Wrong — do not use these formats
state.log.info(f"req_id={req_id}, email={email}, Purchase acknowledged")  # key=value
state.log.info(f"req_id: {req_id}, email: {email}")  # space after colon
state.log.info(f"{{req_id: '{req_id}', email: '{email}'}}")  # JSON-like
```

#### 4.4 Required Context Fields

Always attach the available context fields from this priority list:

| Field | When to Include |
|-------|----------------|
| `req_id` | Every log inside a request/webhook handler (via `request.state.req_id` or `getattr(request.state, 'req_id', '')`) |
| `email` | Any user-facing or subscription operation |
| `user_id` | When `email` is not available, or when both are needed |
| `meeting_id` | Inside meeting/transcription flows |
| `api_key_id` / `api_client_id` | Inside API service (B2B) flows |
| `connection_id` / `ws_id` | Inside WebSocket connection lifecycle |
| `subscription_id` / `transaction_id` | Inside payment/subscription operations |

#### 4.5 Error & Exception Format

For `error` logs in exception handlers, place descriptive context first and `traceback` at the end:

```python
# Good: Module::Event prefix + context fields + traceback at end
try:
    await send_verification_email(email)
except Exception:
    state.log.error(
        f"ApiService::EmailSendFailed email:{email} req_id:{req_id} "
        f"traceback:{traceback.format_exc()}"
    )
    raise

# Good: [Tag] prefix for grouped operations
try:
    await clear_credit(user_id, amount)
except Exception:
    state.log.error(
        f"[ClearReferralCredit] Failed to clear credit for user_id:{user_id} "
        f"email:{email} amount:{amount} traceback:{traceback.format_exc()}"
    )
    raise

# Bad: no prefix, no context fields, vague message
try:
    await send_verification_email(email)
except Exception as e:
    state.log.error(f"email send error: {e}")
```

For non-critical exceptions where you want automatic stack trace capture, use `state.log.exception()` (internally maps to `error` level with stack):

```python
except Exception:
    state.log.exception(f"req_id:{req_id} email:{email} Failed to acknowledge purchase")
```

#### 4.6 Info Log Format

For `info` logs, lead with context fields, then describe the action/result:

```python
# Request-scoped operation
state.log.info(f"req_id:{req_id} email:{email} Acknowledging purchase, subscriptionId:{subscription_id}")

# Result confirmation
state.log.info(f"req_id:{req_id} email:{email} Purchase acknowledged successfully")

# Phase/stage marker in processing pipeline
state.log.info(f"Phase 2: Speaker identification: {meeting_id}")

# Tagged operation group
state.log.info(f"[ClearReferralCredit] Clearing {clear_amount} cents referral credit balance for email:{email}")
```

## Quick Checklist

Before finishing any code change, verify:

- [ ] No `logger.*`, `console.log`, `print` — only `state.log`
- [ ] No `warning` / `warn` / `debug` levels — only `info` and `error`
- [ ] Key code paths have diagnostic logs with context variables
- [ ] Exception handlers include `state.log.error` with identifiers and stack trace
- [ ] Log messages use `key:{value}` format (no spaces after colon), not `key=value` or `key: value`
- [ ] `req_id` and `email`/`user_id` are included when available in scope
- [ ] Error/exception logs use `Module::EventName` or `[Tag]` prefix for easy OpenSearch filtering
