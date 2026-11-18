---
name: write-docs
description: Write or update a README, project documentation, code comments, docstrings, or a CHANGELOG. Use when the user asks to document code or a project, or says "اكتب توثيق" / "README".
---

# Documentation

## README structure
1. Project name and one-sentence description.
2. Features (short bullet list).
3. Requirements (language/runtime versions, services).
4. Installation — exact copy-pasteable commands.
5. Usage — a minimal working example, then common options.
6. Configuration — environment variables in a table (name, required, default, description). Never include real secret values.
7. Development — how to run tests, lint, and build.
8. License.

## Rules
- Verify every command and path against the actual repo (package.json scripts, Makefile, pyproject). Do not invent commands.
- Short sentences, concrete examples, no marketing language.
- Code comments explain **why**, not what the code obviously does.
- Docstrings: follow the existing style (JSDoc, Google/NumPy docstrings, etc.) with params, return value, and raised errors.
- CHANGELOG: follow Keep a Changelog (Added / Changed / Fixed / Removed) under a version heading.
- Write in the language the user asks for; keep code identifiers in English.
