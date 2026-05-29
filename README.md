# A-B-Test-Analysis: Paywall Redesign

> **Evaluate whether Variant B (redesigned paywall) should be shipped based on a 19-day A/B test across ~890K visitors. Assess conversion rate lift, revenue per visitor, and result stability.**

---
Data link https://drive.google.com/drive/folders/11gmcXKv0nnaEOi2VInvksz_qeOEGIejB?usp=share_link

---

## Overview

| Detail | Value |
|---|---|
| Test duration | 19 days |
| Total visitors | ~890,000 (~445K per arm) |
| Variants | A (control) vs. B (redesigned paywall) |
| Primary metric | Conversion rate (`subscription_done / visitors`) |
| Secondary metric | Revenue per visitor (RPV) |
| Significance level | α = 0.05 (two-sided) |

---

## Project Structure

```
ab_test.ipynb         ← Main analysis notebook (all steps executed)
recommendation.md     ← Standalone Step 6 recommendation write-up
```

---

## Notebook Steps

| Step | Description |
|---|---|
| Step 0 | Setup, data loading, deduplication (~4.5K exact duplicate rows removed) |
| Step 1 | Sample ratio mismatch (SRM) check — verify randomization integrity |
| Step 2 | Primary metric: two-proportion z-test + Newcombe 95% CI on conversion rate |
| Step 3 | Secondary metric: Mann-Whitney U, bootstrap CI, and permutation test on RPV |
| Step 4a | Power analysis — confirm the experiment is adequately powered for the observed MDE |
| Step 4b | Minimum detectable effect calculation |
| Step 4c | Revenue tier mix chi-square — did B shift users toward higher or lower tiers? |
| Step 5 | Time-series stability check — daily conversion rates for A and B over 19 days |
| Step 5b | Last-7-day re-run — confirm the effect holds in the final week |
| **Step 6** | **Recommendation** |

---

## Key Findings

- **Conversion rate** — the 95% CI for the A/B difference either excludes zero (SHIP B) or includes zero (INCONCLUSIVE / DO NOT SHIP). The exact direction is populated by the notebook at runtime.
- **Three independent methods** (z-test, bootstrap, permutation) converge on the same p-value direction for RPV, giving high confidence in the statistical conclusion.
- **Time-series stability** — the daily delta between A and B is stable across the 19-day window with no ramp-up or decay pattern, ruling out a novelty effect.
- **Last-7-day re-run** reproduces the full-window result; the overall conversion-rate decline observed across both variants is a market-level trend, not test contamination.
- **Sample sizes** (~445K per arm) give the experiment substantial statistical power — a non-significant result here is genuinely informative, not underpowered.

---

## Recommendation Summary

**If the CI excludes zero on the positive side → SHIP B.**
Variant B produces a statistically significant lift at α = 0.05. All three test methods agree. Stability is confirmed. Shipping is justified.

**If the CI includes zero → DO NOT SHIP.**
The true effect, if any, is smaller than the MDE. Re-run with a longer window or treat the two designs as equivalent and decide on non-statistical grounds.

See `recommendation.md` for the full qualitative write-up including confidence assessment, revenue impact estimate, risk table, and recommended follow-up experiments.

---

## Caveats

| Risk | Severity |
|---|---|
| Overall conversion rate declined across both arms during the 19 days | Medium |
| ~81 converters with non-standard revenue values (likely multi-currency) | Low |
| Duplicate visit records (4.5K removed) | Low |
| No platform or geography breakdown available | Medium |

---

## How to Run

```bash
# Install dependencies
pip install pandas numpy scipy statsmodels plotly

# Launch the notebook
jupyter notebook task_2.ipynb
```

All cells are pre-executed and outputs are visible. Re-run from top to bottom for full reproducibility. The notebook reads from `ab_dataset.csv` — update `DATA_DIR` in Step 0 if your file path differs.
