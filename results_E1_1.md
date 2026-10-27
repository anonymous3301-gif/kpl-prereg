# E1.1 Results (appended for v1.1-prereg)

**Date of completion:** 2026-10-25
**Total runs:** 1184 (16 cells x 2 arms, n_test=64 for C1'-NI cells, 10 seeds otherwise)
**Determinism:** Delta=0 for both arms (keynorm acc=0.4035, L2 const acc=0.1030 at 1500 steps, seed 0)

## G1 verdict: PASS

### C1'-NI (non-inferiority, K vs L)
- n_pairs=32: mean diff = +0.3472, lower95 = +0.3274, p = 3.88e-40 (reject H0)
- n_pairs=64: mean diff = +0.2365, lower95 = +0.2228, p = 2.38e-40 (reject H0)
- Interpretation: Kaczmarz is non-inferior to L2-const; in fact superior.

### C2' (d_mem sensitivity, K vs L)
- n=32: mean = +0.4373, lower95 = +0.4101, p = 1.76e-36 (reject)
- n=64: mean = +0.3897, lower95 = +0.3627, p = 8e-34 (reject)
- Interpretation: Kaczmarz is less sensitive to d_mem than L2-const.

### Holm m=4
- All four tests (C1'-NI x2 + C2' x2) reject after Holm correction.
- **G1 PASS.**

### C1'-EQ (equivalence, TOST, exploratory, not in Holm family)
- FAIL: Kaczmarz is superior to L2-const, not equivalent. K-L gap is positive and significant.

### F1 (capacity retention, guard-gated)
- Guard passes (K guard = 0.9824 >= 0.10, L guard = 0.5933 >= 0.10).
- F1 fails: K ratio = 0.3613, L ratio = 0.2321 (both < 0.9).
- Interpretation: high-load capacity is not retained. Limitation.

### Collapse (k=5, threshold = 5/300 = 0.01667)
- k=5: 293/1184 = 24.7%.
- Sensitivity: k=2: 18.8%, k=10: 30.2%, k=20: 35.1%, k=50: 45.8%.

### SD_adj
- E1.1 max paired SD over n_test cells = 0.2165 (higher than pilot 0.1368).
- n_test = 64 (frozen from pilot, unchanged).

### Ignition
- 402/1184 runs ignited (grad_norm >= 1.0), median step = 1000.

## Interpretation

- G1 PASS: Kaczmarz is non-inferior (and superior) to L2-const, and less sensitive to d_mem.
- F1 fail and C1'-EQ fail are limitations, not G1 conditions.
- Collapse 24.7% is a limitation (threshold sensitivity reported).
- mu-tilde unification is supported in direction (C2' + C1'-NI) but not fully demonstrated (beta-sweep required).

## Related experiments (completed)

- H2 (path dependence, 30 runs): non-significant (Holm m=2). D3 n=1 did not reproduce.
- H4 (beta_min clamp, 30 runs): clamp degrades performance; pure keynorm is best.
- H3-sub (3000 steps, 20 runs): budget dependence supported; per-budget NI not established at n=5.
- beta-sweep (30 runs, Colab L4): beta=0.3 peak (0.826), Kaczmarz (0.982) not reached. mu-tilde alone insufficient.

## v1.1 status

- v1.0 DOI `10.5281/zenodo.23492899` remains frozen and unchanged.
- v1.1-prereg will receive a new DOI after this release.
- The preregistration hypotheses (H1-H4) were frozen on 2026-10-20 before E1.1 runs.
- Results added here are post-hoc documentation, not hypothesis changes.
