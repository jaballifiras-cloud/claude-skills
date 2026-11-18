---
name: code-review
description: Review code changes (the current git diff, a branch, or named files) for bugs, security problems, and maintainability issues. Use when the user asks to review, check, or audit code, or says "راجع الكود".
---

# Code review

## Scope
1. Find what to review: `git diff` (unstaged), `git diff --staged`, or `git diff main...HEAD` for a branch. If the user named files, review those.
2. Read each changed file in full around the change, not just the diff hunk, so you understand callers and context.

## What to look for (in priority order)
1. **Correctness**: logic errors, off-by-one, wrong conditions, unhandled null/undefined/None, race conditions, missing `await`, wrong error handling.
2. **Security**: injection (SQL, shell, HTML/XSS), secrets in code, missing auth checks, unsafe deserialization, path traversal, unvalidated input.
3. **Data loss / breaking changes**: migrations, changed public APIs, removed fields.
4. **Tests**: is the new behaviour covered? Do existing tests still make sense?
5. **Maintainability**: duplication, unclear names, dead code, overly complex functions.

## Rules
- Only report problems you can point to with a concrete failing scenario. No vague style opinions.
- Verify before reporting: check the caller, the type, or run the code/test if possible.
- Respect the project's existing conventions (linter config, formatting, patterns).

## Output format
For each finding:
- **[severity: high/medium/low]** `path/to/file:line` — one-sentence problem
  - Scenario: input/state → what goes wrong
  - Fix: short suggestion (code snippet if helpful)

End with a one-line verdict: ready to merge / needs changes. Answer in the user's language.
