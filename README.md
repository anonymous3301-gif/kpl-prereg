# KPL Preregistration

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.23492899.svg)](https://doi.org/10.5281/zenodo.23492899)

This repository is the **frozen preregistration record** for the KPL
(Kaczmarz Projection Layer) project. It contains the H1-H4 preregistrations,
the n_test adjustment rules, and the freeze notes.

It is **not** the code repository. The experiments are run separately; this
repository exists to timestamp the hypotheses, decision rules, and known
limitations before the confirmatory runs.

## Contents

| File | Content |
|---|---|
| `prereg_H1_load_ratio.md` | H1 load-ratio preregistration, E1.1 test family, n_test rules |
| `prereg_H2_path_dependence.md` | H2 path-dependence preregistration (v0.5.1) |
| `prereg_H3_eta_constant.md` | H3 eta-constant preregistration |
| `prereg_H4_beta_clamp.md` | H4 beta-clamp preregistration |
| `n_test_rules.md` | n_pairs vs n_test, SD_adj, delta, cap, bimodal rule |
| `freeze_notes.md` | What was frozen when, and what was not a full pre-result freeze |

## Freeze status

- Substantive freeze date: **2026-10-20**.
- H2 is **v0.5.1**: two primary contrasts (H2-order and H2-resource), Holm m=2.
- The n_test rule was frozen **after** the pilot intermediate observation was
  seen. The rule uses SD only, not the mean difference. This is recorded
  openly in `freeze_notes.md`; it is not a full pre-result freeze.
- OSF embargo is omitted by decision. Zenodo DOI is the freeze evidence; OSF
  is optional and may be added later.

## License

CC-BY-4.0. See `LICENSE`.
