---
name: error-code-development
description: Use when adding, changing, or reviewing error codes in this repository, especially in common/errors/error_code_base.py, common/errors/error_code_ranges.py, api_server/errors/*_errors.py, api_server/errors/error_codes.py, or the related tests.
---

# Error Code Development

## Overview
This repository uses a shared `CodeItem` / `BaseErrorCode` / `ResponseCode` system. Treat it as the only supported error-code path, and match the current module layout instead of inventing a parallel one.

## When to Use
Use this skill when you need to:
- add a new error-code module
- update or audit an existing module
- decide whether a code belongs in `BasicErrorCode` or a module-specific class
- register a module in `ResponseCode`
- update the range table or the error-code tests

## Repository Pattern
Current structure:
- `common/errors/error_code_base.py` for `CodeItem` and `BaseErrorCode`
- `common/errors/error_code_ranges.py` for shared ranges
- `api_server/errors/<module>_errors.py` for module definitions
- `api_server/errors/error_codes.py` for `ResponseCode`
- `api_server/errors/tests/test_error_code_ranges.py` and `test_error_codes.py` for integrity checks

Large modules may compose nested `BaseErrorCode` subclasses, like `ResponseCode.ws.audio.TRANSCRIPTION_FAILED`.

## Core Checks
Before changing anything, inspect the current module and mirror its local style.

- Confirm the module's range and do not reuse an existing code value.
- Keep the public access path under `ResponseCode` stable.
- Use `CodeItem` for fixed messages.
- Use an instance method only when `detail` must include runtime values.
- Keep `detail` user-facing, in English, friendly, and ending with a period.
- Register the module in `ResponseCode` and both tests.

## Adding a New Module
- Pick an unused range that does not overlap existing modules.
- Create `api_server/errors/<module>_errors.py`.
- Define the module class with `BaseErrorCode` and an explicit `CODE_RANGE`.
- Add the module to `ResponseCode` with a `lower_snake_case` attribute.
- Extend `test_error_code_ranges.py` and `test_error_codes.py`.
- Run the tests to confirm instantiation and range validation still pass.

## Updating an Existing Module
Prefer the smallest change that preserves the current access path.

- If the module is flat, keep it flat.
- If it already uses nested submodules, extend that pattern instead of flattening it.
- If a code is already shipped, leave the numeric value alone.
- If the message changes, keep it clear and non-technical.

## Detail Style
Good details read like final user messages:
- `The email address you entered is invalid. Please check and try again.`
- `We couldn't complete your request right now. Please try again in a moment.`

Avoid stack traces, database fields, internal IDs, Chinese text, and missing punctuation.

## Common Mistakes
| Mistake | Better approach |
|---|---|
| Hardcoding a numeric code in business logic | Route through `ResponseCode` |
| Reusing an old code value for a new meaning | Pick a new code in the correct range |
| Forgetting registration | Update `ResponseCode` and both tests |
| Making `detail` technical | Rewrite it for end users |
| Flattening a large nested module | Keep the nested structure |

## Quick Example
```python
from common.errors.error_code_base import BaseErrorCode, CodeItem
from common.errors.error_code_ranges import error_code_range


class WsBaseErrorCode(BaseErrorCode):
    CODE_RANGE = error_code_range.WS_CODE_RANGE
    INVALID_DATA_FORMAT = CodeItem(90001, "Invalid data format.")


class WsAudioErrorCode(BaseErrorCode):
    CODE_RANGE = error_code_range.WS_CODE_RANGE
    TRANSCRIPTION_FAILED = CodeItem(90203, "Speech to text transcription failed. Please try again.")


class WsErrorCode(BaseErrorCode):
    CODE_RANGE = error_code_range.WS_CODE_RANGE
    base = WsBaseErrorCode()
    audio = WsAudioErrorCode()
```

