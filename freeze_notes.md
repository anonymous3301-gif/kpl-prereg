# Freeze notes

This file records what was frozen when, and what was **not** a full
pre-result freeze. It is intentionally honest about timing.

## 1. Freeze dates

Substantive freeze date: **2026-10-20**.

| Document | Version / status | File last modified (local) |
|---|---|---|
| `prereg_H1_load_ratio.md` | frozen | 2026-10-20 15:37 |
| `prereg_H2_path_dependence.md` | v0.5.1, frozen | 2026-10-20 13:14 |
| `prereg_H3_eta_constant.md` | frozen | 2026-10-20 10:31 |
| `prereg_H4_beta_clamp.md` | frozen | 2026-10-20 11:13 |
| `n_test_rules.md` | frozen | 2026-10-20 |

The file times are local modification times; the substantive freeze date is
2026-10-20.

## 2. n_test rule: freeze timing

The n_test adjustment rule was frozen **after** the pilot intermediate
observation was seen. The observation included the (128,16) K-L difference and
its CI.

- The rule uses **SD only** (`SD_adj` = maximum paired-difference SD across the
  four pilot cells).
- The mean difference `K-L` is **not** used to choose n_test.
- `SD_adj` is the maximum across pilot cells, which is conservative.
- Residual results-dependent bias is small, but this is **not** a full
  pre-result freeze. This is recorded here rather than hidden.

## 3. Escape label

- Primary label: `escape = acc >= 10 * chance = 0.0333`.
- Sensitivity labels: 5x chance, 20x chance, and 0.10.
- The escape label is descriptive. It is not a primary endpoint and does not
  replace the collapse threshold `k * chance`.

## 4. H2 v0.5 -> v0.5.1 correction history

- v0.5 incorrectly demoted **H2-resource (A vs B')** from a primary contrast to
  exploratory. The error was a conflation of:
  - (a) "the D3 n=1 observation is not a primary data point" (correct), and
  - (b) "the A vs B' contrast itself is exploratory" (incorrect).
- v0.5.1 restores **two primary contrasts**, Holm m=2:
  - H2-order: `Delta_C - Delta_A`.
  - H2-resource: `Delta_B' - Delta_A`.
- D3/C/B' n=1 results remain exploratory and are not primary data points. The
  5-seed H2 run tests H2-resource.
- The pre-specified interpretation rules (resource non-significant/order
  significant, resource significant with A>B', resource significant with A<B',
  both non-significant) are in `prereg_H2_path_dependence.md`.

## 5. External termination and restart

- An external termination caused an approximately **1.5 h** loss of compute.
- The determinism check gave `Delta = 0` for both arms, so the restart does not
  introduce a rerun-selection bias.
- This is recorded as an operational event, not as a change to any hypothesis
  or decision rule.

## 6. Publication status

- GitHub repository: `kpl-prereg` (this repository).
- Zenodo DOI: pending release `v1.0-prereg`.
- OSF embargo: **omitted by decision**. Zenodo DOI is the freeze evidence;
  OSF is optional and may be added later, but a later OSF timestamp would not
  replace the Zenodo DOI timestamp.
- Real names are not published in this GitHub repository. If OSF is added
  later, it is the only place where real-name registration is planned.
