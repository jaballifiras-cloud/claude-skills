---
name: accounting-bug-fix
description: Diagnose and fix bugs in accounting or financial software and data — unbalanced trial balance, wrong totals or taxes, rounding differences, reconciliation gaps, duplicated or missing postings, reports that don't match. Use when numbers are wrong, or the user says "الحسابات غير متوازنة" or "فيه فرق في المبالغ".
---

# Fixing accounting bugs

**Never "fix" numbers by editing posted data directly.** Find the cause in the code, fix the code, then correct the data with documented reversing/adjusting entries (or a reviewed, backed-up migration), with the user's approval.

## 1. Pin down the difference
- Get: which report/account, which period, expected amount, actual amount, and the difference.
- The size of the difference is a clue:
  - Tiny (0.01, 0.001): rounding — see step 3.
  - Exactly double an amount: a duplicated posting or a JOIN that duplicates rows.
  - Divisible by 9: transposed digits (e.g. 54 entered as 45) — a data entry problem.
  - Equal to one transaction: missing, duplicated, or posted to the wrong side/account.
  - Equal to a tax amount: tax posted to the wrong account or not posted.

## 2. Diagnostic queries (adapt names to the schema; read-only)
```sql
-- Unbalanced entries
SELECT entry_id, SUM(debit) AS d, SUM(credit) AS c, SUM(debit) - SUM(credit) AS diff
FROM journal_lines GROUP BY entry_id HAVING SUM(debit) <> SUM(credit);

-- Trial balance for a period
SELECT account_id, SUM(debit) - SUM(credit) AS balance
FROM journal_lines jl JOIN journal_entries je ON je.id = jl.entry_id
WHERE je.status = 'posted' AND je.posting_date BETWEEN :start AND :end
GROUP BY account_id;

-- Possible duplicates
SELECT reference, amount, posting_date, COUNT(*) FROM journal_entries
GROUP BY reference, amount, posting_date HAVING COUNT(*) > 1;

-- Lines without a valid entry/account (orphans)
SELECT jl.* FROM journal_lines jl LEFT JOIN accounts a ON a.id = jl.account_id WHERE a.id IS NULL;

-- Sub-ledger vs control account (example: receivables)
SELECT (SELECT SUM(amount_due) FROM invoices WHERE status = 'open') AS subledger,
       (SELECT SUM(debit) - SUM(credit) FROM journal_lines WHERE account_id = :ar_account) AS gl;
```
Bisect by period (year → month → day) and by account until the difference is isolated to specific transactions.

## 3. Rounding differences
- Check for float arithmetic on money and inconsistent rounding (per line vs per total, different modes in different places).
- Reproduce with a minimal example in a test; fix by using decimals and one rounding rule; allocate remainders explicitly.

## 4. Common root causes
- Report query JOIN multiplies rows before `SUM`; missing filter on status (drafts/cancelled included) or on company/tenant.
- Date boundary bugs: `BETWEEN` with timestamps excludes the last day; UTC vs local timezone shifting entries across months.
- Sign convention mixed up (credit stored as negative in one place, positive in another).
- Partial write: entry header saved but lines failed (no transaction) or retry created a duplicate.
- Cached balances not updated in the same transaction as the lines.
- Exchange rate applied twice, or at the wrong date.

## 5. Fix and prove it
1. Write a failing test that reproduces the wrong number.
2. Fix the code; the test passes; run the related test suite.
3. Prepare the data correction as reversing/adjusting entries (or a migration) — show it to the user first, take a backup, and never run it on production without explicit approval.
4. Re-run the diagnostic queries to show the difference is now zero.
5. Report: cause, code fix, data correction, verification.
