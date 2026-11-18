---
name: financial-modeling
description: Build financial models and calculations — budgets and forecasts, variance analysis, break-even, NPV/IRR/payback, DCF valuation, loan amortisation, scenario and sensitivity analysis — in Python or Excel. Use when the user asks for a forecast, budget, feasibility study, valuation, loan schedule, or "دراسة جدوى" / "ميزانية تقديرية".
---

# Financial modeling

## Principles
- Separate **inputs/assumptions** (one clearly labelled place) from **calculations** and **outputs**. No hard-coded numbers inside formulas.
- Every assumption has a unit, a source or rationale, and a period.
- Make periods explicit (monthly/annual) and keep sign conventions consistent (cash out negative).
- Build checks: balance sheet balances, cash never silently negative, sums of parts equal totals.
- In Excel, use formulas (see the `xlsx` skill) so the model stays live; colour inputs differently from formulas.

## Key formulas
- **NPV** = Σ CFₜ / (1 + r)ᵗ for t = 0..n (CF₀ usually the negative investment). Excel `NPV()` discounts the first value too — use `=CF0 + NPV(r, CF1:CFn)`.
- **IRR**: rate where NPV = 0. Use XNPV/XIRR for irregular dates. Beware multiple IRRs when cash flows change sign more than once.
- **Payback**: time until cumulative cash flow ≥ 0; discounted payback uses discounted flows.
- **Break-even units** = fixed costs / (price − variable cost per unit); break-even revenue = fixed costs / contribution margin ratio.
- **Loan payment (annuity)** = P × r / (1 − (1 + r)^−n), r = periodic rate, n = number of periods. Each period: interest = balance × r; principal = payment − interest.
- **DCF**: Enterprise value = Σ FCFₜ/(1+WACC)ᵗ + terminal value/(1+WACC)ⁿ; terminal value (Gordon) = FCFₙ₊₁ / (WACC − g), requires g < WACC. Equity value = EV − net debt.
- **WACC** = E/(D+E) × Re + D/(D+E) × Rd × (1 − tax rate).
- **CAGR** = (end / start)^(1/years) − 1.

## Python helpers
```python
import numpy as np
from decimal import Decimal, ROUND_HALF_UP

def npv(rate, cashflows):          # cashflows[0] at t=0
    return sum(cf / (1 + rate) ** t for t, cf in enumerate(cashflows))

def irr(cashflows, lo=-0.99, hi=10.0, tol=1e-10):
    f = lambda r: npv(r, cashflows)
    if f(lo) * f(hi) > 0:
        raise ValueError("No sign change: IRR not found in range")
    for _ in range(300):           # bisection: robust
        mid = (lo + hi) / 2
        if f(lo) * f(mid) <= 0: hi = mid
        else: lo = mid
        if hi - lo < tol: break
    return (lo + hi) / 2

def amortization(principal, annual_rate, years, per_year=12, places="0.01"):
    """Schedule in Decimal; every row is rounded and principal sums exactly to the loan."""
    q = lambda x: Decimal(x).quantize(Decimal(places), ROUND_HALF_UP)
    P, r, n = Decimal(str(principal)), Decimal(str(annual_rate)) / per_year, years * per_year
    pay = q(P * r / (1 - (1 + r) ** -n)) if r else q(P / n)
    bal, rows = P, []
    for k in range(1, n + 1):
        interest = q(bal * r)
        princ = bal if k == n else pay - interest   # last payment absorbs rounding
        bal -= princ
        rows.append((k, princ + interest, interest, princ, bal))
    return rows
```

## Budgeting and variance analysis
- Variance = actual − budget; mark favourable/unfavourable (for costs, a negative variance is favourable).
- Split variances: price vs volume (sales), rate vs efficiency (labour), spending vs volume (overheads).
- Flexible budget: restate the budget at actual volume before comparing.

## Scenarios and sensitivity
- Base / optimistic / pessimistic scenarios with a clear list of changed assumptions.
- Sensitivity tables (e.g. NPV against discount rate × growth) and a tornado ranking of the most influential inputs.

## Output
Summarise the key results (NPV, IRR, payback, break-even), the main assumptions, and the risks. Say plainly that results depend on the assumptions and are not financial advice.
