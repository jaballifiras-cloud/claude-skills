---
name: git-commit-pr
description: Create clean git commits and pull requests with clear messages. Use when the user asks to commit, write a commit message, create a branch, push, or open a PR, or says "اعمل commit".
---

# Git commits and pull requests

## Before committing
1. `git status` and `git diff` to see exactly what changed.
2. Never commit secrets (.env, keys, tokens), build output, or large binaries. Check `.gitignore`.
3. If on the default branch (main/master) and the user plans a PR, create a branch first: `git switch -c type/short-description`.
4. Group unrelated changes into separate commits. Stage specific files (`git add path`), not blindly `git add -A`.

## Commit message format (Conventional Commits)
```
type(scope): short imperative summary (max ~72 chars)

Why the change was needed and what it does, wrapped at 72 chars.
Mention breaking changes and related issue numbers.
```
Types: feat, fix, docs, refactor, test, chore, perf, ci, build.
Follow the repo's existing style if `git log --oneline -20` shows a different convention.

## Pull requests
- Title: same style as the main commit.
- Body: **What** changed, **Why**, **How to test**, and screenshots for UI changes.
- If `.github/pull_request_template.md` exists, fill in its sections.
- Use `gh pr create --draft` when the `gh` CLI is available.

## Safety
- Never `git push --force` to a shared branch, never rewrite published history, and never run `git reset --hard` / `git clean -fd` without the user's explicit OK.
- Ask before pushing if the user did not request it.
