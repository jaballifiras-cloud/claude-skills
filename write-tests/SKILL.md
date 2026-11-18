---
name: write-tests
description: Write or improve automated tests for a function, module, or feature using the project's existing test framework. Use when the user asks to add tests, increase coverage, or test a change, or says "اكتب اختبارات".
---

# Writing tests

## 1. Detect the setup
- JS/TS: look at `package.json` for vitest, jest, mocha, playwright; find existing `*.test.*` / `*.spec.*` files.
- Python: pytest (`pytest.ini`, `pyproject.toml`, `conftest.py`) or unittest.
- Go: `*_test.go`; Rust: `#[cfg(test)]`.
- Follow the existing file location, naming, fixtures, and assertion style. Do not add a new framework unless none exists (then ask first).

## 2. Decide what to test
- The happy path.
- Edge cases: empty input, boundaries, large values, unicode, null/None.
- Error paths: invalid input, failing dependencies, exceptions raised.
- Regressions: if fixing a bug, first write a test that fails because of the bug.

## 3. Write good tests
- One behaviour per test; descriptive names (`returns_empty_list_when_no_items`).
- Arrange / Act / Assert structure.
- Test public behaviour, not private implementation details.
- Mock only external boundaries (network, time, filesystem, randomness). Prefer real objects otherwise.
- Keep tests deterministic: fixed seeds, frozen time, no reliance on order.

## 4. Run them
- Run only the new/affected tests first, then the full suite if it is fast.
- Report honestly: which tests pass, which fail, and the failure output. Never claim tests pass without running them.
