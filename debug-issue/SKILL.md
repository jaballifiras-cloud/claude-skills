---
name: debug-issue
description: Systematically find and fix the root cause of a bug, error message, failing test, or unexpected behaviour. Use when the user reports an error, a stack trace, a crash, or says something "doesn't work" / "فيه خطأ".
---

# Debugging

1. **Reproduce first.** Get the exact command, input, and error output. Run it yourself. If you cannot reproduce, say so and ask for the missing detail.
2. **Read the error carefully.** Go to the top frame that is in the project's own code (not library code). Read that file around the line.
3. **Form a hypothesis** about the root cause and state it in one sentence.
4. **Test the hypothesis** with the smallest check: a print/log, a REPL call, a focused test, `git log -p` / `git bisect` for regressions.
5. **Fix the root cause**, not the symptom. Avoid try/except or `?.` that just hides the error.
6. **Add a regression test** that failed before the fix and passes after.
7. **Verify**: re-run the original reproduction and the related tests.
8. **Report**: cause (one or two sentences), the fix, how it was verified. Remove any temporary debug logs.

Do not guess-and-change multiple things at once. Change one thing, observe, repeat.
