# Preregistration: H1 load ratio

**Fixed date**: 2026-10-20 (results unseen)

**Stage 1 frozen variant**: keynorm = normalized key direction + raw-key beta
(β = 1/‖k_raw‖²), i.e. the KLA Table 5 "key-norm only" ablation.
The exact raw-key KPL (β = η/(‖k‖²+ε), key_norm=False) is implemented and
unit-tested (T1–T5) but is **not** the Stage 1 arm. The "norm key + norm beta"
reading (β = η/(‖k_norm‖²+ε) ≈ η) is also implemented (`--variant normbeta`) but
is **not** the Stage 1 arm. This distinction is frozen before the E1.1 runs.

V = 300
chance = 1/300 = 0.003333...
n_special = 3
Q = 2000
collapse threshold = k × chance (k fixed after the Day 5 pilot)

## Purpose

Freeze the H1 (load ratio) experiment scale and chance baseline.

## Prerequisites

- n_max = 128 (320-run scale)
- assert: V >= 2 * n_max + n_special → 300 >= 256 + 3 (OK)
- the logits dimension is the full V, so chance = 1/V

## Cell membership (provisional, results unseen)

- (d_mem=16, n_pairs=16) is a capacity-boundary cell, not a saturated low-load cell.
- The Day 4–5 pilot (5 seeds) checks whether the Kaczmarz–L2 difference persists. The decision rule is unchanged; only cell membership is variable.
- This provisional membership is fixed before the E1.1 production runs.

## E1.1 test family (frozen before run)

Notation:
- `K` = keynorm Kaczmarz (Stage 1 frozen variant).
- `L` = L2-const β=0.125.
- `acc(arm, n, d)` = accuracy at 1500 steps, Q=2000, V=300.
- Δ is paired by seed.

### C1′-NI (primary, part of Holm family=4)

- `n_pairs=32`: H0: acc(K,32,d) − acc(L,32,d) ≤ −0.02, rejected when the one-sided 95% lower bound > −0.02.
- `n_pairs=64`: same.
- d_mem is evaluated per cell; the primary test averages the paired Δ over d_mem per seed.
- 2 tests.

### C2′ (primary, part of Holm family=4)

- d-difference: `D_arm(n) = acc(arm,n,d=16) − acc(arm,n,d=128)`.
- `C2′(n) = D_L(n) − D_K(n)`, one-sided 95% lower bound > 0 (prediction: L2 is more sensitive to d_mem).
- `n_pairs=32`, `64`: 2 tests.

### Holm

- Family = 4 (C1′-NI ×2 + C2′ ×2), α=0.05.

### C1′-EQ (family-external)

- `n_pairs=32`, `64`: K vs L TOST, δ=0.02 (90% CI within ±0.02).
- Not in the Holm family. Reported as equivalence evidence.

### F1 (capacity retention at d=128, guard-gated)

- `F1 = acc(K, n_pairs=128, d_mem=128) / acc(K, n_pairs=16, d_mem=128)`.
- Pass: `F1 ≥ 0.9`.
- Guard: `acc(K, n_pairs=16, d_mem=128) ≥ max(5/V, 0.10) = 0.10`.
- If the guard fails: `F1 = n/a` (not tested, not counted as pass/fail).
- The L2 side of the same ratio is reported descriptively.

### collapse / lock-in

- `collapse = acc ≤ k × chance`, `chance = 1/300`.
- **Collapse k freeze rule (2026-10-20, before pilot 40/40)**: primary analysis uses **k=5** (threshold = 5/300 = 0.0167). This is the closest pre-specified candidate to the paper's complete-collapse threshold 0.02, and 5 × chance. Sensitivity analyses use k=2, 10, 20, 50.
- k is not selected by looking at pilot collapse counts. It is fixed after the 14/40 partial summary, but the basis is the 0.02 threshold and the SE pre-calculation, not the partial-data counts. This timing is recorded in `master_log.md`.
- `Q=2000`, `V=300`.

## n_test definition and adjustment rule (frozen 2026-10-20 before pilot_summary)

- **n_test** = the number of paired seeds/runs per C1′-NI cell. It is not `n_pairs` (32/64), not the evaluation query count Q, and not a bootstrap count.
- C1′-NI is evaluated at `n_pairs=32` and `n_pairs=64`; each cell uses n_test paired seeds.
- `Q=2000` is the evaluation query count per run and is fixed. Bootstrap is sensitivity only.
- **SD_adj** = the maximum, across the four pilot cells, of the SD of the paired difference `d = acc_K − acc_L`. The pilot does not contain n_pairs=32/64, so this is a conservative proxy. **The mean difference K−L is not used.**
- Adjustment rule (determined by SD_adj only):
  - `SD_adj ≤ 0.05` → `n_test = 32`
  - `0.05 < SD_adj ≤ 0.10` → `n_test = 48`
  - `SD_adj > 0.10` → `n_test = 64`
- Cap: 64. If SD_adj exceeds this, n_test stays at 64.
- Bimodal cells: the primary n_test uses the overall SD. A stratified re-evaluation using within-stratum SD is reported as **exploratory only** and is not used in the primary analysis.
- `δ=0.02` is fixed. It is not changed by the n_test adjustment.
- E1.1's 320 runs are the base plan. C1′-NI cells expand according to n_test. The total run count is recomputed by Ken.
- **Freeze-timing note**: this rule was frozen after the pilot intermediate observation ((128,16) K-L difference and CI) was seen. The rule uses SD only, not the mean difference, but it is not a full pre-result freeze. This is recorded in `master_log.md`.

## Pilot SD decomposition / n adjustment (before E1.1 main)

- For every cell, report overall SD and SD decomposed by escape/non-escape strata.
- Provisional escape label: `acc ≥ 10 × chance = 0.0333`. Sensitivity: 5x / 20x / 0.10. The label is descriptive and is not a primary endpoint.
- Bimodal cells report both overall SD and stratum SDs. If SD is dominated by escape/non-escape, n may be adjusted within the pre-registered framework (numeric update). **δ is not changed.**
- Robust SD (MAD) is reported as sensitivity.
- Stratification is exploratory and is not used for hypothesis tests.

## Planned statistics

- paired Δ + two-sided 95% CI
- non-inferiority one-sided 95% (C1′-NI)
- TOST (90% CI, C1′-EQ)
- RULER 3-column output (TOST90 / non-inferiority one-sided / two-sided 95%)
- Brown–Forsythe / Fisher / Holm (Holm family=4)
- lock-in rate, collapse decision
- F1 is `n/a` when the guard fails

## Day 2 implementation note (results unseen)

- The standalone Kaczmarz projection passes T1–T5 (raw k, β=η/(‖k‖²+ε), ε=1e-6, η=1.0).
- However, the 1500-step smoke at V=300, d_mem=16, n_pairs=16 left the exact projection arm near chance (0.0045). Ablation: the same exact arm reaches 0.7579 at V=64, n_pairs=8 and 0.3770 at V=64, n_pairs=16. The failure is therefore specific to the V=300 combination, not key_norm=False alone.
- Stage 1's implementation arm is key-norm-only Kaczmarz (normalized k, β=1/‖k_raw‖²), which reaches 0.4035 in the same cell.
- This selection was fixed at the implementation level before the E1.1 runs. The H1 primary comparison (Kaczmarz vs L2) is conducted with the key-norm-only arm.
