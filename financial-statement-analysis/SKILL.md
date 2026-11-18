---
name: financial-statement-analysis
description: Analyse financial statements (income statement, balance sheet, cash flow) — ratio analysis, trends, common-size, DuPont, working capital, cash flow quality, and red flags — and write a clear report. Use when the user shares financial statements, a trial balance, or asks for financial analysis or "تحليل مالي" / "النسب المالية".
---

# Financial statement analysis

## 1. Prepare the data
- Collect at least 2–3 periods (more for trends). Note currency, units (thousands/millions), fiscal year end, and accounting standard.
- Check consistency first: balance sheet balances; net profit links to retained earnings; cash at end of cash flow = cash on balance sheet. Report any break.
- Use averages of opening and closing balances for turnover ratios when two balance sheets are available.

## 2. Ratios
**Liquidity**
- Current ratio = current assets / current liabilities
- Quick ratio = (current assets − inventory − prepaid) / current liabilities
- Cash ratio = cash & equivalents / current liabilities

**Profitability**
- Gross margin = gross profit / revenue
- Operating (EBIT) margin = EBIT / revenue; EBITDA margin = EBITDA / revenue
- Net margin = net profit / revenue
- ROA = net profit / average total assets
- ROE = net profit / average equity
- ROCE = EBIT / (total assets − current liabilities)

**Efficiency (days use 365)**
- DSO = average receivables / revenue × 365
- DIO = average inventory / COGS × 365
- DPO = average payables / COGS (or purchases) × 365
- Cash conversion cycle = DSO + DIO − DPO
- Asset turnover = revenue / average total assets

**Leverage and solvency**
- Debt-to-equity = interest-bearing debt / equity
- Net debt = interest-bearing debt − cash; Net debt / EBITDA
- Interest coverage = EBIT / interest expense
- Equity ratio = equity / total assets

**Cash flow**
- Operating cash flow / net profit (quality of earnings; persistently < 1 is a warning)
- Free cash flow = operating cash flow − capital expenditure
- FCF margin = FCF / revenue

**DuPont**: ROE = net margin × asset turnover × equity multiplier (average assets / average equity).

## 3. Other techniques
- Horizontal (trend) analysis: % change year over year per line.
- Vertical (common-size): every income statement line as % of revenue; every balance sheet line as % of total assets.
- Compare with the company's history and, when available, industry benchmarks; ratios mean little in isolation.

## 4. Red flags
- Revenue growing much faster than cash from operations; receivables growing faster than revenue (DSO rising).
- Inventory growing faster than sales; falling gross margin.
- Negative operating cash flow for several periods while reporting profits.
- Rising debt with falling interest coverage; short-term debt funding long-term assets.
- Large or recurring "one-off" items, frequent changes in accounting policies, qualified audit opinion.

## 5. Python template
```python
import pandas as pd
def safe_div(a, b):
    return a / b.where(b != 0)
# df: one row per year, columns from the statements
r = pd.DataFrame(index=df.index)
r["current_ratio"] = safe_div(df.current_assets, df.current_liabilities)
r["gross_margin"] = safe_div(df.gross_profit, df.revenue)
r["net_margin"] = safe_div(df.net_profit, df.revenue)
avg_eq = (df.equity + df.equity.shift(1)) / 2
r["roe"] = safe_div(df.net_profit, avg_eq)
r["dso"] = safe_div((df.receivables + df.receivables.shift(1)) / 2, df.revenue) * 365
r["ocf_to_net_profit"] = safe_div(df.operating_cash_flow, df.net_profit)
print(r.round(3).T)
```
For Excel output, use the `xlsx` skill and write formulas instead of hard-coded results.

## 6. Report structure
1. Summary (3–5 bullets: overall health, main strength, main risk).
2. Profitability · Liquidity · Efficiency · Leverage · Cash flow — each with the key ratios in a table, the trend, and what drives it.
3. Red flags and questions for management.
4. Recommendations.
State the data source, assumptions, and that this is analysis, not investment advice. Answer in the user's language.
