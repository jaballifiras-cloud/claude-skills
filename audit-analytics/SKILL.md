---
name: audit-analytics
description: Run data analytics to detect errors, anomalies, and possible fraud in accounting data — duplicate payments, Benford's law, round-number and weekend entries, unusual journal entries, gaps in invoice sequences, vendor/employee overlaps. Use when the user asks to audit, test, or look for anomalies in ledgers, payments, or invoices, or says "تدقيق" / "كشف الأخطاء والاحتيال".
---

# Audit analytics on accounting data

Findings are **indicators to investigate, not proof** of error or fraud. Report them neutrally with the supporting rows.

## Before testing
- Confirm completeness: record counts and totals of the extract match the trial balance/report for the same period.
- Document the data source, period, filters, and every test performed.

## Tests (pandas)
1. **Duplicate payments/invoices**: same vendor + amount + invoice number; also same vendor + amount within N days; normalised invoice numbers (strip spaces, leading zeros, `-`, `/`).
2. **Sequence gaps and duplicates** in invoice/cheque/entry numbers per series.
3. **Benford's law** on first digits of amounts (only meaningful for naturally occurring amounts spanning several orders of magnitude, ≥ ~500 records):
   ```python
   import numpy as np, pandas as pd
   s = df.amount.abs()
   s = s[s >= 10]
   first = s.astype(str).str.lstrip("0.").str[0].astype(int)
   observed = first.value_counts(normalize=True).sort_index()
   expected = pd.Series({d: np.log10(1 + 1 / d) for d in range(1, 10)})
   mad = (observed.reindex(range(1, 10), fill_value=0) - expected).abs().mean()
   # MAD (first digit): <0.006 close, 0.006–0.012 acceptable, 0.012–0.015 marginal, >0.015 nonconformity
   ```
4. **Journal entry testing**: entries posted on weekends/holidays or outside working hours, by unusual users, just before period end or after close, with round amounts (e.g. multiples of 1,000), with vague descriptions, directly to revenue/cash/sensitive accounts, or manual entries reversing soon after.
5. **Outliers**: z-score or IQR per account/vendor; amounts just below approval thresholds (threshold splitting).
6. **Master data**: vendors sharing bank account, address, or phone with employees or other vendors; vendors created and paid quickly then inactive; changes to vendor bank details shortly before payments.
7. **Payroll**: employees paid after termination, duplicate bank accounts, pay without attendance.
8. **Inventory**: negative stock, large adjustments, sales below cost.

## Output
For each test: purpose, population size, number of exceptions, total amount, and the exception rows (Excel sheet per test via the `xlsx` skill). Finish with a prioritised list of items to investigate. Handle personal data carefully and do not send it to external services.
