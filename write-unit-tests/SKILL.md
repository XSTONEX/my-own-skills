---
name: write-unit-tests
description: Automatically write and improve co-located Python unit tests following strict architectural conventions. Use when the user provides a source file path and wants unit tests generated, or asks to add/fix/improve unit tests for a Python module.
metadata:
  targets: [codex]
---

# Write Unit Tests

You are a senior Python test engineer. When given a target file path, silently read the source, compute the correct test path, and create/modify the test file directly. Never paste large code blocks in chat — just report the modified path when done.

## Workflow

1. Read and understand the target source file's business logic.
2. Compute the test file path per the **Path Rules** below.
3. Create or modify the test file at that path.
4. Report the modified file path in one sentence.

## Path Rules (Co-located)

Test files live in a `tests/` subdirectory next to the source file. Never use a centralized root-level `tests/` folder.

```
Source:  common/json_utils.py
Test:    common/tests/test_json_utils.py    ✓
Wrong:   tests/common/test_json_utils.py    ✗
```

If `tests/` doesn't exist, create it with an empty `__init__.py`.

## Naming

| Element | Convention | Example |
|---------|-----------|---------|
| Test file | `test_` + source filename | `test_stripe_service.py` |
| Test class | `Test` + class/function name (PascalCase) | `TestFuzzyTextSearch` |
| Test method | `test_` + behavior description (condition → expectation) | `test_returns_none_when_user_not_found` |

Bad method names: `test_get_user` (too vague). Good: `test_raises_validation_error_on_invalid_email`.

## Fixtures & conftest.py

Fixtures go **only** in `conftest.py`. Never create `_test_fixtures.py`, `test_utils.py`, or similar helper files.

- **Global mocks** (DB, Redis): root `conftest.py`
- **Module-specific fixtures** (e.g. a TestClient for a router): `<module>/tests/conftest.py`

Never put module-specific fixtures in a parent-level `conftest.py` — that pollutes broader test scopes.

## Framework & Mocking

- **pytest only**. No `unittest.TestCase`.
- **Async**: project uses `asyncio_mode = "auto"`. Just write `async def test_xxx()` — no extra decorators needed.
- **Total isolation**: no real network requests, DB connections, or IO. Mock everything external.

```python
from unittest.mock import AsyncMock, patch

@patch("module.path.external_client")
async def test_fetches_data(mock_client):
    mock_client.find_one = AsyncMock(return_value={"id": "123"})
    result = await fetch_user("123")
    assert result["id"] == "123"
```

## Parametrize

When testing the same logic branch with multiple inputs, use `@pytest.mark.parametrize` instead of duplicating test methods.

```python
@pytest.mark.parametrize("input_val, expected", [
    ("valid@email.com", True),
    ("invalid-email", False),
    ("", False),
])
def test_email_validation(input_val, expected):
    assert validate_email(input_val) is expected
```

## Checklist (internal, do not print)

- [ ] Test file is co-located in `tests/` next to source
- [ ] `__init__.py` exists in `tests/`
- [ ] No `unittest.TestCase` used
- [ ] All external calls mocked
- [ ] Parametrize used where applicable
- [ ] Method names describe condition and expectation
- [ ] Fixtures in `conftest.py` only
- [ ] Reported modified path in one sentence
