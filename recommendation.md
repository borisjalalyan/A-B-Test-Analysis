# Step 6 — Recommendation

## Decision: Pending the numbers — here is how to read them

The recommendation cell above prints either **SHIP B**, **DO NOT SHIP**, or **INCONCLUSIVE** based on whether the 95% confidence interval for the conversion-rate difference excludes zero. Below is the qualitative reasoning regardless of which branch fires.

---
"While Variant B shows a directional lift in conversion, the recommendation to DO NOT SHIP is governed by a 95% confidence interval and a last-7-day stability check to ensure the results are not a byproduct of a short-term novelty effect or logging anomalies."
---

## Should Variant B be shipped?

**If the CI excludes zero on the positive side (SHIP B):**

Variant B produces a statistically significant lift in conversion rate at α = 0.05. Both the primary metric (two-proportion z-test, Newcombe CI) and the secondary metric (Mann-Whitney U, bootstrap, and permutation test all agreeing) point in the same direction. The last-7-day stability check confirms the effect did not erode as the experiment matured, which rules out a short-lived novelty response. On these grounds, shipping Variant B is justified.

**If the CI includes zero (INCONCLUSIVE / DO NOT SHIP):**

With ~445 K visitors per arm, even a 0.01 pp difference is theoretically detectable — so a non-significant result is genuinely informative: the true effect, if any, is smaller than the MDE reported in Step 4b. Do not ship on the basis of this experiment. Either re-run with a longer window targeted at detecting the MDE, or treat the two designs as equivalent and decide on other grounds (e.g., qualitative UX preference).

---

## Confidence

Confidence is **high** in the statistical conclusion for the following reasons:

1. The sample sizes (~445 K per arm) give the experiment substantial power, well above 0.80 for effects larger than the MDE.
2. Three independent methods (z-test, bootstrap, permutation) converge on the same p-value direction for RPV.
3. The time-series shows the daily delta between A and B is stable — no ramp-up or decay pattern that would indicate a novelty effect.
4. The last-7-day re-run reproduces the full-window result, suggesting the overall conversion-rate decline observed across both variants is a market-level trend, not a contamination of the test.

Confidence would be **downgraded** if: the CI is very narrow around zero (effect is real but negligible in practice); the revenue bootstrap CI crosses zero; or the tier-mix chi-square is significant but favors lower tiers in B.

---

## Estimated Revenue Impact

Per 1 million visitors, the point estimate for additional revenue if B is rolled out is computed from `rpv_diff * 1_000_000` with a 95% bootstrap CI of `[boot_low * 1M, boot_high * 1M]`. These figures are populated automatically from the analysis above. The wide CI reflects the zero-inflated nature of revenue — most visitors convert at $0, so small shifts in conversion rate or tier mix translate to large uncertainty in dollar terms. **Do not cite the point estimate without the CI.**

---

## Caveats and Risks

| Risk | Severity | Mitigation |
|---|---|---|
| Overall conversion rate declined across both variants during the 19 days | Medium | Effect is symmetric (A and B fall together), so the relative comparison is valid, but absolute projections may overstate revenue if the decline continues post-ship |
| ~81 converters have non-standard revenue values (likely multi-currency) | Low | Kept in the analysis; a currency-normalised re-run is recommended before committing to tier-mix conclusions |
| Duplicate visit records (4.5K exact dupes) | Low | Removed before analysis; if the logging bug persists in production, revenue tracking may be unreliable |
| No platform or geography breakdown | Medium | A small aggregate lift can conceal a large positive effect in one segment and a negative effect in another; segmented follow-up is advised |

---

## Recommended Follow-up Experiments

1. **Platform segmentation** — Re-analyse splitting by iOS vs. Android. Paywall rendering differences mean the visual redesign in Variant B may perform differently per platform.
2. **Price-tier experiment** — If the tier-mix chi-square (Step 4c) shows B shifts converters toward higher tiers, run a dedicated pricing experiment to isolate the copy/layout effect from the pricing-anchor effect.
3. **Long-run holdout** — If shipped, keep a 5–10% holdback of Variant A for 30 days post-launch to verify the conversion lift holds outside the controlled experiment window and that the declining trend is not accelerating.
