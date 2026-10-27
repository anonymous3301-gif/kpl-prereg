# Preregistration: H2 path dependence (E1.2)

**Fixed date**: 2026-10-20 (results unseen)
**Hypothesis ID**: H2
**Status**: v0.5.1. Two primary contrasts, Holm m=2. Correction of v0.5: A vs B' is **not** exploratory; it is the H2-resource primary contrast. D3 n=1 remains exploratory and is not a primary data point.

## Purpose

Two primary contrasts are tested across the three training paths A / B' / C:

- **H2-order**: Δ_C − Δ_A. Paths A and C have the same Kaczmarz 750 / const 750 exposure; only the order differs. Does order change the result?
- **H2-resource**: Δ_B' − Δ_A. The same total budget 1500 is spent as "const 750 + Kacz 750" or as "Kacz 1500". Is the Kacz step count a resource?

B' is **not** a reference arm; it is the contrast partner for H2-resource. A reference arm and a contrast partner are different roles.

## Design

- Base cell: V=300, n_pairs=16, n_dist=32, d_mem ∈ {16, 32}, seed ∈ {0,1,2,3,4}.
- Total steps: 1500 for every path.
- Paths:
  - **A**: const 0.125 750 → keynorm Kaczmarz 750
  - **B'**: keynorm Kaczmarz 1500
  - **C**: keynorm Kaczmarz 750 → const 0.125 750
- Learning rate and batch size are the same as E1.1.
- **no-write baseline**: memory writes disabled, state frozen at zero. Readout is always zero.

## Primary endpoints (two, Holm m=2)

For each seed, compute Δ_A = acc(A) − acc(no-write), Δ_B' = acc(B') − acc(no-write), Δ_C = acc(C) − acc(no-write).

- **H2-order**: two-sided 95% CI of Δ_C − Δ_A (paired by seed).
- **H2-resource**: two-sided 95% CI of Δ_B' − Δ_A (paired by seed).
- d_mem is pooled: for each seed, average the contrast over d_mem, then run a paired t-test over seeds.
- Both contrasts are primary and receive **Holm m=2** (the smaller p is compared to 0.025, the larger to 0.05).
- Unadjusted 95% CIs and Holm-adjusted p-values are both reported.
- Per-d_mem estimates and the d_mem×path interaction are reported descriptively.

## Pre-specified interpretation (fixed before results)

- **H2-resource non-significant ∧ H2-order significant** → "an order effect is present, but no resource effect is detected". Only H2-order is supported.
- **H2-resource significant (Δ_B' − Δ_A < 0, A > B')** → "Kaczmarz step count is not a resource". Supports the D3 direction.
- **H2-resource significant (Δ_B' − Δ_A > 0, A < B')** → "resource hypothesis supported". D3 (n=1) is an outlier.
- **H2-order significant (>0)** → Kacz-first is better. **significant (<0)** → const-first is better.
- **Both non-significant** → "neither order nor resource effect is detected".
- No equivalence margin δ is used. No equivalence claim is made.

## D3/C/B' n=1 observations (exploratory, not primary data points)

- D3 (A): acc=0.510, boundary E‖k‖²=10.91
- C: acc=0.062, E‖k‖²=15.04
- B': acc=0.4035, E‖k‖²=14.92
- The mechanism note that const reduces E‖k‖² by 27%, pushes β up, and enables escape is exploratory.
- **D3 alone is exploratory**. It is not a primary data point and is not included in the H2 primary analysis. If it reproduces across the 5-seed H2 run, it is treated as the H2-resource result.

## Diagnostics (exploratory, before H2 main)

- **D1**: 750 Kacz only. acc=0.002, loss 5.7010 / 5.7050 / 5.6985 (250/500/750 training steps).
- **D2**: 750 norm (normbeta β≈1.0) + 750 Kacz. Exploratory description of norm contribution. Does not block the pilot.
- **D3**: A path (750 const + 750 Kacz). acc=0.510. Justification for the 1500 design.
- Trajectory (seed 0, V=300, n=16, d_mem=16): 250:5.7022 / 500:5.7091 / 750:5.7004 / 1000:5.6952 / 1250:5.5533 (acc 0.0450) / 1500:3.8945 (acc 0.4035). **Ignition ~1100–1200, acceleration from 1250 onward.** Exploratory.

## 3000 design withdrawn

- D3 (A, 1500 step) reached acc=0.510, so ignition is established at 1500 and there is no reason to extend to 3000.
- The 3000 design (A/B'/C each 3000) is withdrawn.

## Confound note (P4'')

- **H2-order (Δ_C − Δ_A)**: paths A and C have the same Kaczmarz 750 / const 750 exposure; only the order differs. This is a clean comparison of exposure-matched order.
- **H2-resource (Δ_B' − Δ_A)**: all paths have 1500 total steps, but Kaczmarz exposure is B' 1500 / A 750. The effect of const priming and the effect of 750 additional Kacz steps cannot be fully separated. This is interpreted as a resource-allocation contrast.
- path × allocation factorial design is out of scope for H2 and is recorded as a limitation.
- If H2-order is non-significant, report "order dependence not detected". Do not write "path independent" (CI including 0 is not proof of no difference).

## Interpretation limits

- n=5 seeds. Paths limited to A/B'/C.
- D3/C/B' n=1 observations are exploratory and are separated from the 5-seed H2 primary analysis.
- "CI includes 0" is not proof of no difference.
- Extrapolation to real models is not claimed.
- The norm stage is excluded from the design because it does not learn at V=300/n=16 (normbeta β≈1.0, loss≈5.73). This exclusion is a design decision based on Day 2's known data, not on H2's results.

## Result fields (blank, frozen)

- Δ_A / Δ_B' / Δ_C:
- **H2-order**: Δ_C − Δ_A (95% CI), Holm-adjusted p
- **H2-resource**: Δ_B' − Δ_A (95% CI), Holm-adjusted p
- per d_mem:
- D3/C/B' n=1 (exploratory):
- Decision:
