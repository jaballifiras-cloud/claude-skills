---
name: app-review
description: Do a full review of an application or codebase — architecture, code quality, correctness, security, performance, data handling, tests, dependencies, and deployment — and produce a prioritised report. Use when the user asks to review/evaluate/audit a whole app or project (not just a diff), or says "راجع التطبيق" / "قيّم المشروع".
---

# Full application review

## 1. Map the project (before judging)
- Read README, manifests (`package.json`, `pyproject.toml`, `composer.json`, `pom.xml`, `*.csproj`, `go.mod`), Docker/CI files, env examples.
- Identify: language/framework versions, entry points, main modules, data stores, external services, auth method, how it's built, tested, and deployed.
- Try to install, build, run tests, and run the linter. Record what works and what fails (with output).
- Write a short architecture summary (components and data flow) for the report.

## 2. Review areas
1. **Correctness**: core business flows end to end; error handling; edge cases; transactions around multi-step writes; time zones; money (if financial, also apply the `accounting-software-review` skill).
2. **Security**: apply the `security-review` skill.
3. **Data**: schema design, constraints, indexes, migrations, backups, data validation at boundaries; see `sql-database`.
4. **Performance**: N+1 queries, missing indexes, unbounded queries/pagination, heavy work in request path, memory leaks, caching, bundle size for frontends.
5. **Code quality**: module boundaries, duplication, very long functions/files, dead code, naming, consistent patterns, type safety, lint warnings.
6. **Tests**: what is covered (critical flows?), do tests pass, flaky tests, test speed.
7. **Dependencies**: outdated or vulnerable packages (`npm audit`, `pip-audit`, `composer audit`, `dotnet list package --vulnerable`), abandoned libraries, unpinned versions, licenses.
8. **Operations**: config via environment, secrets management, logging (structured, no secrets/PII), monitoring/health checks, error reporting, Docker/CI quality, reproducible builds.
9. **UX/accessibility** (for UIs): broken flows, validation messages, loading/error states, keyboard and screen-reader basics, responsive layout, i18n/RTL support if Arabic is needed.

## 3. Rules
- Every finding cites `file:line` (or a command output) and a concrete impact. No generic advice without evidence.
- Verify before reporting; mark uncertain items as "to confirm".
- Don't change code during the review unless the user asks; offer fixes afterwards.

## 4. Report
1. Executive summary: overall state, top 3 risks, top 3 strengths.
2. Findings table: severity (Critical/High/Medium/Low), area, location, problem, impact, recommended fix, effort (S/M/L).
3. Quick wins (high value, small effort).
4. Roadmap: what to fix now, next, later.
Write in the user's language; keep code identifiers as-is. For a shareable report, use the `docx` or `pdf` skill if the user wants a file.
