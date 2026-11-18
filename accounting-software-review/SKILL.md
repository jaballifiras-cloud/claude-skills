---
name: accounting-software-review
description: Review the code of accounting, ERP, invoicing, payroll, POS, or any money-handling software for financial correctness, data integrity, audit-trail, and security problems. Use when the user asks to review, audit, or check accounting/financial software or code that stores or computes money, or says "راجع البرنامج المحاسبي".
---

# Reviewing accounting and financial software

Read the data model (schema, migrations, models) first, then the code that creates postings, invoices, payments, and reports. Report findings with `file:line`, a concrete failing scenario (input → wrong number), severity, and a fix. Answer in the user's language.

## 1. Money representation (most common source of bugs)
- **No binary floating point for money.** Flag `float`/`double`/JS `Number` arithmetic on amounts (`0.1 + 0.2 != 0.3`). Use integer minor units (cents/millimes) or a decimal type: Python `decimal.Decimal`, Java `BigDecimal`, C# `decimal`, JS `decimal.js`/`big.js`/`dinero.js`, SQL `NUMERIC(p,s)` / `DECIMAL`, never SQL `FLOAT`/`REAL`.
- Python: `Decimal("0.1")` from strings, not `Decimal(0.1)` from floats.
- Scale per currency: most currencies use 2 decimals, but e.g. TND, KWD, BHD, OMR, JOD, LYD, IQD use 3, and JPY uses 0. Don't hard-code 2.
- Each amount must be stored with its currency code (ISO 4217). Never add amounts in different currencies.

## 2. Rounding
- Rounding mode must be explicit and consistent (e.g. `ROUND_HALF_UP` or banker's `ROUND_HALF_EVEN`, whichever the business/legal rules require).
- Decide where rounding happens: per line vs per document total. Tax computed per line then summed can differ from tax on the total — check which one the local tax rules require and that invoices, reports, and tax returns use the same method.
- Allocations/splits (instalments, cost allocation, discounts across lines) must distribute the remainder so parts sum exactly to the total (largest-remainder method).

## 3. Double-entry integrity
- Every journal entry: sum(debits) == sum(credits), checked in code **and** ideally enforced in the database (constraint, trigger, or a transactional service that is the only writer).
- An entry and all its lines must be written in **one database transaction**.
- Each line hits exactly one side (debit or credit, or one signed amount with a clear convention) and a valid, active, posting-level account.
- Trial balance must always net to zero; account balances should be derivable from lines (cached balances must be updated in the same transaction).

## 4. Immutability and audit trail
- Posted entries/invoices must not be edited or hard-deleted. Corrections are made with reversing entries or credit notes.
- Look for `UPDATE`/`DELETE` on posted journal lines, invoices, or payments; soft deletes that hide records from reports.
- Audit log: who, when, what changed (old/new values), for master data (accounts, customers, prices, tax rates) and documents.
- Invoice numbering: sequential, gap-free where the law requires, per series/fiscal year, generated safely under concurrency (DB sequence or row lock, not `MAX(id)+1`).

## 5. Periods and dates
- Closed/locked periods must reject new postings and edits (check every write path, including imports and APIs).
- Distinguish document date, posting date, and created-at timestamp. Store timestamps in UTC; derive the accounting date in the company's timezone (an entry at 23:30 local time must not land in the next day/month).
- Fiscal year may not equal calendar year.

## 6. Concurrency and idempotency
- Double-click / retry must not create duplicate payments or postings: idempotency keys, unique constraints, or state checks inside a transaction.
- Stock quantities, balances, and counters: atomic updates (`UPDATE ... SET qty = qty - :n WHERE qty >= :n`) or row locks, not read-modify-write in application code.
- Webhooks from payment providers: verify signatures, handle duplicates and out-of-order delivery.

## 7. Tax and invoicing
- Tax rates come from configuration with validity dates, not hard-coded; historical documents keep the rate used at that time.
- Check rounding (see §2), exempt/zero-rated handling, reverse charge, withholding tax, stamp duties if applicable.
- Credit notes reference the original invoice and reverse tax correctly.

## 8. Multi-currency
- Store the exchange rate and its date on each transaction; book in functional currency and keep the original amount.
- Realised vs unrealised FX gains/losses on settlement and revaluation.

## 9. Reports
- Balance sheet balances (Assets = Liabilities + Equity), trial balance nets to zero, sub-ledgers (AR, AP, inventory) reconcile to their control accounts.
- Report queries filter by posting date and status (exclude drafts/cancelled), and don't double-count due to JOINs (check for one-to-many joins before `SUM`).

## 10. Security and controls
- Authorisation on every endpoint (not only hidden buttons): who may post, approve, reverse, close periods, change tax rates.
- Segregation of duties: creator ≠ approver for payments where required.
- SQL injection, IDOR (accessing another company's invoices by changing an ID), multi-tenant data isolation (every query filtered by company/tenant).
- Personal and banking data (IBAN, salaries) protected and not logged.

## 11. Tests to ask for / write
- Property test: random valid transactions → trial balance always zero.
- Rounding edge cases (x.xx5), 3-decimal currencies, large amounts, negative amounts/credit notes.
- Period lock, duplicate submission, concurrent stock decrement.

## Output
Group findings by severity (Critical: wrong money or data loss; High: integrity/security; Medium: controls/maintainability; Low). End with the top 3 actions to take first.
