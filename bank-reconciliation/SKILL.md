---
name: bank-reconciliation
description: Reconcile bank statements with the general ledger, or match any two sets of transactions (invoices vs payments, supplier statements, card settlements) using Python/pandas or Excel. Use when the user asks for a bank reconciliation, matching payments to invoices, or "مطابقة بنكية" / "تسوية بنكية".
---

# Bank and transaction reconciliation

## 1. Load and normalise both sides
- Read the bank statement (CSV/Excel/PDF — use the `pdf` skill to extract tables) and the ledger export.
- Normalise: dates to `datetime`; amounts to `Decimal` or integer minor units with one sign convention (inflow positive); strip and upper-case references; remove thousands separators (watch `1.234,56` vs `1,234.56` formats).
- Keep the original row id on both sides for traceability.

## 2. Match in passes (strictest first), one-to-one
1. Exact: same amount, same reference, date within ±0 days.
2. Amount + date window (e.g. ±3 days) with unique candidates.
3. Reference/fuzzy text match (invoice numbers found inside the bank description; `rapidfuzz` for similarity) with equal amount.
4. Many-to-one / one-to-many: one bank deposit = several invoices (subset sum on small candidate sets), batch card settlements net of fees.
Remove matched rows after each pass so nothing is matched twice. Never auto-match ambiguous candidates — list them for review.

```python
import pandas as pd
def match_exact(bank, ledger, days=0):
    b = bank.assign(key=bank.amount_cents)
    l = ledger.assign(key=ledger.amount_cents)
    m = b.merge(l, on="key", suffixes=("_b", "_l"))
    m = m[(m.date_b - m.date_l).abs().dt.days <= days]
    # keep only unambiguous pairs
    m = m[~m.id_b.duplicated(keep=False) & ~m.id_l.duplicated(keep=False)]
    return m[["id_b", "id_l"]]
```

## 3. Reconciliation statement
```
Balance per bank statement
+ deposits in transit (in ledger, not yet in bank)
− outstanding cheques/payments (in ledger, not yet in bank)
± bank errors
= Adjusted bank balance

Balance per ledger
+ bank credits not yet recorded (interest, transfers received)
− bank charges, fees, returned cheques not yet recorded
± ledger errors
= Adjusted ledger balance
```
The two adjusted balances must be equal. If not, list the unexplained difference — do not plug it.

## 4. Output
- Matched pairs, unmatched bank items, unmatched ledger items, ambiguous candidates — as an Excel workbook with one sheet each (see the `xlsx` skill).
- Proposed journal entries for bank-only items (fees, interest) for the user to approve.
- Summary: counts and totals per category and the final difference.
