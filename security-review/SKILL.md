---
name: security-review
description: Review code or an application for security vulnerabilities (OWASP Top 10) — injection, broken access control, authentication and session flaws, secrets in code, insecure configuration, vulnerable dependencies — and propose fixes. Use when the user asks for a security review/audit, or says "ثغرات" / "أمان التطبيق". For authorised review of the user's own code only.
---

# Security review

Review code the user owns or is authorised to assess. Report vulnerabilities with `file:line`, an exploitation scenario described at a high level, severity, and a fix. Do not write weaponised exploits.

## Checklist
1. **Access control** (most common critical issue): every endpoint/action checks authentication and authorisation server-side; no IDOR (changing an id in the URL/body gives access to another user's/company's data); multi-tenant queries always filtered by tenant; admin functions protected; mass assignment blocked (allow-list fields).
2. **Injection**: SQL built with string concatenation/f-strings/template literals → use parameterised queries/ORM bindings; OS command injection (`exec`, `system`, `shell=True`); NoSQL injection; LDAP/XPath; template injection.
3. **XSS**: unescaped output (`dangerouslySetInnerHTML`, `v-html`, `|safe`, `{!! !!}`, `innerHTML`); user content in attributes/URLs (`javascript:`); set a Content-Security-Policy.
4. **Authentication & sessions**: passwords hashed with bcrypt/argon2/scrypt (never MD5/SHA1/plain); rate limiting and lockout on login and OTP; secure session cookies (`HttpOnly`, `Secure`, `SameSite`); session rotation on login; JWT: verify signature and algorithm, expiry, don't store sensitive data in payload; password reset tokens single-use and expiring.
5. **CSRF** on cookie-authenticated state-changing requests.
6. **Secrets**: API keys, passwords, private keys in code, config, git history, logs, or frontend bundles. Use environment variables/secret managers; rotate any leaked secret.
7. **Sensitive data**: TLS everywhere; encryption at rest for highly sensitive data; no PII/financial data/tokens in logs or error messages; minimal data in API responses.
8. **File handling**: upload type/size validation, stored outside web root, random names; path traversal (`../`) on download/read; unsafe deserialization (`pickle`, `yaml.load`, Java/PHP `unserialize`) and XXE in XML parsers.
9. **SSRF**: server fetching user-supplied URLs → allow-list hosts, block internal IP ranges and cloud metadata.
10. **Configuration**: debug mode off in production, default credentials, permissive CORS (`*` with credentials), verbose error pages, security headers (HSTS, X-Content-Type-Options, frame-ancestors), directory listing.
11. **Dependencies**: run `npm audit` / `pip-audit` / `composer audit` / `bundle audit` / `dotnet list package --vulnerable` / `govulncheck`; flag outdated framework versions.
12. **Business logic**: negative quantities/amounts, race conditions on balances or coupons, skipping workflow steps (approve without review), price/total taken from the client instead of recomputed on the server.

## Output
Findings sorted by severity (Critical/High/Medium/Low), each with: location, vulnerability, impact, fix (code snippet). Then a short list of hardening recommendations. Never print real secret values found — show the file and the first few characters only, and recommend rotation.
