---
name: ml-patterns
description: ML engineering patterns for forecasting and DSML pipelines — feature engineering, strict train/inference separation, time series cross-validation, model evaluation metrics (RMSE/MAE/MAPE/SMAPE), experiment tracking, and anti-patterns (data leakage, future leakage). Use when designing or implementing ML models, forecasting pipelines, feature stores, model evaluation, experiment runs, or data science workflows.
---

# ML Patterns — Forecasting & DSML

## Quick Start

Every ML task must answer three questions before code is written:
1. **What is the prediction target and horizon?** (e.g. daily volume, 14-day horizon)
2. **Where is the train/validation/test split?** (time-based — never random)
3. **What metric defines success?** (pick one primary metric per task)

---

## Workflow

### 1. Pipeline Separation (non-negotiable)

Always separate training and inference into distinct code paths:

```
training pipeline:   raw data → features → fit → serialise model artefact
inference pipeline:  raw data → features → load artefact → predict → output
```

- Features must be computed identically in both paths — extract to a shared `features.py`
- Never fit scalers/encoders on test data; fit on train, transform both

### 2. Time Series Splits

**Never use random splits on time series.** Use forward-chaining (walk-forward) validation:

```python
# Good — respects temporal order
from sklearn.model_selection import TimeSeriesSplit
tscv = TimeSeriesSplit(n_splits=5, gap=horizon)

# Bad — leaks future into past
train_test_split(X, y, random_state=42)
```

Rules:
- Include a `gap` equal to the forecast horizon between fold train/test to avoid leakage
- Evaluate on the last N periods as a held-out test set (never touched during tuning)
- Use the same split logic in production as in evaluation

### 3. Evaluation Metrics

| Metric | Use when | Pitfall |
|---|---|---|
| RMSE | Penalise large errors | Scale-dependent |
| MAE | Robust to outliers | Ignores error magnitude |
| MAPE | Interpretable % | Blows up near zero |
| SMAPE | Symmetric %, handles zero | Asymmetric on over/under |
| Bias | Systematic over/under-forecast | Must be near zero in production |

Always report **both** a scale-sensitive (RMSE/MAE) and scale-free (MAPE/SMAPE) metric.

### 4. Feature Engineering Rules

- **No future leakage.** Any feature computed at time `t` must use only data available at `t`
- **Lag features** are safe: `lag_7`, `lag_14`, `rolling_mean_28`
- **Calendar features**: day-of-week, week-of-year, is_holiday, is_month-end
- **Target encoding** must be fitted on train only and applied to validation/test
- Document the feature window: `lag_7` assumes 7 days of history are always available

### 5. Experiment Tracking

Every training run must log:
- Input dataset version / date range
- Hyperparameters (full config, not just changed params)
- Train/validation metrics (all reported metrics)
- Model artefact path
- Random seed

Use MLflow, DVC, or equivalent — no bare `print()` logging for experiments.

---

## Anti-Patterns

- ❌ Fitting preprocessing on the full dataset before splitting
- ❌ Using `iloc[-test_size:]` without ensuring no future features leak back
- ❌ Reporting only the metric that looks best
- ❌ Hardcoding the forecast horizon in feature computation
- ❌ Retraining in the inference path
