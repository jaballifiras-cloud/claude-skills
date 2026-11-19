# claude-skills

32 مهارة لـ Claude Code: البرمجة، مراجعة التطبيقات، البرمجيات المحاسبية، التحليل المالي والمحاسبة.

## التثبيت على أي جهاز (لكل المشاريع)

```bash
git clone https://github.com/jaballifiras-cloud/claude-skills.git ~/.claude/skills
```

للتحديث لاحقاً:

```bash
git -C ~/.claude/skills pull
```

## المهارات

| المجال | المهارات |
|---|---|
| المحاسبة والمالية | accounting-software-review, accounting-bug-fix, double-entry-accounting, financial-statement-analysis, financial-modeling, bank-reconciliation, audit-analytics |
| البرمجة والمراجعة | app-review, security-review, sql-database, code-review, debug-issue, write-tests, git-commit-pr, write-docs, python-data |
| Anthropic (Apache-2.0) | xlsx, pdf, docx, frontend-design, webapp-testing, mcp-builder, skill-creator |
| Vercel (MIT) | find-skills — البحث عن مهارات جديدة وتثبيتها عبر `npx skills`، web-design-guidelines — مراجعة الواجهات وإمكانية الوصول |
| Matt Pocock (MIT) | domain-modeling — قاموس المصطلحات وقرارات التصميم (ADR)، codebase-design — تصميم وحدات عميقة قابلة للاختبار، diagnosing-bugs — منهجية تشخيص الأخطاء الصعبة، tdd — التطوير بكتابة الاختبار أولاً |
| C# / .NET / MySQL (MIT) | csharp-async (github/awesome-copilot)، winforms-expert (dotnet/skills)، mysql (planetscale/database-skills) |

المهارات الرسمية من [anthropics/skills](https://github.com/anthropics/skills) مرخّصة بـ Apache License 2.0 (انظر LICENSE.txt داخل كل مجلد).

مهارة `find-skills` من [vercel-labs/skills](https://github.com/vercel-labs/skills) مرخّصة بـ MIT (انظر `find-skills/LICENSE.txt`).

مهارات `web-design-guidelines` من [vercel-labs/agent-skills](https://github.com/vercel-labs/agent-skills)، و`domain-modeling` و`codebase-design` و`diagnosing-bugs` و`tdd` من [mattpocock/skills](https://github.com/mattpocock/skills)، و`csharp-async` من [github/awesome-copilot](https://github.com/github/awesome-copilot)، و`winforms-expert` من [dotnet/skills](https://github.com/dotnet/skills)، و`mysql` من [planetscale/database-skills](https://github.com/planetscale/database-skills) (روابط المراجع فيها حُوّلت إلى الملفات المحلية في `references/`)، كلها مرخّصة بـ MIT (انظر LICENSE.txt داخل كل مجلد).
