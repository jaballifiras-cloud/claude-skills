---
name: python-data
description: Analyse, clean, and visualise data with Python (pandas, numpy, matplotlib) from CSV, Excel, JSON, or databases, and write data scripts. Use when the user asks to analyse a dataset, compute statistics, make a chart, or clean data, or says "حلل البيانات".
---

# Python data analysis

## Setup
- Use the project's virtualenv if present (`.venv`, `poetry`, `uv`). Otherwise suggest `python -m venv .venv` and install only what is needed: `pip install pandas matplotlib openpyxl`.

## Workflow
1. **Load and inspect** before doing anything:
   ```python
   import pandas as pd
   df = pd.read_csv("data.csv")   # read_excel / read_json / read_sql
   print(df.shape); print(df.dtypes); print(df.head()); print(df.isna().sum())
   ```
2. **Clean** explicitly and report what changed: fix dtypes (`pd.to_datetime`, `pd.to_numeric(errors="coerce")`), handle missing values, drop duplicates, normalise text columns. Never silently drop rows — print how many were removed and why.
3. **Analyse** with vectorised pandas (`groupby`, `agg`, `pivot_table`, `merge`) instead of Python loops.
4. **Visualise** with matplotlib: one clear message per chart, labelled axes with units, a title, readable tick labels; save with `plt.savefig("chart.png", dpi=150, bbox_inches="tight")`.
5. **Report** key numbers in plain language, plus any caveats (missing data, small samples, outliers).

## Script quality
- Put reusable logic in functions; use `if __name__ == "__main__":` for scripts.
- Use `pathlib.Path` for file paths, type hints, and `argparse` for CLI inputs.
- Never modify the original data file; write outputs to a new file.
- For large files, use `usecols`, `dtype`, or `chunksize`.
