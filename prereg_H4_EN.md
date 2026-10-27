# Preregistration: H4 lower clamp (E1.4)

**Fixed date**: 2026-10-20 (results unseen)
**Hypothesis ID**: H4
**Status**: v0.2 draft. Supersedes `prereg_H4_clamp.md` (v0.1, kept for history).

## Purpose

Test whether raising the lower clamp β_min reduces variance and lowers the lock-in rate.

## Design

- Base cell: V=300, n_pairs=16, n_dist=32, d_mem ∈ {16, 32}, seed ∈ {0,1,2,3,4}.
- β_min ∈ {0, 0.25·mean, 0.5·mean}.
- **Definition of mean**: the measured mean β from the **β_min=0 run** for each seed × d_mem. The 0.25×/0.5× levels use the same absolute values across conditions.
- **t=0 is excluded from the mean**: at t=0, e_prev=0 → k=0 → β=η/ε≈1e6, an meaningless spike that corrupts the mean. The t≥1 mean is 0.111. The mean is computed from t≥1 only.
- Total steps: same as E1.1 (1500).
- lock-in definition: acc < k × chance. k is fixed after the H1 Day 5 pilot and applied before H4's main runs.

## Primary endpoints (two endpoints, conjunction)

1. **Variance endpoint**
   - Brown–Forsythe (median-centered) as an omnibus test across the 3 levels.
   - Linear trend: regress absolute deviations from each level's median on the ordinal level (0,1,2); one-sided test for a negative slope (α=0.05).
   - The primary decision uses the linear trend. Brown–Forsythe is reported as omnibus support.
2. **lock-in endpoint**
   - Cochran–Armitage trend test (3 levels, ordinal scores 0/1/2) for a decrease in lock-in rate with increasing β_min, one-sided (α=0.05).
   - Pairwise Fisher tests are reported descriptively (not used for the primary decision).

- **H4 holds** = **both** the variance trend (negative) and the lock-in trend (negative) are significant at α=0.05.
- **Position tests (mean acc) are prohibited**: t-test / ANOVA / Mann–Whitney are not used. A mean difference is not the clamp's predicted effect.
- Brown–Forsythe is an **omnibus sensitivity** check, Fisher is a **2×2 sensitivity** check (C0 vs C1 / C0 vs C2, Holm m=2). Neither is used for the primary decision.

## Collapse 0 case (pre-registered rule)

- If collapse is 0 in all levels, Cochran–Armitage is degenerate (p=1) and the conjunction does not hold.
- In that case, "no collapse" is **not** treated as p=1; it is reported as a **separate floor-effect result**.
- H4's decision switches to the **variance-trend-only secondary mode**. The lock-in endpoint is marked "not evaluable (0 in all levels)" and is not counted toward H4.
- When the secondary mode is triggered, `master_log.md` records **"H4 secondary mode activated"**.
- Reporting distinguishes "H4 primary conjunction not established; lock-in unmeasurable due to floor effect; variance trend reported separately" from "H4 not supported".
- If any level has collapse >0, the usual conjunction (variance + CA) is used.

## Statistics

- Primary: `stats.linear_trend_dispersion` (variance, one-sided negative trend) + `stats.cochran_armitage_trend` (lock-in, one-sided negative trend).
- Sensitivity: `stats.brown_forsythe` (omnibus), `stats.fisher_exact_test` (2×2, C0 vs C1 / C0 vs C2, Holm m=2).
- Position tests (t / ANOVA / Mann–Whitney) are prohibited.
- The two endpoints form a conjunction, so no multiplicity correction is applied (each at α=0.05).

## Interpretation limits

- β_min is treated as an ordinal scale. Equal spacing between levels is not assumed (scores 0/1/2 are ordinal only).
- Variance reduction and lock-in reduction are different aspects; H4 does not hold if only one is significant.
- Small-scale MQAR only.

## Result fields (blank, frozen)

- mean β (β_min=0):
- Variance: Brown–Forsythe p, trend slope, one-sided p
- lock-in: rates (3 levels), CA z, one-sided p
- Decision:
