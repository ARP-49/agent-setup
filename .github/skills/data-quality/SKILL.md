---
name: data-quality
description: Data quality patterns for ML and forecasting pipelines — schema validation (pandera), null/missing value handling, distribution checks, outlier detection, time series integrity (gaps, duplicates, monotonicity), and data drift detection. Use when validating input data, writing data pipeline tests, designing ingestion steps, or debugging unexpected model behaviour caused by data issues.
---

# Data Quality — ML & Forecasting Pipelines

## Quick Start

Every data ingestion boundary needs three checks before the data moves downstream:

1. **Schema** — correct columns, types, no unexpected nulls
2. **Integrity** — no duplicates, no gaps (time series), correct range
3. **Distribution** — values within expected bounds, no anomalous drift

---

## Workflow

### 1. Schema Validation (pandera)

Validate at every pipeline entry point — never assume upstream is clean:

```python
import pandera as pa
from pandera import Column, DataFrameSchema, Check

schema = DataFrameSchema({
    "date":     Column(pa.DateTime, nullable=False),
    "volume":   Column(pa.Float,    checks=Check.greater_than_or_equal_to(0)),
    "location": Column(pa.String,   nullable=False),
})

validated_df = schema.validate(df)  # raises SchemaError on failure
```

Rules:
- Define schemas in `schemas/` as reusable constants — not inline
- Validate at source load, not deep in the pipeline
- Write a pytest fixture that exercises the schema with known-bad data

### 2. Time Series Integrity Checks

```python
def assert_no_gaps(df: pd.DataFrame, freq: str, date_col: str = "date") -> None:
    """Raise if expected time steps are missing."""
    full_range = pd.date_range(df[date_col].min(), df[date_col].max(), freq=freq)
    missing = full_range.difference(df[date_col])
    assert len(missing) == 0, f"Missing {len(missing)} periods: {missing[:5]}"

def assert_no_duplicates(df: pd.DataFrame, keys: list[str]) -> None:
    dupes = df.duplicated(subset=keys, keep=False)
    assert not dupes.any(), f"{dupes.sum()} duplicate rows on {keys}"
```

Checklist for every time series dataset:
- [ ] No duplicate (date, entity) pairs
- [ ] No gaps at the expected frequency (daily, weekly, etc.)
- [ ] Timestamps are timezone-aware or consistently naive
- [ ] Date column is sorted ascending before feature engineering

### 3. Distribution & Outlier Checks

```python
def assert_within_bounds(series: pd.Series, lo: float, hi: float) -> None:
    out = series[(series < lo) | (series > hi)]
    assert out.empty, f"{len(out)} values outside [{lo}, {hi}]: {out.head()}"

def assert_no_nulls(df: pd.DataFrame, cols: list[str]) -> None:
    null_counts = df[cols].isnull().sum()
    bad = null_counts[null_counts > 0]
    assert bad.empty, f"Nulls found:\n{bad}"
```

Flag (do not silently drop) outliers beyond ±4σ or domain-defined limits.

### 4. Data Drift Detection

Compare incoming data distribution against a reference baseline:

```python
from scipy.stats import ks_2samp

def check_drift(reference: pd.Series, current: pd.Series, threshold: float = 0.05) -> bool:
    stat, p_value = ks_2samp(reference, current)
    if p_value < threshold:
        raise ValueError(f"Drift detected: KS p={p_value:.4f} < {threshold}")
    return True
```

Run drift checks on:
- Target variable distribution
- Key features (volume, price, date range coverage)
- Null rate per column (sudden spike = upstream issue)

### 5. Test Patterns

Every data quality function must have a pytest test with:
- A passing case (clean data)
- A failing case (known-bad data triggers the assertion)

```python
def test_assert_no_gaps_raises_on_missing_date():
    df = pd.DataFrame({"date": pd.to_datetime(["2024-01-01", "2024-01-03"])})
    with pytest.raises(AssertionError, match="Missing"):
        assert_no_gaps(df, freq="D")
```

---

## Anti-Patterns

- ❌ Silently dropping rows with nulls without logging how many
- ❌ Schema validation only in notebooks, never in the pipeline
- ❌ Assuming upstream data is always complete — validate every run
- ❌ Using `fillna(0)` on volume data without understanding the business meaning
- ❌ Skipping drift checks in retraining pipelines
