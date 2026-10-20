# n_test rules (frozen 2026-10-20)

## Terminology

| Term | Meaning |
|---|---|
| `n_pairs` | MQAR task level (32 or 64 in C1'-NI). This is **not** a sample size. |
| `n_test` | Number of paired seeds/runs per C1'-NI cell. This **is** the test sample size. |
| `Q` | Evaluation queries per run. Fixed at 2000. |
| bootstrap | Sensitivity analysis only. Never the primary test. |

## SD_adj

`SD_adj` is the **maximum** across the four pilot cells of the SD of the paired
difference `d = acc_K - acc_L`.

- Only the SD is used. The mean difference is **not** used.
- The pilot does not contain n_pairs=32/64, so the maximum is a conservative
  proxy.
- The pilot cells are (d_mem, n_pairs) = (16,16), (16,128), (128,16), (128,128).

## Adjustment rule

| SD_adj | n_test |
|---:|---:|
| <= 0.05 | 32 |
| 0.05 < SD_adj <= 0.10 | 48 |
| > 0.10 | 64 |

- Cap: **64**. If SD_adj exceeds this, n_test stays at 64.
- `delta = 0.02` is fixed. It is not changed by the n_test adjustment.
- The C1'-NI test is a paired test over n_test seeds/runs, at `n_pairs=32` and
  `n_pairs=64`.

## Bimodal cells

- Escape label (primary): `acc >= 10 * chance = 0.0333`.
- Sensitivity labels: 5x chance, 20x chance, and 0.10.
- If a cell is bimodal and the pooled within-stratum SD is less than 50% of the
  overall SD, the primary n_test still uses the overall-SD rule. A stratified
  re-evaluation is reported as **exploratory only** and is not used in the
  primary analysis.

## Freeze timing

The rule was frozen after the pilot intermediate observation ((128,16) K-L
difference and CI) had been seen. The rule uses SD only and is conservative,
but this is **not** a full pre-result freeze. See `freeze_notes.md`.

## Run-count implications

- 8 C1'-NI cells x 2 arms x 64 = 1024 runs (C1'-NI subset only).
- If the other 8 cells stay at 10 seeds, the full E1.1 is 1024 + 160 = 1184 runs.
- If all 16 cells expand to 64, the full E1.1 is 2048 runs.
- The total run count and ETA must state which of these is used.
